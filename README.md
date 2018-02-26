# PiOpenCV
Instruction to install OpenCV on Raspberry Pi.
* Open CV version: 3.3.0
* target platform: Raspberry Pi 3B
* OS: Raspbian Stretch
* language: Python 3

Installing OpenCV on OSX is similar. See [OSX.md](OSX.md).


# 1. Easy way: install pre-compiled packages (**NEED UPDATE**)

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

**NOTE: NEEDS RE-BUILDING FOR RASPBIAN STRETCH!!! WAIT FOR A MOMENT.** 

1. Download the pre-compiled package: 
https://github.com/ys7yoo/PiOpenCV/releases/download/2017.10/opencv3-RPI-STR.deb

```bash
wget https://github.com/ys7yoo/PiOpenCV/releases/download/2017.10/opencv3-RPI-STR.deb
```

2. Install it.

```bash
sudo dpkg -i opencv3-RPI-STR.deb
```

3. Add symbolic links

```bash
sudo ln -s /usr/local/lib/python3.5/dist-packages/cv2.cpython-35m-arm-linux-gnueabihf.so /usr/local/lib/python3.5/dist-packages/cv2.so
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



# 2. Harder way: build OpenCV from source
 
based on the 
[Install guide: Raspberry Pi 3 + Raspbian Jessie + OpenCV 3](http://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/) from pyimagesearch.com

testing for Raspberry Pi 3 + Raspbian Stretch + OpenCV3 + Python 3


## Step 1: Remove Python 2.7 and install Python 3


Still, you may want to remove unused packeges to get more free space.

Let's remove python 2.7.
```bash
sudo apt purge python
sudo apt autoremove
```

```bash
sudo apt-get purge wolfram-engine
```

Install Python 3 and numpy
```bash
sudo apt install python3 python3-setuptools python3-dev python3-venv -y
```
```
pip3 install numpy
```
This takes some time.


## Step 2: Install dependencies
```bash
sudo apt-get install build-essential cmake pkg-config -y

sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev -y
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev -y
sudo apt-get install libxvidcore-dev libx264-dev -y
sudo apt-get install libgtk2.0-dev -y
sudo apt-get install libatlas-base-dev gfortran -y
```

## Step 3: Download the OpenCV source code
```bash
cd ~
mkdir src
cd src
wget -O opencv.zip https://github.com/opencv/opencv/archive/3.4.0.zip
unzip opencv.zip
```

It's recommended to include contrib package.
```bash
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/3.4.0.zip
unzip opencv_contrib.zip
```


## Step 4: Compile and Install OpenCV




Prepare configurations to build OpenCV from the source you downloaded.
```
cd opencv-3.4.0
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D BUILD_opencv_java=OFF \
    -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=ON \
    -D PYTHON_DEFAULT_EXECUTABLE=$(which python3) \
    -D INSTALL_C_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-3.3.0/modules ..
```

In the Python section of the log, you should see something like this: 
```
--   Python 3:
--     Interpreter:                 /usr/bin/python3 (ver 3.5.3)
--     Libraries:                   /usr/lib/arm-linux-gnueabihf/libpython3.5m.so (ver 3.5.3)
--     numpy:                       /home/yyoo/.local/lib/python3.5/site-packages/numpy/core/include (ver 1.13.1)
--     packages path:               lib/python3.5/dist-packages
-- 
--   Python (for build):            /usr/bin/python3
```


Now, let's build & install it.
```
make -j4
sudo make install
```

Due to some bugs, the name of the Python binding library is not correct.
During the installation, you will see log like this.
```bash
-- Installing: /usr/local/lib/python3.5/dist-packages/cv2.cpython-35m-arm-linux-gnueabihf.so
```

Let's fix it with a symbolic link.
```bash
sudo ln -s /usr/local/lib/python3.5/dist-packages/cv2.cpython-35m-arm-linux-gnueabihf.so /usr/local/lib/python3.5/dist-packages/cv2.so
```

*ON UBUNTU, it's in `/usr/lib/python3/dist-packages/cv2.so`.*


Now, you're ready to use OpenCV from Python system-wide. 
Testing with Python 3.5: 
```
$ python3
Python 3.5.3 (default, Jan 19 2017, 14:11:04) 
[GCC 6.3.0 20170124] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.0'
```

Testing with Python 3.4: 
```
$ python3
Python 3.4.2 (default, Oct 19 2014, 13:31:11) 
[GCC 4.9.1] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.0'
```








## Step 5: Setting OpenCV for a virtual environment (Python 3)

Next section is for adding access from a virtual environment.

Make sure that you have installed venv for Python 3.
```bash
sudo apt-get install python3-venv
```

Make a virtual environment for OpenCV3 with Python3
```bash
python3 -m venv ~/cv3
``` 

To activate the venv you made, execute
```bash
source ~/cv3/bin/activate
``` 


Let's make a symbolic like inside of your venv package folder. 
```bash
ln -s /usr/local/lib/python3.5/dist-packages/cv2.so ~/cv3/lib/python3.5/site-packages/cv2.so
```

*ON UBUNTU, you should run the following.*
```bash
ln -s /usr/lib/python3/dist-packages/cv2.so ~/cv3/lib/python3.5/site-packages/cv2.so
```
Here, '~/cv3' is the virtual environment directory




Result will look like this.
```bash
(cv3) yyoo@raspberrypi:~/cv3/lib/python3.5/site-packages $ ls -al
total 40
drwxr-xr-x  9 yyoo yyoo 4096 Sep 14 06:44 .
drwxr-xr-x  3 yyoo yyoo 4096 Sep 14 06:38 ..
lrwxrwxrwx  1 yyoo yyoo   45 Sep 14 06:44 cv2.so -> /usr/local/lib/python3.5/dist-packages/cv2.so
-rw-r--r--  1 yyoo yyoo  126 Sep 14 06:39 easy_install.py
drwxr-xr-x 11 yyoo yyoo 4096 Sep 14 06:39 pip
drwxr-xr-x  2 yyoo yyoo 4096 Sep 14 06:39 pip-9.0.1.dist-info
drwxr-xr-x  5 yyoo yyoo 4096 Sep 14 06:39 pkg_resources
drwxr-xr-x  2 yyoo yyoo 4096 Sep 14 06:39 pkg_resources-0.0.0.dist-info
drwxr-xr-x  2 yyoo yyoo 4096 Sep 14 06:39 __pycache__
drwxr-xr-x  5 yyoo yyoo 4096 Sep 14 06:39 setuptools
drwxr-xr-x  2 yyoo yyoo 4096 Sep 14 06:39 setuptools-32.3.1.dist-info
```

Don't forget to install numpy for a new venv
```bash
pip install numpy
```


Now, check that you can use cv2 INSIDE of the virtual environment.

```
(cv3) yyoo@raspberrypi:~/cv3/lib/python3.5/site-packages $ python3
Python 3.5.3 (default, Jan 19 2017, 14:11:04) 
[GCC 6.3.0 20170124] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.0'
```



# Tips

Use screen for time-consuming tasks!

```bash
sudo apt install screen
```

