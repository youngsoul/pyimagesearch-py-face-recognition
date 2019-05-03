# PyImageSearch Raspberry Pi Face Recognition

[https://www.pyimagesearch.com/2018/06/25/raspberry-pi-face-recognition/](https://www.pyimagesearch.com/2018/06/25/raspberry-pi-face-recognition/)

Be prepared for many hours of compiling on the Raspberry Pi.  

## Setup

Setting up the necessary software on the Raspberry Pi took a very long time.  Hours.. many hours.

What I did was installed Python 3.6.6, OpenCV 4.0.1, dlib, face_recognition, numpy, scipy, scikit-image, various required Python modules.

### Raspbian Stretch Desktop

Start with the Raspbian Stretch Desktop image.

For the next few hours, you just ssh into the Rpi and run everything from you your laptop if you like.  Not until you actually do face recognition do you need to run on Raspberry Pi.


### Install Python 3.6.6 on Raspberry Pi

I recommend [this Gist](https://gist.github.com/dschep/24aa61672a2092246eaca2824400d37f)

but change the 3.6.5 to 3.6.6.  Here is the script that I used:

```text
# create file on RPI, copy contents to a .sh file and execute
# See Gist
# https://gist.github.com/dschep/24aa61672a2092246eaca2824400d37f

# setup
echo "Update RPI"
sudo apt-get -y update
sudo apt-get -y install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev

# install python 3.6.6
wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tar.xz
tar xf Python-3.6.6.tar.xz
cd Python-3.6.6
./configure
make
sudo make altinstall

sudo apt-get install python3-dev

# clean up
cd ..
sudo rm -r Python-3.6.6
rm Python-3.6.6.tar.xz
sudo apt-get -y --purge remove build-essential tk-dev
sudo apt-get -y --purge remove libncurses5-dev libncursesw5-dev libreadline6-dev
sudo apt-get -y --purge remove libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev
sudo apt-get -y --purge remove libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev
sudo apt-get -y autoremove
sudo apt-get clean


echo "execute: python3.6 --version"
echo "execute: python3.6 -m venv my_py366_env"

```

### Install OpenCV4 on Raspberry Pi

I highly recommend [this PyImageSearch Blog](https://www.pyimagesearch.com/2018/09/26/install-opencv-4-on-your-raspberry-pi/)

That blog installs 4.0.0 and the script I used below installs 4.0.1

```text
sudo apt-get update
sudo apt-get upgrade
sudo apt-get -y install screen
sudo apt-get -y install htop

# set CONF_SWAPSIZE=100
echo "CONF_SWAPSIZE=2048"
sudo nano /etc/dphys-swapfile

sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start

free -m

# set GPU mem to 128
echo "Set gpu_mem=128"
sudo nano /boot/config.txt

sudo apt-get -y install build-essential cmake pkg-config
sudo apt-get -y install libjpeg-dev libtiff-dev libjasper-dev libpng12-dev
sudo apt-get -y install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get -y install libxvidcore-dev libx264-dev
sudo apt-get -y install libgtk2.0-dev libgtk-3-dev
sudo apt-get -y install libatlas-base-dev gfortran

sudo apt-get -y install python3-dev

wget -O opencv.zip https://github.com/opencv/opencv/archive/4.0.1.zip

wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.0.1.zip

unzip opencv.zip
unzip opencv_contrib.zip
# yes you really need to rename the directories

mv opencv-4.0.1/ opencv
mv opencv_contrib-4.0.1/ opencv_contrib

# you have to make python3.6 env so it uses python3.6 for the build
python3.6 -m venv cv2_env
source cv2_env/bin/activate

# took a really long time
sudo pip3.6 install numpy

# took so long I gave up
# sudo pip3.6 install scipy

cd ~/opencv/
mkdir build
cd build

cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV3=ON \
    -D BUILD_TESTS=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF ..
    
make -j4

sudo make install
sudo ldconfig
sudo apt-get update

cd ~/opencv/build/lib/python3
mkdir ~/cv2lib
cp cv2.cpython-35m-arm-linux-gnueabihf.so ~/cv2lib
ln -s ~/cv2lib/cv2.cpython-36m-arm-linux-gnueabihf.so cv2_env/lib/python3.6/site-packages/cv2.so
#ln -s cv2.cpython-35m-arm-linux-gnueabihf.so /usr/local/lib/python3.6/dist-packages/cv2.so

# set CONF_SWAPSIZE=100
echo "CONF_SWAPSIZE=100"
sudo nano /etc/dphys-swapfile
sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start

cd ~
rm opencv.zip
rm opencv_contrib.zip

sudo rm -r opencv
sudo rm -r opencv_contrib

```

This will take many hours, and another blog recommended putting a fan on your Raspberry Pi.  I recommend doing that.  It got very hot.  If you have a heat sink - I would use that too.

## Install dlib and face_recognition

To get this project to work on the Raspberry Pi you will need *dlib* and *face_recognition*.

You also need scipy and sklearn-image - both of these also take a long time to compile on the Raspberry Pi.

[PyImageSearch 2018 Blog - Install dlib (the easy, complete guide)](https://www.pyimagesearch.com/2018/01/22/install-dlib-easy-complete-guide/)
[PyImageSearch 2017 Blog - How to install dlib](https://www.pyimagesearch.com/2017/03/27/how-to-install-dlib/)

I did a pip install, and waited forever, but Adrian also recommends building from source.  You can decide which is better for you.

Here is the script I followed:

```text
sudo nano /etc/dphys-swapfile

# update CONF_SWAPSIZE=1024

sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start

free -m

sudo apt-get update
sudo apt-get install build-essential cmake
sudo apt-get install libgtk-3-dev
sudo apt-get install libboost-all-dev

# SOURCE YOUR VIRTUAL ENV
source venv/bin/activate

pip install numpy
pip install scipy
pip install scikit-image

pip install dlib
pip install face_recognition

sudo nano /etc/dphys-swapfile

# update CONF_SWAPSIZE=100

sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start
```

## Backup your image NOW

You just spent hours getting that image just the way you need it.  I would recommend you backup that image so you can just reburn a new image with all of the software already installed.


## Ready for Raspberry Pi
After you have all of that installed, you can then encode_faces on your laptop to get the encodings pickle file, and then transfer that to your raspberry pi and run pi_face_recognition.py.

Keep in mind that you have to comment/uncomment one line of code:
```python

#vs = VideoStream(src=0).start()
vs = VideoStream(usePiCamera=True).start()
```

And enable the camera on your Pi.

You might also want to adjust the threshold in this line:

```python
		matches = face_recognition.compare_faces(data["encodings"],
			encoding, tolerance=0.46)
```

as I have done to 0.46 ( default value is 0.6 ) if you are finding too many false positives.

Good luck - its a super fun project once you get it running.

## Download to RaspberryPI

You will need to download the following files to the raspberry pi

- haarcascade_frontalface_default.xml

- pi_face_recognition.py

- pr_encodings.pkl

- pi_run.sh


