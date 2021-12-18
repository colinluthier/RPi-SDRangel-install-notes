# RPi-SDRangel-install-notes.

```sudo apt-get -y install libopencv-dev libxml2-dev libavcodec-dev libavformat-dev graphviz bison flex ffmpeg libopus-dev libfaad-dev

# Optional full QT5 install 
#git clone git://code.qt.io/qt/qt5.git
#cd qt5
#git checkout 5.12
#perl init-repository
#.configure
#make
#make install

# These are the required QT5 build libraries. 
sudo apt-get -y install qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools
sudo apt-get -y install libqt5websockets5-dev qtmultimedia5-dev qtpositioning5-dev libqt5charts5-dev libqt5serialport5-dev qtlocation5-dev libqt5texttospeech5-dev

sudo mkdir -p /opt/build
sudo chown pi:users /opt/build
sudo mkdir -p /opt/install
sudo chown pi:users /opt/install

mkdir -p ~/SDRangel

#FFTW3
cd ~/SDRangel
wget http://www.fftw.org/fftw-3.3.10.tar.gz
tar zxvf fftw-3.3.10.tar.gz
sudo mkdir/usr/local/fftw
cd fftw-3.3.10
./configure 
make
sudo make install

#FFTW3 pt2
make clean
./configure --enable-float 
make
sudo make install

#APT
#Aptdec is a FOSS program that decodes images transmitted by NOAA weather satellites.
cd ~/SDRangel
git clone https://github.com/srcejon/aptdec.git
cd aptdec
git checkout libaptdec
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/aptdec ..
make -j $(nproc) install

#CM265cc
cd ~/SDRangel
git clone https://github.com/f4exb/cm256cc.git
cd cm256cc
git reset --hard c0e92b92aca3d1d36c990b642b937c64d363c559
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/cm256cc ..
make -j $(nproc) install

#LibDAB
cd ~/SDRangel
git clone https://github.com/srcejon/dab-cmdline
cd dab-cmdline/library
git checkout msvc
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/libdab ..
make -j $(nproc) install

#MBElib
cd ~/SDRangel
git clone https://github.com/szechyjs/mbelib.git
cd mbelib
git reset --hard 9a04ed5c78176a9965f3d43f7aa1b1f5330e771f
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/mbelib ..
make -j $(nproc) install

#SerialDV
cd ~/SDRangel
git clone https://github.com/f4exb/serialDV.git
cd serialDV
git reset --hard "v1.1.4"
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/serialdv ..
make -j $(nproc) install

#DSDcc
cd ~/SDRangel
git clone https://github.com/f4exb/dsdcc.git
cd dsdcc
git reset --hard "v1.9.3"
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/dsdcc -DUSE_MBELIB=ON -DLIBMBE_INCLUDE_DIR=/opt/install/mbelib/include -DLIBMBE_LIBRARY=/opt/install/mbelib/lib/libmbe.so -DLIBSERIALDV_INCLUDE_DIR=/opt/install/serialdv/include/serialdv -DLIBSERIALDV_LIBRARY=/opt/install/serialdv/lib/libserialdv.so ..
make -j $(nproc) install

sudo apt-get install libsamplerate0-dev

#Codec2/FreeDV
# Codec2 is already installed from the packager, but this version is required for SDRangel.
cd ~/SDRangel
git clone https://github.com/drowe67/codec2.git
cd codec2
git reset --hard 76a20416d715ee06f8b36a9953506876689a3bd2
mkdir build_linux; cd build_linux
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/codec2 ..
make -j $(nproc) install

#SGP4
# python-sgp4 1.4-1 is available in the packager, installing this version just to be sure.
cd ~/SDRangel
git clone https://github.com/dnwrnr/sgp4.git
cd sgp4
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/sgp4 ..
make -j $(nproc) install

#json
cd ~/SDRangel
git clone https://github.com/nlohmann/json.git
cd json
md build
cd build
cmake -DJSON_MultipleHeaders=ON ../json
make
sudo make install

#LibSigMF
cd ~/SDRangel
git clone https://github.com/f4exb/libsigmf.git
cd libsigmf
git checkout "new-namespaces"
mkdir build; cd build
cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/opt/install/libsigmf .. 
make -j $(nproc) install

sudo ldconfig

cd ~/SDRangel
git clone https://github.com/f4exb/sdrangel.git
cd sdrangel
mkdir build; cd build
cmake -Wno-dev -DDEBUG_OUTPUT=ON -DRX_SAMPLE_24BIT=ON \
-DCMAKE_BUILD_TYPE=RelWithDebInfo \
-DHACKRF_DIR=/usr/local \
-DSOAPYSDR_DIR=/usr/local \
-DUHD_DIR=/usr/local \
-DAPT_DIR=/opt/install/aptdec \
-DCM256CC_DIR=/opt/install/cm256cc \
-DDSDCC_DIR=/opt/install/dsdcc \
-DSERIALDV_DIR=/opt/install/serialdv \
-DMBE_DIR=/opt/install/mbelib \
-DCODEC2_DIR=/opt/install/codec2 \
-DSGP4_DIR=/opt/install/sgp4 \
-DLIBSIGMF_DIR=/opt/install/libsigmf \
-DDAB_DIR=/opt/install/libdab \
-DCMAKE_INSTALL_PREFIX=/opt/install/sdrangel ..

make -j4 install
sudo ldconfig

cd ~/.config/
# If you have not run SDRangel yet, create the settings directory.
mkdir f4exb
cd f4exb
# Generate a new wisdom file for FFT sizes : 128, 256, 512, 1024, 2048, 4096, 8192, 16384 and 32768.
fftwf-wisdom -n -o fftw-wisdom 128 256 512 1024 2048 4096 8192 16384 32768

#Run SDRangel from gui terminal
/opt/install/sdrangel/bin/sdrangel --fftwf-wisdom ~/.config/f4exb/fftw-wisdom```
