## Install UHD (build from source)

[best doc i can find](https://kb.ettus.com/Building_and_Installing_the_USRP_Open-Source_Toolchain_(UHD_and_GNU_Radio)_on_Linux). 

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
git checkout v4.9.0.0
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

## Install Open5GS (build from source)

Follow every step in [official guide](https://open5gs.org/open5gs/docs/guide/02-building-open5gs-from-sources/) should do it.

## Install srsRAN (build from source)

Help ([this reddit post](https://stackoverflow.com/questions/33304828/when-trying-to-use-my-usrp-in-gnu-radio-i-get-a-no-devices-found-for)). 

## Configs

These two config combinations currently runs: 
```
# This example configuration outlines how to use the 'cells' subcommand. The base cell configuration is
# for all cells using the 'cell_cfg' subcommand, with the 'cell' subcommand being used to overwrite this
# base configuration for a single cell.

cu_cp:
  amf:
    addr: 192.168.20.1 # set to the IP address of AMF
    port: 38412
    bind_addr: 192.168.20.1
    supported_tracking_areas:
      - tac: 1
        plmn_list:
          - plmn: "00101"
            tai_slice_support_list:
              - sst: 1

ru_sdr:
  device_driver: uhd
  device_args: type=x4xx,num_recv_frames=64,num_send_frames=64
  sync: internal
  srate: 122.88
  tx_gain: 50
  rx_gain: 40

# Properties set in this section are inherited for all cells unless overridden inside the cells section.
cell_cfg:
  dl_arfcn: 632628
  band: 78
  channel_bandwidth_MHz: 20
  common_scs: 30
  plmn: "00101"
  tac: 1
  pci: 1

# This section is represented as an array of cells. For this example, a single cell is declared that overrides the PCI and channel bandwidth properties from the default values.
# cells:
#   - pci: 10
#     channel_bandwidth_MHz: 10

log:
  filename: /tmp/gnb.log
  all_level: warning

pcap:
  mac_enable: false
  mac_filename: /tmp/gnb_mac.pcap
  ngap_enable: false
  ngap_filename: /tmp/gnb_ngap.pcap
```

```
db_uri: mongodb://localhost/open5gs

logger:

test:
  serving:
    - plmn_id:
        mcc: 001 #999
        mnc: 01 #70

global:
  parameter:
#    no_nrf: true
#    no_scp: true
    no_sepp: true
#    no_amf: true
#    no_smf: true
#    no_upf: true
#    no_ausf: true
#    no_udm: true
#    no_pcf: true
#    no_nssf: true
#    no_bsf: true
#    no_udr: true
#    no_mme: true
#    no_sgwc: true
#    no_sgwu: true
#    no_pcrf: true
#    no_hss: true

mme:
  freeDiameter:
    identity: mme.localdomain
    realm: localdomain
    listen_on: 127.0.0.2
    no_fwd: true
    load_extension:
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dbg_msg_dumps.fdx
        conf: 0x8888
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_rfc5777.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_mip6i.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nasreq.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nas_mipv6.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca_3gpp/dict_dcca_3gpp.fdx
    connect:
      - identity: hss.localdomain
        address: 127.0.0.8

  s1ap:
    server:
      - address: 127.0.0.2
  gtpc:
    server:
      - address: 127.0.0.2
    client:
      sgwc:
        - address: 127.0.0.3
      smf:
        - address: 127.0.0.4
  metrics:
    server:
      - address: 127.0.0.2
        port: 9090
  gummei:
    - plmn_id:
        mcc: 999
        mnc: 70
      mme_gid: 2
      mme_code: 1
  tai:
    - plmn_id:
        mcc: 999
        mnc: 70
      tac: 1
  security:
    integrity_order : [ EIA2, EIA1, EIA0 ]
    ciphering_order : [ EEA0, EEA1, EEA2 ]
  network_name:
    full: Open5GS
  time:
    t3412:
      value: 540

sgwc:
  gtpc:
    server:
      - address: 127.0.0.3
  pfcp:
    server:
      - address: 127.0.0.3
    client:
      sgwu:
        - address: 127.0.0.6

smf:
  sbi:
    server:
      - address: 127.0.0.4
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777
  pfcp:
    server:
      - address: 127.0.0.4
    client:
      upf:
        - address: 127.0.0.7
  gtpc:
    server:
      - address: 127.0.0.4
  gtpu:
    server:
      - address: 127.0.0.4
  metrics:
    server:
      - address: 127.0.0.4
        port: 9090
  session:
    - subnet: 10.45.0.0/16
      gateway: 10.45.0.1
    - subnet: 2001:db8:cafe::/48
      gateway: 2001:db8:cafe::1
  dns:
    - 8.8.8.8
    - 8.8.4.4
    - 2001:4860:4860::8888
    - 2001:4860:4860::8844
  mtu: 1400
  freeDiameter:
    identity: smf.localdomain
    realm: localdomain
    listen_on: 127.0.0.4
    no_fwd: true
    load_extension:
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dbg_msg_dumps.fdx
        conf: 0x8888
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_rfc5777.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_mip6i.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nasreq.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nas_mipv6.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca_3gpp/dict_dcca_3gpp.fdx
    connect:
      - identity: pcrf.localdomain
        address: 127.0.0.9

amf:
  sbi:
    server:
      - address: 127.0.0.5
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777
  ngap:
    server:
      - address: 192.168.20.1 #127.0.0.5
      # - address: 10.10.0.5
  metrics:x
    server:
      - address: 127.0.0.5
        port: 9090
  guami:
    - plmn_id:
        mcc: 001 #999
        mnc: 01 #70
      amf_id:
        region: 2
        set: 1
  tai:
    - plmn_id:
        mcc: 001 #999
        mnc: 01 #70
      tac: 1
  plmn_support:
    - plmn_id:
        mcc: 001 #999
        mnc: 01 #70
      s_nssai:
        - sst: 1
  security:
    integrity_order : [ NIA2, NIA1, NIA0 ]
    ciphering_order : [ NEA0, NEA1, NEA2 ]
  network_name:
    full: Open5GS
  amf_name: open5gs-amf0
  time:
    t3512:
      value: 540     # 9 mintues * 60 = 540 seconds

sgwu:
  pfcp:
    server:
      - address: 127.0.0.6
  gtpu:
    server:
      - address: 127.0.0.6

upf:
  pfcp:
    server:
      - address: 127.0.0.7
  gtpu:
    server:
      - address: 127.0.0.7 # Jasmine: open5gs doc says i should use 10.11.0.7 but it's kinda crashes
  session:
    - subnet: 10.45.0.0/16
      gateway: 10.45.0.1
    - subnet: 2001:db8:cafe::/48
      gateway: 2001:db8:cafe::1
  metrics:
    server:
      - address: 127.0.0.7
        port: 9090

hss:
  freeDiameter:
    identity: hss.localdomain
    realm: localdomain
    listen_on: 127.0.0.8
    no_fwd: true
    load_extension:
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dbg_msg_dumps.fdx
        conf: 0x8888
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_rfc5777.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_mip6i.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nasreq.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nas_mipv6.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca_3gpp/dict_dcca_3gpp.fdx
    connect:
      - identity: mme.localdomain
        address: 127.0.0.2
pcrf:
  freeDiameter:
    identity: pcrf.localdomain
    realm: localdomain
    listen_on: 127.0.0.9
    no_fwd: true
    load_extension:
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dbg_msg_dumps.fdx
        conf: 0x8888
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_rfc5777.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_mip6i.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nasreq.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_nas_mipv6.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca.fdx
      - module: /home/lyspfan/5g-cell/open5gs/build/subprojects/freeDiameter/extensions/dict_dcca_3gpp/dict_dcca_3gpp.fdx
    connect:
      - identity: smf.localdomain
        address: 127.0.0.4

nrf:
  sbi:
    server:
      - address: 127.0.0.10
        port: 7777

scp:
  sbi:
    server:
      - address: 127.0.0.200
        port: 7777
    client:
      nrf:
        - uri: http://127.0.0.10:7777

ausf:
  sbi:
    server:
      - address: 127.0.0.11
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777

udm:
  hnet:
    - id: 1
      scheme: 1
      key: /home/lyspfan/5g-cell/open5gs/build/configs/open5gs/hnet/curve25519-1.key
    - id: 2
      scheme: 2
      key: /home/lyspfan/5g-cell/open5gs/build/configs/open5gs/hnet/secp256r1-2.key
  sbi:
    server:
      - address: 127.0.0.12
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777

pcf:
  sbi:
    server:
      - address: 127.0.0.13
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777
  metrics:
    server:
      - address: 127.0.0.13
        port: 9090

nssf:
  sbi:
    server:
      - address: 127.0.0.14
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777
      nsi:
        - uri: http://127.0.0.10:7777
          s_nssai:
            sst: 1
bsf:
  sbi:
    server:
      - address: 127.0.0.15
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777

udr:
  sbi:
    server:
      - address: 127.0.0.20
        port: 7777
    client:
      scp:
        - uri: http://127.0.0.200:7777
```