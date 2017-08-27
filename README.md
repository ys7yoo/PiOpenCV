# PiOpenCV
Instruction to install OpenCV from source on Raspberry Pi.
* target platform: Raspberry Pi 3B
* language: Python 3
* Open CV version: 3.3.0


# easy way: install pre-compiled packages

## Step 1: Install dependencies
```bash
sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev -y
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev -y
sudo apt-get install libxvidcore-dev libx264-dev -y
sudo apt-get install libgtk2.0-dev -y
sudo apt-get install libatlas-base-dev gfortran -y

sudo apt-get install python3-dev python3-venv -y
```

## Step 2. Download and install pre-built packages

Download the pre-compiled package: 
https://github.com/ys7yoo/PiOpenCV/releases/download/2017/opencv3-RPI.deb

```bash
wget https://github.com/ys7yoo/PiOpenCV/releases/download/2017/opencv3-RPI.deb
```

Install it.

```bash
sudo dpkg -i opencv3-RPI.deb
```


## Step 3: Testing 

```
$ python
Python 3.4.2 (default, Oct 19 2014, 13:31:11) 
[GCC 4.9.1] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.3.0'
```



# Harder way: build OpenCV from source
 
 based on the 
[Install guide: Raspberry Pi 3 + Raspbian Jessie + OpenCV 3](http://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/) from pyimagesearch.com


## Step 1: Expand filesystem

It's automatically done in new versions.

Still, you may want to remove wolfram-engine to get more free space.

```bash
sudo apt-get purge wolfram-engine
```

## Step 2: Install dependencies
```bash
sudo apt-get install build-essential cmake pkg-config

sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install libgtk2.0-dev
sudo apt-get install libatlas-base-dev gfortran

sudo apt-get install python3-dev python3-venv
```

## Step 3: Download the OpenCV source code
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


## Step 4: Setting Python (Python 3)

In Jessi, pip is already installed.

Make a virtual environment for OpenCV3 with Python3
```bash
python3 -m venv ~/cv3
``` 

With Python3, DO NOT install or use virtualenv or virtualenvwrapper.
Python 3 has built-in virtualenv!

See the [official document](https://docs.python.org/3/library/venv.html) for more information.

(Optional) If you want to upgrade your python 3 to the latest version (3.6.2),follow [this](https://gist.github.com/ys7yoo/93b1531d453eeb803fda30b5480c59c0).
IT TAKES LONG TIME!!!



## Step 5: Compile and Install OpenCV

Activate the venv you made in Step 4.
```bash
source ~/cv3/bin/activate
``` 

Install numpy: 
```
pip install numpy
```
This takes some time.

Prepare configurations to build OpenCV from the source you downloaded.
```
cd opencv-3.3.0
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D BUILD_opencv_java=OFF \
    -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=ON \
    -D PYTHON_DEFAULT_EXECUTABLE=$(which python3) \
    -D PYTHON3_EXECUTABLE=$(which python3) \
    -D PYTHON3_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
    -D PYTHON3_PACKAGES_PATH=$(python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
    -D INSTALL_C_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-3.3.0/modules ..
```

Now, let's build & install it.
```
make -j4
sudo make install
```

## Step 6: Finishing installation.

Ater completing installation, you will have a .so file in the sitepackage folder IN THE VIRTUAL ENVIONMENT.
For example, your find cv2.cpython-34m.so in ~/cv3/lib/python3.4/site-packages.
```
(cv3) yyoo@RPI3B:~/cv3/lib/python3.4/site-packages $ ls -al
total 4404
drwxr-xr-x 10 yyoo yyoo    4096 Aug 25 08:26 .
drwxr-xr-x  3 yyoo yyoo    4096 Aug 25 04:41 ..
-rw-r--r--  1 root root 4358992 Aug 25 07:44 cv2.cpython-34m.so
-rw-r--r--  1 yyoo yyoo     126 Aug 25 04:41 easy_install.py
drwxr-xr-x  3 yyoo yyoo    4096 Aug 25 04:41 _markerlib
drwxr-xr-x 17 yyoo yyoo    4096 Aug 25 04:54 numpy
drwxr-xr-x  2 yyoo yyoo    4096 Aug 25 04:54 numpy-1.13.1.egg-info
drwxr-xr-x  6 yyoo yyoo    4096 Aug 25 04:41 pip
drwxr-xr-x  2 yyoo yyoo    4096 Aug 25 04:41 pip-1.5.6.dist-info
-rw-r--r--  1 yyoo yyoo  101340 Aug 25 04:41 pkg_resources.py
drwxr-xr-x  2 yyoo yyoo    4096 Aug 25 04:41 __pycache__
drwxr-xr-x  5 yyoo yyoo    4096 Aug 25 04:41 setuptools
drwxr-xr-x  2 yyoo yyoo    4096 Aug 25 04:41 setuptools-5.5.1.dist-info
```

However, the filename should be cv2.so (due to some bugs).
Go to the folder and change the name.
```
sudo mv cv2.cpython-34m.so cv2.so
```

## Step 7: Testing 

```
$ python
Python 3.4.2 (default, Oct 19 2014, 13:31:11) 
[GCC 4.9.1] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.3.0'
```



# Tips

Use screen for time-consuming tasks!

```bash
sudo apt install screen
```

