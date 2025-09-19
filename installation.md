
## Install UHD

Install dependencies: 
```
sudo apt-get install autoconf automake build-essential ccache cmake cpufrequtils doxygen ethtool \
g++ git inetutils-tools libboost-all-dev libncurses5 libncurses5-dev libusb-1.0-0 libusb-1.0-0-dev \
libusb-dev python3-dev python3-mako python3-numpy python3-requests python3-scipy python3-setuptools \
python3-ruamel.yaml 
```

Clone repository and go to v4.8.0.0 (both version recommended on srsRAN are buggy) ([ref](https://pysdr.org/content/usrp.html)): 
```
git clone https://github.com/EttusResearch/uhd.git
cd uhd
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
make test # This step is optional
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

## Install Open5GS

Follow every step in official guide up until the `ogstun` part ([Ref](https://open5gs.org/open5gs/docs/guide/02-building-open5gs-from-sources/))

Then do:
```
cd misc/
touch netconf.sh
```

Paste [this](https://github.com/open5gs/open5gs/blob/main/misc/netconf.sh) into `netconf.sh`, then do:
```
chmod +x netconf.sh
sudo ./netconf.sh
```

