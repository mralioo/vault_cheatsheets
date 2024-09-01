#python #gpu 

Link : [Course Detail | NVIDIA](https://learn.nvidia.com/courses/course-detail?course_id=course-v1:DLI+S-AC-10+V1)

Notebooks:  

![Introduction to CUDA Python with Numba](../../figures/Fundamentals%20of%20Accelerated%20Computing%20with%20CUDA%20Python.ipynb)
# _Introduction to CUDA Python with Numba_


> [!summary] 
> In this first section you will learn first how to use Numba to compile functions for the CPU, and will receive an introduction to the inner workings of the Numba compiler. 
> 
> You will then proceed to learn how to GPU accelerate element-wise NumPy array functions, along with some techniques for efficiently moving data between a CPU host and GPU device. 
> 
> By the end of the first session you will be able to GPU accelerate Python code that performs element-wise operations on NumPy arrays.


**[Numba](http://numba.pydata.org/)** is a just-in-time Python function compiler that exposes a simple interface for accelerating numerically-focused Python functions. Numba is a very attractive option for Python programmers wishing to GPU accelerate their applications without needing to write C/C++ code, especially for developers already performing computationally heavy operations on NumPy arrays. Numba can be used to accelerate Python functions for the CPU, as well as for NVIDIA GPUs.

here are some characteristics of Numba: 

- **function compiler**: Numba compiles Python functions, not entire applications, and not parts of functions. Numba does not replace your Python interpreter, but is just another Python module that can turn a function into a (usually) faster function.

- **type-specializing**: Numba speeds up your function by generating a specialized implementation for the specific data types you are using. Python functions are designed to operate on generic data types, which makes them very flexible, but also very slow. In practice, you only will call a function with a small number of argument types, so Numba will generate a fast implementation for each set of types.

- **just-in-time**: Numba translates functions when they are first called. This ensures the compiler knows what argument types you will be using. This also allows Numba to be used interactively in a Jupyter notebook just as easily as a traditional application.

- **numerically-focused**: Currently, Numba is focused on numerical data types, like `int`, `float`, and `complex`. There is very limited string processing support, and many string use cases are not going to work well on the GPU. To get best results with Numba, you will likely be using NumPy arrays.


## Aside: CUDA C/C++ vs. Numba vs. pyCUDA

By no means is Numba the only way to program with CUDA. By far the most common way to program in CUDA is with the CUDA C/C++ language extensions. With regards to Python, [pyCUDA](https://documen.tician.de/pycuda/) is, in addition to Numba, an alternative to GPU accelerating Python code. We will remained focused on Numba throughout this course, but a quick comparison of the three options just named is worth a mention before we get started, just for a little context.

**CUDA C/C++**:
- The most common, performant, and flexible way to utilize CUDA
- Accelerates C/C++ applications

**pyCUDA**:
- Exposes the entire CUDA C/C++ API
- Is the most performant CUDA option available for Python
- Requires writing C code in your Python, and in general, a lot of code modifications

**Numba**:
- Potentially less performant than pyCUDA
- Does not (yet?) expose the entire CUDA C/C++ API
- Still enables massive acceleration, often with very little code modification
- Allows developers the convenience of writing code directly in Python
- Also optimizes Python code for the CPU


## Compile for the CPU 

* The Numba compiler is typically enabled by applying a [**function decorator**](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Decorators) to a Python function. Decorators are function modifiers that transform the Python functions they decorate, using a very simple syntax. Here we will use Numba's CPU compilation decorator `@jit`

* in more details , when calling a function, the compiler is triggered and compiles a machine code implementation of the function for float inputs. Numba also saves the original Python implementation of the function in the `.py_func` attribute, so we can call the original Python code to make sure we get the same answer

*  [`%timeit` magic function](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) is used to measure the speed 
	* The `%timeit` magic runs the statement many times to get an accurate estimate of the run time. It also returns the best time by default, which is useful to reduce the probability that random background events affect your measurement
	* 668 ns ± 1.75 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)


## How Numba Works

Now that you've gotton your hands a little dirty using the Numba compiler, let's take a look at what is actually going on under the hood. The first time we called our Numba-wrapped `hypot` function, the following process was initiated:

![](../../figures/Fundamentals%20of%20Accelerated%20Computing%20with%20CUDA%20Python.png)


We can see the result of type inference by using the `.inspect_types()` method, which prints an annotated version of the source code

## Object and nopython Modes

- Numba cannot compile all Python code.  ====**Some functions don't have a Numba-translation**====, and some kinds of Python types can't be efficiently compiled at all (yet).  For example, Numba does not support dictionaries (as of this writing). Here let's try to compile some Python code that Numba does not yet know how to compile

* `@jit(nopython=True) `:  Object mode exists to enable other Numba functionality, but in many cases, you want Numba to tell you if type inference fails. You can force **nopython mode** (the other compilation mode) by passing the `nopython` argument to the decorator
	* e.g .
```
This error may have been caused by the following argument(s):
- argument 0: cannot determine Numba type of <class 'dict'>](<* e.g>)
```
	- Alternative to use njit decorator
```
	from numba import njit
		@njit
```


## Introduction to Numba for the GPU with NumPy Universal Functions (ufuncs)

- The most important thing to know about GPU programming as we get started is that ====GPU hardware is designed for _data parallelism_====. Maximum throughput is achieved when the GPU is computing the same operations on many different elements at once.

- NumPy Universal functions, which perform the same operation on every element in a NumPy array, are naturally data parallel, so they are a natural fit for GPU programming.

### NumPy Universal Functions (ufuncs)

NumPy has the concept of ====universal functions ("ufuncs"),==== which are functions that can take NumPy arrays of varying dimensions, or scalars, and operate on them element-by-element.

As an example we'll use the NumPy `add` ufunc to demonstrate the basic ufunc mechanism:
```python

import numpy as np

a = np.array([1, 2, 3, 4])
b = np.array([10, 20, 30, 40])

np.add(a, b) # Returns a new NumPy array resulting from adding every element in `a` to every element in `b`

```
- Broadcasting : The lower dimensional array will be replicated to match the dimensionality of the higher dimensional array

### Making ufuncs for the GPU

- With Numba you simply implement a scalar function to be performed on all the inputs, decorate it with `@vectorize`, and Numba will figure out the broadcast rules for you. For those of you familiar with [NumPy's `vectorize`](https://docs.scipy.org/doc/numpy-1.15.0/reference/generated/numpy.vectorize.html), Numba's `vectorize` decorator will be very familiar.
	- numpy.vectorize :  Generalized function class.
	Define a vectorized function which takes a nested sequence of objects or numpy arrays as inputs and returns an single or tuple of numpy array as output. The vectorized function evaluates _pyfunc_ over successive tuples of the input arrays like the python map function, except it uses the broadcasting rules of numpy.
	
	The data type of the output of _vectorized_ is determined by calling the function with the first element of the input. This can be avoided by specifying the _otypes_ argument.

* For GPU , we need to specify an **explicit type signature** and setting the `target` attribute
``` python
@vectorize(['int64(int64, int64)'], target='cuda') # Type signature and target are required for the GPU
def add_ufunc(x, y):
    return x + y
    
```

- [Types and signatures — Numba 0.52.0.dev0+274.g626b40e-py3.7-linux-x86_64.egg documentation (pydata.org)](https://numba.pydata.org/numba-doc/dev/reference/types.html)
- [Creating NumPy universal functions — Numba 0.52.0.dev0+274.g626b40e-py3.7-linux-x86_64.egg documentation (pydata.org)](https://numba.pydata.org/numba-doc/dev/user/vectorize.html)

or such a simple function call, a lot of things just happened! Numba just automatically:

- **Compiled a CUDA kernel** to execute the ufunc operation in parallel over all the input elements.
- Allocated **GPU memory** for the inputs and the output.
- Copied the input data to the GPU.
- Executed the **CUDA kernel (GPU function) with the correct kernel dimensions** given the input sizes.
- Copied the result back from the GPU to the CPU.
- Returned the result as a NumPy array on the host.

Compared to an implementation in C, the above is remarkably more concise.

You might be wondering how fast our simple example is on the GPU? Let's see: TOO SLOW , WHY ?!

#### kinds of problems are well-suited for GPU computing : 

- **Our inputs are too small**: the GPU achieves performance through parallelism, operating on thousands of values at once. Our test inputs have only 4 and 16 integers, respectively. We need a much larger array to even keep the GPU busy.

- **Our calculation is too simple**: Sending a calculation to the GPU involves quite a bit of overhead compared to calling a function on the CPU. If our calculation does not involve enough math operations (often called "arithmetic intensity"), then the GPU will spend most of its time waiting for data to move around.

- **We copy the data to and from the GPU**: While in some scenarios, paying the cost of copying data to and from the GPU can be worth it for a single function, often it will be preferred to to run several GPU operations in sequence. In those cases, it makes sense to send data to the GPU and keep it there until all of our processing is complete.

- **Our data types are larger than necessary**: Our example uses `int64` when we probably don't need it. Scalar code using data types that are 32 and 64-bit run basically the same speed on the CPU, and for integer types the difference may not be drastic, but 64-bit floating point data types may have a significant performance cost on the GPU, depending on the GPU type. Basic arithmetic on 64-bit floats can be anywhere from 2x (Pascal-architecture Tesla) to 24x (Maxwell-architecture GeForce) slower than 32-bit floats. If you are using more modern GPUs (Volta, Turing, Ampere), then this could be far less of a concern. NumPy defaults to 64-bit data types when creating arrays, so it is important to set the [`dtype`](https://docs.scipy.org/doc/numpy-1.14.0/reference/arrays.dtypes.html) attribute or use the [`ndarray.astype()`](https://docs.scipy.org/doc/numpy-1.15.0/reference/generated/numpy.ndarray.astype.html) method to pick 32-bit types when you need them.


Ufuncs that use special functions (`exp`, `sin`, `cos`, etc) on large data sets run especially well on the GPU.

## CUDA Device Functions

There are any number of functions however, that do not fit this description. To compile functions for the ====GPU that are **not** element wise====, vectorized functions, we use **`numba.cuda.jit`**.

``` python
from numba import cuda

@cuda.jit(device=True)
def foo():
	pass
```


> [!NOTE] 
> Note that the CUDA compiler aggressively inlines device functions, so there is generally no overhead for function calls


## Allowed Python on the GPU

Compared to Numba on the CPU (which is already limited), Numba on the GPU has more limitations.  Supported Python includes:

* `if`/`elif`/`else`
* `while` and `for` loops
* Basic math operators
* Selected functions from the `math` and `cmath` modules
* Tuples

See [the Numba manual](http://numba.pydata.org/numba-doc/latest/cuda/cudapysupported.html) for more details.

## Managing GPU Memory

[CUDA C++ Best Practices Guide (nvidia.com)](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html)

- **High Priority**: Minimize data transfer between the host and the device, even if it means running some kernels on the device that do not show performance gains when compared with running them on the host CPU.
* The way to do this is to create **CUDA Device Arrays** and pass them to our GPU functions. Device arrays will not be automatically transfered back to the host after processing, and can be reused as we wish on the device before ultimately, and only if necessary, sending them, or parts of them, back to the host.

``` python 

from numba import cuda

x_device = cuda.to_device(x) # numba.cuda module includes a function that will copy host data to the GPU and return a CUDA device array 

# we can create the output array with the [`numba.cuda.device_array()`](https://numba.pydata.org/numba-doc/dev/cuda-reference/memory.html#numba.cuda.device_array) function
out_device = cuda.device_array(shape=(n,), dtype=np.float32)  # does not initialize the contents, like np.empty()

# And then we can use a special `out` keyword argument to the ufunc to specify the output buffer:
add_ufunc(x_device, y_device, out=out_device)
```

> [!NOTE] 
> Be sure to benchmark your data transfers when exploring whether or not a trip to the GPU is worth it.
> 
> Also, Numba provides additional methods for managing device memory and data transfer, check out [the docs](https://numba.pydata.org/numba-doc/dev/cuda/memory.html) for full details.





# _Custom CUDA Kernels in Python with Numba_


> [!summary]
> In the second section you will expand your abilities to be able to launch arbitrary, not just element-wise, numerically focused functions in parallel on the GPU by writing custom CUDA kernels. In service of this goal you will learn about how NVIDIA GPUs execute code in parallel. 
> 
> Additionally, you will be exposed to several fundamental parallel programming techniques including how to coordinate the work of parallel threads, and how to address race conditions. You will also learn techniques for debugging code that executes on the GPU.
> 
> By the end of the second section you will be ready to GPU accelerate an incredible range of numerically focused functions on 1D data sets.

- ====Custom CUDA kernels====, in utilizing the CUDA programming model, require more work to implement than, for example, simply decorating a ufunc with `@vectorize`. However, they make possible parallel computing in places where **ufuncs** are just not able, and provide a flexibility that can lead to the highest level of performance.

- **Ufuncs** are fantastically elegant, and for any scalar operation that ought to be performed element wise on data, ufuncs are likely the right tool for the job.
- Writing custom CUDA kernels, while more challenging than writing GPU accelerated ufuncs, provides developers with tremendous flexibility for the types of functions they can send to run in parallel on the GPU.
-  similar to CUDA C/C++ 

## Introduction to CUDA Kernels

When programming in CUDA, developers write functions for the GPU called **====kernels====**, which are executed, or in CUDA parlance, **launched**, on the GPU's many cores in parallel **threads**. When kernels are launched, programmers use a special syntax, called an ====**execution configuration**==== (also called a launch configuration) to describe the parallel execution's configuration.

> [!NOTE]
> The following slides give a high level introduction to how CUDA kernels can be created to work on large datasets in parallel on the GPU device. Work through the slides and then you will begin writing and executing your own custom CUDA kernels, using the ideas presented in the slides.

![](../../figures/Fundamentals%20of%20Accelerated%20Computing%20with%20CUDA%20Python.pdf)


## A First CUDA Kernel

Let's start with a concrete, and very simple example by rewriting our addition function for 1D NumPy arrays. CUDA kernels are compiled using the `numba.cuda.jit` decorator. `numba.cuda.jit` is not to be confused with the `numba.jit` decorator you've already learned which optimizes functions **for the CPU**.

```python
from numba import cuda

# Note the use of an `out` array. CUDA kernels written with `@cuda.jit` do not return values,
# just like their C counterparts. Also, no explicit type signature is required with @cuda.jit
@cuda.jit
def add_kernel(x, y, out):
    
    # The actual values of the following CUDA-provided variables for thread and block indices,
    # like function parameters, are not known until the kernel is launched.
    
    # This calculation gives a unique thread index within the entire grid (see the slides above for more)
    idx = cuda.grid(1)          # 1 = one dimensional thread grid, returns a single value.
                                # This Numba-provided convenience function is equivalent to
                                # `cuda.threadIdx.x + cuda.blockIdx.x * cuda.blockDim.x`

    # This thread will do the work on the data element with the same index as its own
    # unique index within the grid.
    out[idx] = x[idx] + y[idx]

# Now excution 

import numpy as np

n = 4096
x = np.arange(n).astype(np.int32) # [0...4095] on the host
y = np.ones_like(x)               # [1...1] on the host

d_x = cuda.to_device(x) # Copy of x on the device
d_y = cuda.to_device(y) # Copy of y on the device
d_out = cuda.device_array_like(d_x) # Like np.array_like, but for device arrays

# Because of how we wrote the kernel above, we need to have a 1 thread to one data element mapping,
# therefore we define the number of threads in the grid (128*32) to equal n (4096). (equal to the data dim)
threads_per_block = 128
blocks_per_grid = 32


add_kernel[blocks_per_grid, threads_per_block](d_x, d_y, d_out)
cuda.synchronize()
print(d_out.copy_to_host()) # Should be [1...4096]


>>x = array([   0,    1,    2, ..., 4093, 4094, 4095], dtype=int32)
>>y = array([1, 1, 1, ..., 1, 1, 1], dtype=int32)
>>d_out = [   1    2    3 ... 4094 4095 4096]

```



![](../../figures/Fundamentals%20of%20Accelerated%20Computing%20with%20CUDA%20Python-1.png)


> [!NOTE]
> No return in the ufunc  written for the CUDA kernel
> 
> By **reducing the number of threads in the grid**, either by reducing the number of blocks, and/or reducing the number of threads per block, there are elements where work is left undone and thus we can see in the output that the elements toward the end of the `d_out` array did not have any values added to it. If you edited the execution configuration by reducing the number of threads per block, then in fact there are other elements through the `d_out` array that were not processed.
> 
> **Increasing the size of the grid** in fact creates issues with out of bounds memory access. This error will not show in your code presently, but later in this section you will learn how to expose this error using `cuda-memcheck` and debug it.
> 
> You might have expected that **removing the synchronization point** would have resulted in a print showing that no or less work had been done. This is a reasonable guess since without a synchronization point the CPU will work asynchronously while the GPU is processing. The detail to learn here is that memory copies carry implicit synchronization, making the call to `cuda.synchronize` above unnecessary


## An Aside on Hiding Latency and Execution Configuration Choices

### Streaming Multiprocessors (SMs)

- CUDA enabled NVIDIA GPUs consist of several [**Streaming Multiprocessors**](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#hardware-implementation), or **SMs** on a die, with attached DRAM. 

- SMs contain all required resources for the execution of kernel code including many CUDA cores. 

- When a kernel is launched, each block is assigned to a single SM, with potentially many blocks assigned to a single SM. 

- SMs partition blocks into further subdivisions of ====32 threads==== called **warps** and it is these warps which are given parallel instructions to execute.

When an instruction takes more than one clock cycle to complete (or in CUDA parlance, to **expire**) the SM can continue to do meaningful work *if it has additional warps that are ready to be issued new instructions.* 

	Because of very large register files on the SMs, there is no time penalty for an SM to change context between issuing instructions to one warp or another. 
	In short, the latency of operations can be hidden by SMs with other meaningful work so long as there is other work to be done.

> [!important] 
> **Therefore, of primary importance to utilizing the full potential of the GPU, and thereby writing performant accelerated applications, it is essential to give SMs the ability to hide latency by providing them with a sufficient number of warps which can be accomplished most simply by executing kernels with sufficiently large grid and block dimensions.**
> Deciding the very best size for the CUDA thread grid is a complex problem, and depends on both the algorithm and the specific GPU's [compute capability](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#compute-capabilities), but here are some very rough heuristics that we tend to follow and which can work well for getting started:
> - The size of a block should be a multiple of 32 threads (the size of a warp), with typical block sizes between 128 and 512 threads per block.
> - The size of the grid should ensure the full GPU is utilized where possible. Launching a grid where the number of blocks is 2x-4x the number of SMs on the GPU is a good starting place. Something in the range of 20 - 100 blocks is usually a good starting point.
> - The CUDA kernel launch overhead does increase with the number of blocks, so when the input size is very large we find it best not to launch a grid where the number of threads equals the number of input elements, which would result in a tremendous number of blocks. Instead we use a pattern to which we will now turn our attention for dealing with large inputs.

## Working on Largest Datasets with Grid Stride Loops

 - **grid stride loop** create flexible kernels where each thread is able to work on more than one data element, an essential technique for large datasets. 

![](../../figures/Fundamentals%20of%20Accelerated%20Computing%20with%20CUDA%20Python-1.pdf)


```python
from numba import cuda

@cuda.jit
def add_kernel(x, y, out):
    

    start = cuda.grid(1)
    
    # This calculation gives the total number of threads in the entire grid
    stride = cuda.gridsize(1)   # 1 = one dimensional thread grid, returns a single value.
                                # This Numba-provided convenience function is equivalent to
                                # `cuda.blockDim.x * cuda.gridDim.x`

    # This thread will start work at the data element index equal to that of its own
    # unique index in the grid, and then, will stride the number of threads in the grid each
    # iteration so long as it has not stepped out of the data's bounds. In this way, each
    # thread may work on more than one data element, and together, all threads will work on
    # every data element.
    for i in range(start, x.shape[0], stride): # iterate with stride
        # Assuming x and y inputs are same length
        out[i] = x[i] + y[i]

import numpy as np

n = 100000 # This is far more elements than threads in our grid
x = np.arange(n).astype(np.int32)
y = np.ones_like(x)

d_x = cuda.to_device(x)
d_y = cuda.to_device(y)
d_out = cuda.device_array_like(d_x)

threads_per_block = 128
blocks_per_grid = 30 

# there will be 26 = 100000 // (30*128) 'jump' 

add_kernel[blocks_per_grid, threads_per_block](d_x, d_y, d_out)
print(d_out.copy_to_host()) # Remember, memory copy carries implicit synchronization

>> X = array([    0,     1,     2, ..., 99997, 99998, 99999], dtype=int32)
>> d_out = [     1      2      3 ...  99998  99999 100000]


```


## Atomic Operations and Avoiding Race Conditions

- CUDA, like many general purpose parallel execution frameworks, makes it possible to have ====race conditions==== in your code.
- A race condition in CUDA arises when threads read to or write from a memory location that might be modified by another independent thread. Generally speaking, you need to worry about:

	 * read-after-write hazards: One thread is reading a memory location at the same time another thread might be writing to it.
	 * write-after-write hazards: Two threads are writing to the same memory location, and only one write will be visible when the kernel is complete.
 
- A common strategy to avoid both of these hazards is to organize your CUDA kernel algorithm such that each thread has ====exclusive responsibility for unique subsets of output array elements, and/or to never use the same array for both input and output in a single kernel call====. (Iterative algorithms can use a double-buffering strategy if needed, and switch input and output arrays on each iteration.)

However, there are many cases where different threads need to combine results. Consider something very simple, like: "every thread increments a global counter." Implementing this in your kernel requires each thread to:

1. Read the current value of a global counter.
2. Compute `counter + 1`.
3. Write that value back to global memory.

However, there is no guarantee that another thread has not changed the global counter between steps 1 and 3. To resolve this problem, CUDA provides ====**atomic operations** ====which will read, modify and update a memory location in one, indivisible step. Numba supports several of these functions, [described here](http://numba.pydata.org/numba-doc/dev/cuda/intrinsics.html#supported-atomic-operations).

Let's make our thread counter kernel:


```python
@cuda.jit

def thread_counter_race_condition(global_counter):

    global_counter[0] += 1  # This is bad

@cuda.jit

def thread_counter_safe(global_counter):

    cuda.atomic.add(global_counter, 0, 1)  # Safely add 1 to offset 0 in global_counter array

In [95]:

# This gets the wrong answer

global_counter = cuda.to_device(np.array([0], dtype=np.int32))

thread_counter_race_condition[64, 64](global_counter)

​

print('Should be %d:' % (64*64), global_counter.copy_to_host())

Should be 4096: [1]

In [96]:

# This works correctly

global_counter = cuda.to_device(np.array([0], dtype=np.int32))

thread_counter_safe[64, 64](global_counter)

​

print('Should be %d:' % (64*64), global_counter.copy_to_host())

Should be 4096: [4096]


```


![](../../figures/Fundamentals%20of%20Accelerated%20Computing%20with%20CUDA%20Python.gz)


### CUDA Memcheck

Another common error occurs when a CUDA kernel has an invalid memory access, typically caused by running off the end of an array.  The full CUDA toolkit from NVIDIA (not the `cudatoolkit` conda package) contain a utility called `cuda-memcheck` that can check for a wide range of memory access mistakes in CUDA code.


``` python
! cuda-memcheck python debug/ex3.py
```

The output of `cuda-memcheck` is clearly showing a problem with our histogram function:
```
========= Invalid __global__ write of size 4
=========     at 0x00000548 in cudapy::__main__::histogram$241(Array<float, int=1, C, mutable, aligned>, float, float, Array<int, int=1, C, mutable, aligned>)
```
But we don't know which line it is.  To get better error information, we can turn "debug" mode on when compiling the kernel, by changing the kernel to look like this:
``` python
@cuda.jit(debug=True)
def histogram(x, xmin, xmax, histogram_out):
    nbins = histogram_out.shape[0]
```
`cuda-memcheck` has different modes for detecting different kinds of problems (similar to `valgrind` for debugging CPU memory access errors). Take a look at the documentation for more information: [http://docs.nvidia.com/cuda/cuda-memcheck/](http://docs.nvidia.com/cuda/cuda-memcheck/)
# _Multidimensional Grids and Shared Memory for CUDA Python with Numba_


> [!summary] 
> In the third section you will begin working in parallel with 2D data, and will learn how to utilize an on-chip memory space on the GPU called shared memory.
> 
> By the end of the third section, you will be able to write GPU accelerated code in Python using Numba on 1D and 2D datasets while utilizing several of the most important optimization strategies for writing consistently fast GPU accelerated code.



