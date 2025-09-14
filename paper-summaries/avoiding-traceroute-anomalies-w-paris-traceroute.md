Paper summary:
When there are load balancing routers, there can be many equal cost paths being used. Therefore, while probes can be going through different routes, classic traceroute still treats the responses as coming from the same route. We thus infer false paths. 
- Loops
	- Load balancing
	- Router $i$ sends timeout response; $i$ sends unreachable response when router $i+1$ is unreachable. Classic traceroute does not differentiate between timeouts and unreachables. 
	- Bad router $i$ forwards probe with TTL=0; router $i+1$ drops it; next round router $i+1$ also drops it.
	- Where the responses have the same source IP address but different TTL. 
- Cycles
	- Load balancing but also real cycles in routing tables. 
- Diamonds
	- Load balancing
Paris traceroute fixes probe packet header (5-tuple) such that all probes going to the same destination go through the exact same route.

---
**[[TTL]]** limits the lifespan of data. At IP layer, TTL is a counter that decreases after passing through every hop. Once TTL goes to 0, the packet is dropped.
**[[ICMP]]** (is above but considered a part of IP) is used for diagnostic or control purposes in response to errors in IP operations.
**[[Traceroute]]** (`traceroute <dest IP>`): Host sends out a request (initially with a TTL of 1). The router, at which TTL drops to 0, send back a time out error message. Host takes note on the IP address. It also sends out the _same_ packet 3 times to ensure consistency.
- By default, `traceroute` sends out UPD probe packets. 
- `ping` sends out ICMP echo probe packets. 
**[[Paris traceroute]]** does some tricks to the probe packet header fields such that they all follow the same path to reach destination. 
The **[[ping]]** (`ping <dest IP>`) command continues to send ICMP echo packets to check if destination is reachable. 
[**Three tiers of ISP**](https://www.geeksforgeeks.org/computer-networks/internet-service-provider-isp-hierarchy/#)
- Tier 1: Huge, usually has international reach, builds their own infrastructure, charges lower-tier ISPs to use their networks (e.g., AT&T)
- Tier 2: Smaller, has regional or national infrastructure, charges similarly. 
- Tier 3: Provide internet to end users (e.g., Comcast, Verizon)
**IP ID** has two primary uses:
1. Identifier of data sequence; put data fragments back together (which _Should_ be unique). 
2. 
---
![](https://www.geeksforgeeks.org/computer-networks/internet-service-provider-isp-hierarchy/)
_Load balancing_
- Routers designate packets to a certain interface based on the packet's _five-tuple_ (source addr, dest addr, protocol, source port, dest port, etc.) from either the IP, UDP, TCP header, and such and such. 
	- Importantly it also use the checksum field `checksum` which can be manipulated.
Traceroute nitty gritty
* _Response TTL_ (the TTL of the time out error message) helps infer the length of the return path.