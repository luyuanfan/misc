## Consistent Updates for Software-Defined Networks: Change You Can Believe In! 

### Summary
Planned network configuration changes (updates) have been a major cause of network failures. The paper purposes several abstract *update operations* (i.e., *rules* for network switches regarding how they handle packets coming through) such that programmers would not have to worry about the intermediate, incremental rule (one switch at a time) updates process that *would* cause inconsistent rules between switches (i.e., a given packet is handled by two different polices). The goal is to prevent this kind of situation. 

Two consistency classes are brought forward: per-packet consistency and per-flow consistency. Let's assume a network transitioning from an old rule to a new rule. 

(1) To ensure per-packet consistency, each packet's header is stamped with a rule version number it must be processed with. All switches in this network are installed with both the old and new rules, and process packets accordingly based on their version stamp. Once all packets from the old version have left the network, the old rules are deleted from all switches.

(2) Per-flow consistency ensures that all packets *in a given flow* follow a single rule version, in cases where flow and ordering matters (e.g., in a TCP connection we want to make sure packets arrive in order, so when there's a policy change we do not want to distrub the flow and mess up the in-order delivery). The mechanism that ensures per-flow consistency is mostly similar to the one for per-packet. The controller must track all flows for the old rule - when they all die, the new rule takes effect. This does not work when packets belonging to one flow come from more than one ingress point.

### Comments
As the paper says these methods are mostly tested in a research setting. It seems like they are not dependent on other aspects of the network so I wonder why hasn't it been tested elsewhere given that it is not much of a disruption.