
## Install UHD

Install dependencies: 
```
sudo apt-get install autoconf automake build-essential ccache cmake cpufrequtils doxygen ethtool \
g++ git inetutils-tools libboost-all-dev libncurses5 libncurses5-dev libusb-1.0-0 libusb-1.0-0-dev \
libusb-dev python3-dev python3-mako python3-numpy python3-requests python3-scipy python3-setuptools \
python3-ruamel.yaml 
```

Clone repository and go to branch UHD-3.15.LTS
```
git clone https://github.com/EttusResearch/uhd.git
cd uhd
git branch -a
git switch remotes/origin/UHD-3.15.LTS
```

Generate Makefile with CMake: 
```
cd host
mkdir build
cd build
cmake ../  # or cmake -DCMAKE_FIND_ROOT_PATH=/usr ../
```

Make and install: 
```
make
make test # This step is optional
sudo make install
```




