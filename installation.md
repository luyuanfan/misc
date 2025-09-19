
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

Note: when doing cmake, python version higher than 3.8 cannot be seen by cmake. A solution is to add `python3.12` to the list of versions on line 60 in `uhd/host/cmake/Modules/UHDPython.cmake`. 

Make and install: 
```
make -j $(nproc)
make test # This step is optional
sudo make install
```

Note: add `#include <cstdint>` to the imported libraries in `uhd/host/lib/usrp/dboard/rhodium/rhodium_constants.hpp`. 


