#CV #GenAI 

# From U-Nets to Diffusion

![](../../figures/Generative%20AI%20with%20Diffusion%20Models.pdf)

- U-Nets are a type of convolutional neural network that was originally designed for medical imaging. For instance, we could feed the network an image of a heart and it could return a different picture highlighting potentially cancerous areas.

- Can we use this process to generate new images? Here's an idea: what if we add noise to our images, and then use a U-Net to separate the images from the noise. Could we then feed the model noise and have it create a recognizable image? Let's give it a try!


First, let's define the different components of our U-Net architecture. Primarily, the `DownBlock` and the `UpBlock`.
## 1.2 The U-Net Architecture
### 1.2.1 The Down Block

The `DownBlock` is a typical convolutional neural network. If you are new to PyTorch and are coming from a Keras/TensorFlow background, the below is more similar to the [functional API](https://keras.io/guides/functional_api/) instead of a [sequential model](https://keras.io/guides/sequential_model/). We will later be using [residual](https://stats.stackexchange.com/questions/321054/what-are-residual-connections-in-rnns) and skip connections. A sequential model does not have the flexibility to support these types of connections, but a functional model does.

In our `__init__` function below, we will assign our various neural network operations to class variables:
* [Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html) applies convolution to the input. The `in_ch` is the number of channels we are convolving over and `out_ch` is the number of output channels, which is the same as the number of kernel filters used for convolution. Typically in a U-Net architecture, the number of channels increase the further down we move in the model.
* [ReLU](https://pytorch.org/docs/stable/generated/torch.nn.ReLU.html) is the activation function for the convolution kernels.
* [BatchNorm2d](https://pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html) applies [batch normalization](https://towardsdatascience.com/batch-normalization-in-3-levels-of-understanding-14c2da90a338) to a layer of neurons. ReLu has no learnable parameters, so we can apply the same function to multiple layers and it will have the same effect as using multiple ReLu functions. Batch Normalization does have learnable parameters, and reusing this function can have unexpected effects.
* [MaxPool2D](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html) is what we'll use to reduce the size of our feature map as it moves down the network. It's possible to achieve this effect through convolution, but max pooling is commonly used with U-Nets.

In the `forward` method, we describe how are various functions should be applied to an input. So far, the operations are sequential in this order:
* `Conv2d`
* `BatchNorm2d`
* `ReLU`
* `Conv2d`
* `BatchNorm2d`
* `ReLU`
* `MaxPool2d`

``` python

class DownBlock(nn.Module):
    def __init__(self, in_ch, out_ch):
        kernel_size = 3
        stride = 1
        padding = 1

        super().__init__()
        layers = [
            nn.Conv2d(in_ch, out_ch, kernel_size, stride, padding),
            nn.BatchNorm2d(out_ch),
            nn.ReLU(),
            nn.Conv2d(out_ch, out_ch, kernel_size, stride, padding),
            nn.BatchNorm2d(out_ch),
            nn.ReLU(),
            nn.MaxPool2d(2)
        ]
        self.model = nn.Sequential(*layers)

    def forward(self, x):
        return self.model(x)

```

![](../../figures/Generative%20AI%20with%20Diffusion%20Models.png)
### 1.2.2 The Up Block

While the `DownBlock` reduces the size of our feature map, the `UpBlock` doubles it back up. This is accomplished with [ConvTranspose2d](https://pytorch.org/docs/stable/generated/torch.nn.ConvTranspose2d.html). We can use almost the same architecture as the `DownBlock`, but we will replace conv2 with convT. The tranpose's `stride` of 2 will cause the doubling with the right amount of `padding`.

Let's get some practice with the code block below. We've set up an example to test out this function by creating a test image of `1`s.

Another interesting difference: we will be multiplying the input channel by 2. This is to accommodate the skip connections. We will be concatenating the output of an `UpBlock`'s matching `DownBlock` with the `UpBlock`'s input.



If x is the size of the input feature map, the output size is:

`new_x = (x - 1) * stride + kernel_size - 2 * padding + out_padding`

If stride = 2 and out_padding = 1, then in order to double the size of the input feature map:

`kernel_size = 2 * padding + 1`

The operations are almost the same as before, but with two differences:
* `ConvTranspose2d` - Convolution Transpose instead of Convolution
* `BatchNorm2d`
* `ReLU`
* `Conv2d`
* `BatchNorm2d`
* `ReLU`
* ~~`MaxPool2d`~~ - Scaling up instead of down

``` python

class UpBlock(nn.Module):
    def __init__(self, in_ch, out_ch):
        # Convolution variables
        kernel_size = 3
        stride = 1
        padding = 1

        # Transpose variables
        strideT = 2
        out_paddingT = 1

        super().__init__()
        # 2 * in_chs for concatednated skip connection
        layers = [
            nn.ConvTranspose2d(2 * in_ch, out_ch, kernel_size, strideT, padding, out_paddingT),
            nn.BatchNorm2d(out_ch),
            nn.ReLU(),
            nn.Conv2d(out_ch, out_ch, kernel_size, stride, padding),
            nn.BatchNorm2d(out_ch),
            nn.ReLU()
        ]
        self.model = nn.Sequential(*layers)
    
    def forward(self, x, skip):
        x = torch.cat((x, skip), 1) # skip connection
        x = self.model(x)
        return x

```

### 1.2.3 A Full U-Net

It's finally time to piece it together! Below, we have our full `UNet` model.

In the `__init__` function, we can define the number of channels at each step of the U-Net with `down_chs`. The current default is `(16, 32, 64)` meaning the current dimensions of the data as it moves through the model are:

* input: 1 x 16 x 16
* down0: 16 x 16 x 16
  * down1: 32 x 8 x 8
    * down2: 64 x 4 x 4
      * dense_emb: 1024
    * up0: 64 x 4 x 4
  * up1: 64 x 8 x 8
* up2: 32 x 16 x 16
* out: 1 x 16 x 16

The `forward` class method is where we will finally add our skip connections. For each step down in the U-Net, we will keep track of the output of each `DownBlock`. Then, when we move through the `UpBlock`s, we will [concatenate](https://pytorch.org/docs/stable/generated/torch.cat.html) the output of the previous `UpBlock` with its corresponding `DownBlock`.



``` python

class UNet(nn.Module):
    def __init__(self):
        super().__init__()
        img_ch = IMG_CH
        down_chs = (16, 32, 64)
        up_chs = down_chs[::-1]  # Reverse of the down channels
        latent_image_size = IMG_SIZE // 4 # 2 ** (len(down_chs) - 1)

        # Inital convolution
        self.down0 = nn.Sequential(
            nn.Conv2d(img_ch, down_chs[0], 3, padding=1),
            nn.BatchNorm2d(down_chs[0]),
            nn.ReLU()
        )

        # Downsample
        self.down1 = DownBlock(down_chs[0], down_chs[1])
        self.down2 = DownBlock(down_chs[1], down_chs[2])
        self.to_vec = nn.Sequential(nn.Flatten(), nn.ReLU())
        
        # Embeddings
        self.dense_emb = nn.Sequential(
            nn.Linear(down_chs[2]*latent_image_size**2, down_chs[1]),
            nn.ReLU(),
            nn.Linear(down_chs[1], down_chs[1]),
            nn.ReLU(),
            nn.Linear(down_chs[1], down_chs[2]*latent_image_size**2),
            nn.ReLU()
        )
        
        # Upsample
        self.up0 = nn.Sequential(
            nn.Unflatten(1, (up_chs[0], latent_image_size, latent_image_size)),
            nn.Conv2d(up_chs[0], up_chs[0], 3, padding=1),
            nn.BatchNorm2d(up_chs[0]),
            nn.ReLU(),
        )
        self.up1 = UpBlock(up_chs[0], up_chs[1])
        self.up2 = UpBlock(up_chs[1], up_chs[2])

        # Match output channels
        self.out = nn.Sequential(
            nn.Conv2d(up_chs[-1], up_chs[-1], 3, 1, 1),
            nn.BatchNorm2d(up_chs[-1]),
            nn.ReLU(),
            nn.Conv2d(up_chs[-1], img_ch, 3, 1, 1),
        )

    def forward(self, x):
        down0 = self.down0(x)
        down1 = self.down1(down0)
        down2 = self.down2(down1)
        latent_vec = self.to_vec(down2)

        up0 = self.up0(latent_vec)
        up1 = self.up1(up0, down2)
        up2 = self.up2(up1, down1)
        return self.out(up2)

```

> [!NOTE]
> reverse list 
> ``` python
> down_chs = (16, 32, 64)
> up_chs = down_chs[::-1]
> >> (64, 32, 16)
### Add noise to images 

``` python

def add_noise(imgs):
    dev = imgs.device
    percent = .5 # Try changing from 0 to 1
    beta = torch.tensor(percent, device=dev)
    alpha = torch.tensor(1 - percent, device=dev)
    noise = torch.randn_like(imgs)
    return alpha * imgs + beta * noise

```
