Notes -

- uhd is package (no source cuz it has bugs or version not stable)
- open5gs is package
- srsran is built from source

plug usrp
probe
run uhd
see log

uhd building from source is also being weird. so rn we use the 4.9.0 release. **but i believe the 4.0.0 or 3.1.5. are the ones compatible with srsran** (rn with 4.9.0 there's an error when building srsran but it can be fixed by adding a line which is sketchy ig) -- so def switch back to that later if curr setup works. 

open5gs build from source is also buggy. there is a open5gs vonr error that idk where it's from. trying with package (did not config logging, but that'll only affect journalctl prints ig so it's not a big deal; also webui is facing some troubles because srsran_proj does not have a release, i have to remove its ppa before **running(???)** the open5gs web page)

srsran ppa has some problems i think. will look into what it is but since we build from source it doesn't matter. srsran build from source goes alright so long as i add a line in radio/uhd/radio_uhd_tx_stream on line 57 that falls through the case of event_code_ok. might be because my uhd version is too new. 

install open5gs before srsran

to use `uhd_usrp_probe`, make sure to unplug and replug. 

and them you can run the gnb (with srsran) by passing in one of those yml files in the `configs` in the root folder.
- rn running `gnb` after making sure the device is there gives in error. it says 'connection refused cu-up failed to connect with AMF on `127.01.100:38412`. which is kinda bullshit bc open5gs asks me to configure amf-ngap on `10.10.05` instead of `127.01.100`. why is gnb trying to connect to that address?? 
- the guide said the only thing running on port 38412 should be amf-ngap on `127.0.0.5` so i'll try to change that. 
when creating subscriber it also needs a AMF field which defaults to `8000`, dnn defaults to `internet` 

so when trying to run `gnb` the reason it connects that way is that the config files in srsran project says to. according [this running open5gs page](https://open5gs.org/open5gs/docs/guide/01-quickstart/), must "- Make sure the PLMN and TAC of the eNB/gNB matches the settings in your MME/AMF". so this is defined in `srsRAN_Project/configs/b200smth` 

NOW: so now i've changed the srsran gnb config and can try to run again. 

something is not persistent after rebooting might have to do that (some image with uhd or smth) again?? well when i did probe it's fine it's there. 

when adding APNs on phone, turns out i can't use mcc/mnc with 001/01, but must use 999/70. 
- so i changed the the config in srsran/configs/b200
- also the `amf` and `nrf` and `upf` 

if NGAP and N2 and NG are the same thing, then my connection should be successful. 

seems like when an UE actually connects, you should see ([ref](https://github.com/srsran/srsRAN_Project/issues/706)) that `number of gNB-UE is now one 

so i think we can look at the logs located in `var/log/open5gs/` specifically `nrf` `upf` `amf`. 

---
## Flow
user equipment (phone + sim) → \[sends radio signal to] → base station/cell tower (gNB made by usrp + ghd as hardware driver + srsran) → goes into open5gs core → sets up WAN connectivity so user plane reaches the actual internet

---
## Open5GS
Overview - is a software that builds the authenticating and routing for a cellular network (the signal processing stuff are handled by srsRAN, described below); supports both 4G/LTE and 5G networks.

I think each `.yml` file in the root folder corresponds to each of these modules. 

![image](./images/open5gs.jpg)


### Core services
- Control plane: contains the authenticator, database (mongodb; for storing subscriber info), 
- User plane: does some data transmission stuff. 

```
NRF - NF Repository Function
SCP - Service Communication Proxy
SEPP - Security Edge Protection Proxy
AMF - Access and Mobility Management Function
SMF - Session Management Function
UPF - User Plane Function
AUSF - Authentication Server Function
UDM - Unified Data Management
UDR - Unified Data Repository
PCF - Policy and Charging Function
NSSF - Network Slice Selection Function
BSF - Binding Support Function
```

- NRF - registers and location of all other core services and does (IP address) lookups when one needs to communicate with another. 
- SCP - routes messages between core services if direct communication isn't possible.
- SEPP - secures communication between two different mobile networks.
- AMF - stores and does authentication, registration with subscribers (phones).
- SMF - set up connection session with internet when phone tries to connect to internet.
- UPF - does the actual data forwarding.
- AUSF - authenticates subscriber info, like checking if sim card info is valid. 
- UDM - not sure! 
- UDR - that's what mongodb does here i believe, it stores subscriber info. 
- PCF - not sure! 
- NSSF - assign the right slice of a network.
- BSF - not sure! 

### Mobile network & cellular network
are used interchangeably.
### 5G network slicing
from Verizon - allows multiple logical networks to be created on top of a common shared physical network. Essentially this means segmenting parts of the network for different users and/or use cases. For example, it can have (1) dedicated slices for self-driving cars to ensure reliable communication, (2) a slice for smart factories to coordinate autonomous forklifts safely, and (3) a slice for remote medical procedures requiring high bandwidth and low latency. So that each slice has different properties that suites the specific needs of a purpose. 
### 5G non standalone v.s. 5G standalone
former deploys 4G control plane infrastructures (like base stations) with higher data transfer rate; latter has its own dedicated hardware.
- read more [here](https://www.netscout.com/what-is/5g-sa-vs-nsa)
- the frequency band (range of radio frequency; bandwidth lol) used by 4g/5g is region specific, but 5g typically has a much larger bandwidth than 4g. 
### mme & amf
MME (mobility management entity; 4G) & AMF (authentication management field; 5G) - does authentication and registration and track location, etc. It needs
- set `plmn` (public landline mobile network; is a comb of mcc and mnc) 
	- takes the format: `mnc<MNC>.mcc<MCC>.gprs` (e.g., `mnc12.mcc345.gprs`)
		- MCC (mobile country code)
			- e.g., see [3166 countries w/ regional code](https://github.com/lukes/ISO-3166-Countries-with-Regional-Codes/blob/master/all/all.csv) from ISO (international standardization organization)
		- MNC (mobile network code) 
			- see this [list](https://www.mcc-mnc.com/)
		- test: `001/01`
- set `tac` (track area code; changes when user move from an area to another)
- set `ip addr` (configure connection from base station to my laptop running the software)
	- but since both srsran and open5gs runs on the same laptop there's no need to config network here? or i pick one of the loopback addresses. 
### apn & dnn
**APN** (access point name) & **DNN** (data network name) - APN in EPS (powers 3G) is the same as DNN in 5G. 
- APN is composed of two parts:
	- APN network id (*mandatory*)
	- APN operator id (*optional*)
- but i feel like they both have some default value in testing. 
### setting up WAN connectivity
1. enable IP forwarding:
```
$ sudo sysctl -w net.ipv4.ip_forward=1
$ sudo sysctl -w net.ipv6.conf.all.forwarding=1
```
- so that this machine no longer acts as an endhost in network but a router that forwards from the user device (phone) to wider internet. 

2. add NAT Rule 
```
$ sudo iptables -t nat -A POSTROUTING -s 10.45.0.0/16 ! -o ogstun -j MASQUERADE
$ sudo ip6tables -t nat -A POSTROUTING -s 2001:db8:cafe::/48 ! -o ogstun -j MASQUERADE
```
- see [man page](https://man7.org/linux/man-pages/man8/iptables.8.html) for everything. 
- `-t nat`: use NAT; can also do `-t filter`, etc.
- `-A POSTROUTING`: alters packets as they are about to go out by appending rules.
- `-s`: specifies source; so any packets coming from these source ips `10.45.0.0/16` are gonna abide the following rules. 
- `-o `: name of an interface via which a packet is going to be sent; when the "!" argument is used before the interface name, the sense is inverted; so phone packet is NOT going back out through ogstun, then masquerade it. 
- `-j MASQ` give these packets the ip address of the server running open5gs. 

GPRS (General Packet Radio Services) - is 2.5G network. It's the first to move from circuit-switched data to packet-switched data. 
- GGSN (Gateway GPRS Support Node) supports it.

---
## USRP & UHD
Overview - USRP and antenna is the hardware part that receives and transmit radio signals;
UHD (stands for usrp hardware driver) is just a driver that talks in between hardware and srsRAN (which does all the signal processing part).
- refer to the [official guide](https://kb.ettus.com/B200/B210/B200mini/B205mini/B206mini_Getting_Started_Guides). 
### turning digital data into radio waves
some radio waves have very high frequency (like x-ray or gamma-ray) and longer radio waves are what we are looking at here (like wavelength $\lambda$ in the unit of meters).
- shorter ones carries more energy and can penetrate some obstacles (like human body). 
- longer ones can move around obstacles and travel across long distances. 
![image](./images/freqs.png)
- [cool vid](https://www.youtube.com/watch?v=whEqaxlCVSs) and [cooler vid](https://youtu.be/0faCad2kKeg?si=HRHab9LSmF8bvc1_)
	- AM (amplitude modulation) & FM (frequency modulation)![[amfm.png]]
	- some cool encoding that translates from waves to binaries (but 8 phases is usually the limit, anything higher than that it's hard to distinguish)
	- what's cooler is that amplitude can also be modified so that the can be more distinctions thus more bits in one wave.![image](./images/waves-encoding.png)
### RF frontend
is a word for all the circuitry that converts radio signals to some kind of intermediate signal form (before they become electric).  
### Software defined radio
after getting the radio signals from hardware, but most processing (such as modulation) are done by software as opposed to having dedicated circuitry in a traditional radio. so there must be something **in the hardware** that converts signals **from radio to digital**. 

---
## srsRAN
Overview - the software part of software defined radio; does the modulation and all the processing type things; what comes out of it gets passed into open5gs and gets routed into real internet. 
### RAN
Radio access network (RAN) - it's a mobile device network that connects to end-users. 
- Virtualized RAN (vRAN) - no longer requires proprietary hardware to run but instead runs on any general server.
- Open RAN (O-RAN) - Container-based and cloud-native implementation of vRAN. 
### installation guide
```
1. Install dependencies
2. Install RF driver (only required for Split 8 deployments)
3. Clone the repository
4. Build the codebase
```

notes - example configs can be found at `/usr/share/srsran` 
### split 8 & split 7.2  
i think it's a separation of functions where split 8 has their hardware with uhd separated out instead of having everything in a big chunk in 7.2.

### NG, N2, N3, NGAP
NG (= N2 + N3) is the interface between gNB and 5g core (i.e., srsRAN and open5GS). N2 is the authentication module and N3 is the user plane module (UPF). 
NGAP is the protocol that runs over N2 interface. 

![[n2-handover-procedure.png]]

### RAN
radio access network


### GNB config
```
# This example configuration outlines how to configure the srsRAN Project gNB to create a single TDD cell
# transmitting in band 78, with 20 MHz bandwidth and 30 kHz sub-carrier-spacing. A USRP B200 is configured
# as the RF frontend using split 8. Note in this example an external clock source is not used, so the sync
# is not defined and the default is used.

cu_cp:
  amf:
    addr: 127.0.0.5 # Jasmine: changed this from 127.0.1.100
    port: 38412
    bind_addr: 127.0.0.1
    bind_interface: auto
    supported_tracking_areas:
      - tac: 7 # Jasmine: changed this from 7
        plmn_list:
          # - plmn: "00101" # Jasmine: changed this so UE can add APN
          - plmn: "99970"
          # - plmn: "99999"
            tai_slice_support_list:
              - sst: 1

ru_sdr:
  device_driver: uhd
  device_args: type=b200,num_recv_frames=64,num_send_frames=64
  # sync: external
  srate: 23.04 # Jasmine: might adjust based on phone model
  otw_format: default #sc12
  tx_gain: 80  # Jasmine: might adjust based on phone model
  rx_gain: 40  # Jasmine: might adjust based on phone model

cell_cfg:
  dl_arfcn: 632628  # Jasmine: not sure what this does
  band: 78
  channel_bandwidth_MHz: 20
  common_scs: 30
  plmn: "99970"
  tac: 7 # Jasmine: changed this from 7 to match core config
  pci: 1

log:
  filename: /tmp/gnb.log
  all_level: warning

pcap:
  mac_enable: false
  mac_filename: /tmp/gnb_mac.pcap
  ngap_enable: false
  ngap_filename: /tmp/gnb_ngap.pcap
```
### phone model nothing phone (2)


```
# This example configuration outlines how to configure the srsRAN Project gNB to create a single TDD cell
# transmitting in band 78, with 20 MHz bandwidth and 30 kHz sub-carrier-spacing. A USRP B200 is configured
# as the RF frontend using split 8. Note in this example an external clock source is not used, so the sync
# is not defined and the default is used.

cu_cp:
  amf:
    addr: 127.0.0.5 # Jasmine: changed this from 127.0.1.100
    port: 38412
    bind_addr: 127.0.0.1
    bind_interface: auto
    supported_tracking_areas:
      - tac: 1 # Jasmine: changed this from 7
        plmn_list:
          # - plmn: "00101" # Jasmine: changed this so UE can add APN
          - plmn: "12345"
            tai_slice_support_list:
              - sst: 1

ru_sdr:
  device_driver: uhd
  device_args: type=b200,num_recv_frames=64,num_send_frames=64
  sync: external
  srate: 23.04 # Jasmine: might adjust based on phone model
               # # Required FLOAT (61.44). Sets the sampling rate of the RF-frontend in MHz.
  otw_format: sc12
  tx_gain: 80  # Jasmine: might adjust based on phone model
               # Required FLOAT (50). Sets the transmit gain in dB. Supported: [0 - max value supported by radio].
  rx_gain: 40  # Jasmine: might adjust based on phone model
               # Required FLOAT (60). Sets the receive gain in dB. Supported: [0 - max value supported by radio].

cell_cfg:
  dl_arfcn: 632628  # Required UINT (536020). Sets the Downlink ARFCN.
  band: 78  # Optional TEXT (auto). Sets the NR band being used for
            # the cell. If not specified, will be set automatically
            # based on ARFCN. Supported: all release 17 bands.
  channel_bandwidth_MHz: 20 # 20 the number of PRBs will be derived from this.
                         # Supported: [5, 10, 15, 20, 25, 30, 40, 50, 60, 70, 80, 90, 100].
  common_scs: 30 # Required UINT (15). Sets the subcarrier spacing in KHz to be used by the cell. Supported: [15, 30].
  plmn: "12345" # "00101"
  tac: 1 # Jasmine: changed this from 7 to match core config
  pci: 1

log:
  filename: /tmp/gnb.log
  all_level: warning

pcap:
  mac_enable: false
  mac_filename: /tmp/gnb_mac.pcap
  ngap_enable: false
  ngap_filename: /tmp/gnb_ngap.pcap
```