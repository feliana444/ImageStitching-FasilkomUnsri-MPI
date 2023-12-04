# ImageStitching-FasilkomUnsri-MPI
Operating System yang digunakan adalah ubuntu server 22.04

## Topologi
![Topologi](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/263a5fa1-220e-46f2-8959-34ba6c33901f)

## 1. Install MPI
```sh
sudo apt-get install -y mpich-doc mpich
```
```sh
pip install mpi4py
```
output <br>
![MPI](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/0a4b646e-f73b-4060-9364-49c5f43f4f2a)
## 2. Konfigurasi
### 2.1 Konfigurasi /etc/hosts <br>
cek alamat ip komputer menggunakan perintah `ip a` `if config` `hostname -  I` <br>
**MASTER**
```sh
192.168.135.181 master
192.168.135.39 slave1
192.168.135.75 slave2
192.168.135.168 slave3
```
**SLAVE**
```sh
192.168.135.181 master
192.168.135.39 slave1
```
### 2.2 Konfigurasi SSH <br>
Lakukan pada semua slave
```sh
ssh-copy-id <nama user>@<slave>
```

## 3. Install numpy, imutils dan opencv
### 3.1 numpy
    pip install numpy
### 3.2 imutils
    pip install imutils
### 3.3 opencv
    pip install opencv-python


## 5. Copy Seluruh File ke dalam Slave
### 5.1 Copy File
```sh
scp image-Stiching/* mpiuser@slave1:/home/mpiuser/
scp image-Stiching/* mpiuser@slave2:/home/mpiuser/
scp image-Stiching/* mpiuser@slave3:/home/mpiuser/
```
### 5.2 Membuat Directory Baru Di Tiap Node <br>
**MASTER** <br>
![master]<img width="77" alt="1" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/ede18f3c-0faf-45d5-aac0-341a2791bfa1"><br>

## 6. Program
```sh
from imutils import paths
import numpy as np
import argparse
import imutils
import cv2

# construct the argument parser and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--images", type=str, required=True,
	help="path to input directory of images to stitch")
ap.add_argument("-o", "--output", type=str, required=True,
	help="path to the output image")
args = vars(ap.parse_args())

# grab the paths to the input images and initialize our images list
print("[INFO] loading images...")
imagePaths = sorted(list(paths.list_images(args["images"])))
images = []

# loop over the image paths, load each one, and add them to our
# images to stich list
for imagePath in imagePaths:
	image = cv2.imread(imagePath)
	images.append(image)

# initialize OpenCV's image sticher object and then perform the image
# stitching
print("[INFO] stitching images...")
stitcher = cv2.createStitcher() if imutils.is_cv3() else cv2.Stitcher_create()
(status, stitched) = stitcher.stitch(images)

# if the status is '0', then OpenCV successfully performed image
# stitching
if status == 0:
	# write the output stitched image to disk
	cv2.imwrite(args["output"], stitched)
```

## 7. Jalankan Image Stitching dengan MPI dan Multinode
### 7.1 Memasukan Semua File Ke dalam Folder MPI
<img width="190" alt="123" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/397c30af-60c7-4420-8018-316a9c5c02cf">
Disini Terlihat Terdapat 4 Directory Yang Berisi Hasil Dari Tiap Frame Yakni 1 2 3 4 video menjadi gambar dengan cara di frame
### 7.2 Waktu Running
    mpiexec -n <jumlah slave> -host master,slave1,slave2,slave3 python3 /home/mpiuser/MPI/image_stitching_simple.py -i /home/mpiuser/MPI/1 -o output1.png
    mpiexec -n <jumlah slave> -host master,slave1,slave2,slave3 python3 /home/mpiuser/MPI/image_stitching_simple.py -i /home/mpiuser/MPI/2 -o output2.png
    mpiexec -n <jumlah slave> -host master,slave1,slave2,slave3 python3 /home/mpiuser/MPI/image_stitching_simple.py -i /home/mpiuser/MPI/3 -o output3.png
    mpiexec -n <jumlah slave> -host master,slave1,slave2,slave3 python3 /home/mpiuser/MPI/image_stitching_simple.py -i /home/mpiuser/MPI/4 -o output4.png
<img width="464" alt="3" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/724e0984-1c0a-43b9-810c-89e121085475">
<img width="464" alt="4" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/fc323232-8fe3-46d4-9940-3c66033c4808">
<img width="467" alt="5" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/94e513d9-251c-4e6d-9d09-dd27a5759d77">
<img width="470" alt="6" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/13d9fdb8-aa9d-40d0-b13b-162da4940a6c">

### 7.4 Output
Output Dari Video 1
<img width="399" alt="1" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/3db845df-4101-422c-9be7-c050891eaa38">
Output Dari Video 2
<img width="304" alt="2" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/f7d5fa8f-b91c-4834-83cc-82fe508dd01d">
Output Dari VIdeo 3
![WhatsApp Image 2023-12-02 at 22 54 48](https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/cf4059ea-a8bf-42b5-b753-982631df2d23)
Output Dari Video 4
<img width="280" alt="44" src="https://github.com/Alzidan21/Image-Stitching-MPI/assets/105232288/27053c3e-4f92-4822-9802-e8489bdf1148">
