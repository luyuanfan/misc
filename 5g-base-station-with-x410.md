## Building UHD and gnuradio from source

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

To see if the radio is on, use the `uhd_fft` tool with another radio: 
```
sudo uhd_fft -a 192.168.20.2 -f 6326.28e6
```

## Connecting USRP device (x410)

x410 has to be connected through Ethernet cable. In that case, follow the "Configuring Ethernet" section in the [offical guide](https://kb.ettus.com/Building_and_Installing_the_USRP_Open-Source_Toolchain_(UHD_and_GNU_Radio)_on_Linux).

Setting IP addresses must be done manually because the host system and the USRP device are connected directly, and there is nothing (such as [DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)) that helps you to automatically assign IP addresses. Not having DHCP in between is actually preferred, because host and USRP talk via IP (hard coded in configuration files), so IP addresses should *not* be changing all the time. 

Ubuntu 24.04 does not seem to allow configuring host system's device address through GUI interface. Alternatively, you must add a configuration file on the Ethernet interface. ([tutorial](https://gal.vin/posts/2023/ubuntu-static-ip/))

When this is set up, the host system should be able to `ping` the USRP's address. If `ping`ing works, then `uhd_usrp_probe` would work as well. 


## Install Open5GS (build from source)

Follow every step in [official guide](https://open5gs.org/open5gs/docs/guide/02-building-open5gs-from-sources/).

Sometimes these are these kind of error messages:
```
[sock] ERROR: socket bind(2) [x.x.x.x]:xxxx failed (98:Address already in use)
```

It's likely because there is an previous NRF process from Open5GS already running there. This might happen because (1) in earlier runs, the processes listening on that port is still here and has not been shut down cleanly. So, two processes collides; (2) these NFs are run from different shells, and they are not considered the same one. When this happen, can simply do:
```
pkill -9 open5gs-*
```

To configure all the 5G core services at once, write all configuration in `build/configs/sample.yaml`
```
./build/configs/sample.yaml
```

Then simply run the file `open5gs/build/tests/app/5gc`: 
```
./build/tests/app/5gc
```

\[Reboot\] **Remember to set up TUN device (not persistent after rebooting)**:
```
sudo ip tuntap add name ogstun mode tun
sudo ip addr add 10.45.0.1/16 dev ogstun
sudo ip addr add 2001:db8:cafe::1/48 dev ogstun
sudo ip link set ogstun up
```

Also remember to add a route for the UE to have WAN connectivity: 
```
### Enable IPv4/IPv6 Forwarding
$ sudo sysctl -w net.ipv4.ip_forward=1
$ sudo sysctl -w net.ipv6.conf.all.forwarding=1

### Add NAT Rule
$ sudo iptables -t nat -A POSTROUTING -s 10.45.0.0/16 ! -o ogstun -j MASQUERADE
$ sudo ip6tables -t nat -A POSTROUTING -s 2001:db8:cafe::/48 ! -o ogstun -j MASQUERADE
```

Configure the firewall correctly. Some operating systems (Ubuntu) by default enable firewall rules to block traffic.
```
$ sudo ufw status
Status: active
$ sudo ufw disable
Firewall stopped and disabled on system startup
$ sudo ufw status
Status: inactive
```

See logs in `$INSTALL_PREFIX/var/log/open5gs/*.log`.

### Modifications and rebuilds

If only `.yaml` files are edited, no rebuild is necessary, but you should restart all relevant services (but I'll just restart everything): 
```
sudo systemctl restart open5gs-*
```

If any open5gs source files is edited, you do need to recompile (no need to run `meson build`): 
```
ninja -C build
```

However if a build is broken and you want to start fresh, do:
```
rm -rf build
meson build --prefix=`pwd`/install
ninja -C build
### check if the build is corrrect
cd build
meson test -v
```

### Debugging core services
- Can use GDB to debug (but I feel like that might be a after changing configuration file type of situation).
- Or use the `-d` flag.  

## Install srsRAN (build from source)

Follow every step in [official guide](https://docs.srsran.com/projects/project/en/latest/user_manuals/source/installation.html).

Run: 
```
sudo gnb -C configs/gnb_x410.yml
```

\[Reboot\] **Also remember to tune network buffers** (for more information, see [this description](https://docs.srsran.com/projects/project/en/latest/user_manuals/source/running.html)): 
```
sudo ./scripts/srsran_performance
```

## Debugging

```
/tmp/gnb.log
```