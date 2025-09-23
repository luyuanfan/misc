## Install UHD (build from source)

[This doc solves everything](https://kb.ettus.com/Building_and_Installing_the_USRP_Open-Source_Toolchain_(UHD_and_GNU_Radio)_on_Linux). 

Install dependencies: 
```
sudo apt-get install autoconf automake build-essential ccache cmake cpufrequtils doxygen ethtool \
g++ git inetutils-tools libboost-all-dev libncurses5 libncurses5-dev libusb-1.0-0 libusb-1.0-0-dev \
libusb-dev python3-dev python3-mako python3-numpy python3-requests python3-scipy python3-setuptools \
python3-ruamel.yaml 
```

Clone repository and go to v4.8.0.0 (this version is required specifically) ([ref](https://pysdr.org/content/usrp.html)):
```
git clone https://github.com/EttusResearch/uhd.git
cd uhd
git tag -l
git checkout v4.8.0.0
```

Generate Makefile with CMake:
```
cd host
mkdir build
cd build
cmake ../
```

Make and install: 
```
make -j $(nproc)
make test
sudo make install
```

Set up library path:
```
sudo ldconfig
```

Download FPGA (field-programmable gateway array) images:
```
sudo uhd_images_downloader
```

## Install Open5GS (build from source)

Follow every step in [official guide](https://open5gs.org/open5gs/docs/guide/02-building-open5gs-from-sources/).

Sometimes these are these kind of error messages:
```
[sock] ERROR: socket bind(2) [x.x.x.x]:xxxx failed (98:Address already in use)
```

It's likely because there is an previous NRF process from Open5GS already running there. When this happen, can simply:
```
sudo pkill -9 open5gs-*
```
This might happen because (1) in earlier runs, the processes listening on that port is still here and has not been shut down cleanly. So, two processes collides; (2) these NFs are run from different shells, and they are not considered the same one. 

To run all the 5G core services at once, write all the configurations you need in `open5gs/build/tests/app/5gc`, and then run: 
```
./build/tests/app/5gc
```

If any open5gs source files is edited, you must to recompile (no need to run `meson build`): 
```
ninja -C build
```

However if a build is broken and you want to start fresh, do:
```
rm -rf build
meson build --prefix=`pwd`/install
ninja -C build
```

## Install srsRAN (build from source)

Follow every step in [official guide](https://docs.srsran.com/projects/project/en/latest/user_manuals/source/installation.html).

[this reddit post](https://stackoverflow.com/questions/33304828/when-trying-to-use-my-usrp-in-gnu-radio-i-get-a-no-devices-found-for). 