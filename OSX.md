# Instruction for installing OpenCV on OSX

* Open CV version: 3.4.1
* language: Python 3
* OS: OSX Sierra (10.12), and others


# Build OpenCV from source
 
 
Building OpenCV3 for Python3, based on 
http://www.pyimagesearch.com/2016/12/05/macos-install-opencv-3-and-python-3-5/ from pyimagesearch.com, whith some modifications. 

## Step 1: Install Xcode (SAME)


## Step 2. Install MacPort

Install MacPort that is compatible with your OS: https://www.macports.org/install.php



## Step 3: Install Python 3 using MacPort and numpy 

You can install Python3 using Macport using the following command.

```bash
sudo port install python36
```

And, then, activate 36 by:
```bash
sudo port select --set python3 python36
```


Install numpy
```
pip3 install numpy
```
This may take some time.


## Step 2: Install dependencies using MacPort
```bash
sudo port install cmake -y
sudo port install pkgconfig -y
sudo port install jpeg libpng openexr -y
sudo port install eigen3 tbb -y
sudo port install ffmpeg -y
```


## Step 3: Download the OpenCV source code
```bash
cd ~
mkdir src
cd src
wget -O opencv.zip https://github.com/opencv/opencv/archive/3.4.1.zip
unzip opencv.zip
```

It's recommended to include contrib package.
```bash
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/3.4.1.zip
unzip opencv_contrib.zip
```


## Step 4: Compile and Install OpenCV




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
    -D PYTHON3_LIBRARY=/opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/config-3.6m-darwin \
    -D PYTHON3_LIBRARIES=/opt/local/Library/Frameworks/Python.framework/Versions/3.6/bin \
    -D PYTHON3_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
    -D PYTHON3_PACKAGES_PATH=$(python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
    -D INSTALL_C_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D WITH_CUDA=OFF \    
    -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-3.4.1/modules ..
```

If things went smoothly, you should see python related log like this:
```
--   Python 3:
--     Interpreter:                 /opt/local/Library/Frameworks/Python.framework/Versions/3.6/bin/python3 (ver 3.6.3)
--     Libraries:                   /opt/local/Library/Frameworks/Python.framework/Versions/3.6/bin (ver 3.6.3)
--     numpy:                       /Users/yyoo/Library/Python/3.6/lib/python/site-packages/numpy/core/include (ver 1.13.1)
--     packages path:               /opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages
-- 
--   Python (for build):            /opt/local/Library/Frameworks/Python.framework/Versions/3.6/bin/python3
```



Now, let's build & install it.
```
make -j4
sudo make install
```


Due to some bugs, the name of the Python binding library is not correct.
During the installation, you will see log like this.
```
-- Installing: /opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/cv2.cpython-36m-darwin.so
```

Let's fix it with a symbolic link.
```bash
sudo ln -s /opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/cv2.cpython-36m-darwin.so /opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/cv2.so
```

Now, you're ready to use OpenCV from Python system-wide. 
Testing with Python 3.6: 
```
$ python3
Python 3.6.3 (default, Oct  5 2017, 23:34:28) 
[GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.42)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.3.0'
```


## Step 5: Setting OpenCV for a virtual environment (Python 3)

Next section is for adding access from a virtual environment.

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
ln -s /opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/cv2.so ~/cv3/lib/python3.6/site-packages/cv2.so
```

Result will look like this.
```
(cv3) comp:site-packages yyoo$ ls -al
total 16
drwxr-xr-x  10 yyoo  staff   340 Oct 12 17:29 .
drwxr-xr-x   3 yyoo  staff   102 Oct 12 17:27 ..
drwxr-xr-x   3 yyoo  staff   102 Oct 12 17:27 __pycache__
lrwxr-xr-x   1 yyoo  staff    94 Oct 12 17:29 cv2.so -> /opt/local/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/cv2.so
-rw-r--r--   1 yyoo  staff   126 Oct 12 17:27 easy_install.py
drwxr-xr-x  23 yyoo  staff   782 Oct 12 17:27 pip
drwxr-xr-x  10 yyoo  staff   340 Oct 12 17:27 pip-9.0.1.dist-info
drwxr-xr-x   6 yyoo  staff   204 Oct 12 17:27 pkg_resources
drwxr-xr-x  34 yyoo  staff  1156 Oct 12 17:27 setuptools
drwxr-xr-x  12 yyoo  staff   408 Oct 12 17:27 setuptools-28.8.0.dist-info

```

Don't forget to install numpy for a new venv
```bash
pip install numpy
```


Now, check that you can use cv2 INSIDE of the virtual environment.

```
(cv3) comp:~ yyoo$ python3
Python 3.6.3 (default, Oct  5 2017, 23:34:28) 
[GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.42)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.3.0'
```



# Tips

Use screen for time-consuming tasks!

```bash
sudo port install screen
```

