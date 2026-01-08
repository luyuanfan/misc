## Summary

5G roaming security was designed to mainly prevent billing fraud, but not spying. It does not have a goal such as stopping a VPLMN that just wants a free mutually-authenticated session. A state-sponsored attacker + cooperating network operator can turn roaming into a fully authenticated rogue base station who's existence is almost invisible to the user and the trusted HPLMN. 

Roaming allows VPLMN to legitimately get authentication vectors from the HPLMN. Once authenticated, VPLMN can lawfully intercept target user's traffic. A government can make a MNO collaborate and steal information, while the assumption is that MNO never misbehave. 

Turns out I can have the cell to have any name and no checks is done, and the phone is welling to connect and get internet. Of course it is properly registered in this fake network so no tricking is happening. 

USIM takes the MCC MNC on the SIM card and match it with the name of the provider who has this PLMN registered. The PLMN is globally unique. 

How does having the phone rooted play a part in this? If I use a regular non-rooted phone, will it behave differently?

Also can I legally use another network's PLMN? If MNO is a small club what about people like me setting up private networks aren't I an out law?

So we have evil gNB, a VPLMN that collaborates with the evil gNB, and a trusted HPLMN that has roaming agreement with this VPLMN. Targeted user's UE connects to evil gNB due to physical proximity, evil gNB sends registration request to VPLMN, VPLMN relay this request to HPLMN, and a connection is established. 

To prevent the target from noticing the connection to a network different than their HPLMN, the attacker can deliberately choose the network name displayed on the target’s UE. If the target disabled roaming on their UE, the attacker can trick the UE into a connection by setting the MCC and MNC of the target's HPLMN.

In case of a targeted attack, the interest in handling the user’s traffic by itself might be higher so the VPLMN may choose to use Local Breakout (LBO).

It says that 5g roaming specifications does not prevent fake roaming that aims at billing fraud (i.e., those that cause no hplmn charges are not prevented). This is the whole assumption of the paper and is verified with open5gs. However people mostly definitely use commercial network operators which likely perform extra checks? (not sure how it's done but...)

Another major argument is the chain of trust is not passed down to the user in 5g communication specification alone (user has other ways to find out where their packets go through by traceroute apps which are mostly only available on rooted android phones). The chain of trust is only established between HPLMN and VPLMN, nothing between HPLMN and the gNB that the VPLMN owns. If the gNB is evil and collaborating with the VPLMN, there's no way for the HPLMN to detect anomalies at the evil gNB. 