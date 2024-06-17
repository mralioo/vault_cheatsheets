

#### To upload file from computer to colab:
```
from google.colab import files
uploaded = files.upload()
```

#### Extract Google Drive zip from Google colab notebook
Mount GDrive:
```
from google.colab import drive
drive.mount('/content/gdrive')
```
Open the link -> copy authorization code -> paste that into the prompt and press "Enter"

Check GDrive access:
```
!ls "/content/gdrive/My Drive"
```
Unzip (q - quite!) file from GDrive:
```
!unzip -q "/content/gdrive/My Drive/dataset.zip"
```

