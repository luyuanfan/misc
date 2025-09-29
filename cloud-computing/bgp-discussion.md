# Interdomain routing and BGP

## What is BGP

We want to send packets across autonomous systems, each controlled by separate entities. The interdomain routing problem is about having different ASes share *reachability information* of the set of IP addresses that can be reached with each other. Since *carrying traffic through some system is usually a paid service*, so the reachability information (i.e., the routing policies of ASes) tends to get very complex.

These policies must work (i.e., get a packet to its destination) even if (1) ASes along the way might be unrealiable, and (2) other ASes keeps their policies private (because don’t want their economic arrangements made public!). 

Border Gateway Protocol (BGP) is one of such policies and is on its 4th iteration. BGP does not make any assumption about the Internet topology, forming an arbitrary graph of ASes. Gateway routers broadcast to its neighbors what prefixes it can reach and other routers would believe it (in order to prevent loops, these advertised pathes would always be complete path). In case of policy changes or link failures, a BGP can also withdraw an advertised path. These routers would also send the *keepalive* messages to its neighbors which basically just say nothing in my routing table has changed (this way, other routers would be about to infer failure if they no longer receive *keepalive*s from a router). 

## Disadvantages of BGP

1. The chapter does not talk about how routers veritfies the path advertisements from those routers. If there's actually no such verifications built in BGP, a malicious router can easily direct traffic through ASes that it's not supposed to go through. 

2. Since this is another convergence-based policy like link state and distance vector, when the network condition changes frequently and drastically, convergence of information might take a long time. 

## Why is it hard to improve

The slowness to convergence roots in the complexity of the network system. As Internet involves and forms more complex topology, BGP is destined to become more and more complex. Maybe for something (convention, infrastructure) this engrained into how networks communicate, maybe it's also hard to adopt any patches to BGP not mentioning entirely new ones. Just like what the OpenFlow paper suggests, it must be hard to test new things in a real-world setting because of the possible detrimental effects in case of failures. 