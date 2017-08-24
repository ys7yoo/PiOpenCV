# PiOpenCV
Instruction to install OpenCV on Raspberry Pi.
* based on the 
[Install guide: Raspberry Pi 3 + Raspbian Jessie + OpenCV 3.](http://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/) from pyimagesearch.com
* target platform: Raspberry Pi 3B
* language: Python 3 (3.6.2)


# Steps

## Step #1: Expand filesystem

It's automatically done in new versions.

Still, you may want to remove wolfram-engine to get more free space.

```bash
sudo apt-get purge wolfram-engine
```

## Step #2: Install dependencies
```bash
sudo apt-get install build-essential cmake pkg-config

sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install libgtk2.0-dev
sudo apt-get install libatlas-base-dev gfortran

sudo apt-get install python3-dev
```

## Step #3: Download the OpenCV source code
```bash
cd ~
mkdir src
cd src
wget -O opencv.zip https://github.com/opencv/opencv/archive/3.3.0.zip
unzip opencv.zip
```

Optionally, install contrib package.
```bash
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/3.3.0.zip
unzip opencv_contrib.zip
```


## Step #4: Setting Python (Python 3)

In Jessi, pip is already installed.

With Python3, DO NOT install virtualenv.
Python 3 has built-in virtualenv!

You may also want to upgrade your python 3 to version 3.6.
For that, follow [this](https://gist.github.com/ys7yoo/93b1531d453eeb803fda30b5480c59c0).
