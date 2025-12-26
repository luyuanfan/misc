## 5G SA Core Services

![image](./images/open5gs-highres.png)

- Control Plane (AMF - Access and Mobility Management Function): manages mobility, authentication, and session management, ensuring secure and efficient communication. 
    - **NRF** (NF Repository Function) - Registers the locations (IP addresses) of all other core services and does location lookups when one core services needs to communicate with another. 
    - **SCP** (Service Communication Proxy) - Routes messages messages between core services if direct communication is not possible.
    - **SMF** (Session Management Function) - Sets up connection to the Internet when UE tries to connect to the Internet.
    - **AUSF** (Authentication Server Function) - Authenticates subscriber information, e.g., checking if a SIM card's information is valid.
    - **SEPP** (Security Edge Protection Proxy) - Secures communication between two different mobile networks.
    - **UDM** (Unified Data Management) - Handles subscription management and authorization, etc. 
    - **UDR** (Unified Data Repository) - Serves as a centralized data repository. Stores subscriber data, policy data, session data, application data, etc. 
    - **PCF** (Policy Control Function) - Applies policies for charging, [QoS](https://www.fortinet.com/resources/cyberglossary/qos-quality-of-service), and resource allocation, ensuring data prioritization.
    - **BSF** (Binding Support Function) - Stores and serves subscriber-PDU bindings, so NF/AFs can find the correcrt PCF instance handling a specific session. 
    - **NSSF** (Network Slice Selection Function) - Manages [network slicing](https://en.wikipedia.org/wiki/5G_network_slicing), allocating virtual networks for specific services like IoT and HD video.

- User Plane (UPF - User Plane Function): carries user data packets between the cell and the external WAN. It contains only: 
    - **UPF** - Does data forwarding. It connects back to SMF.
kkk
## Roaming

There are two types of 5G Roaming (both are supported by Open5GS):
- LBO (Local Breakout): User plane traffic is routed directly through the visited network's UPF, without going through the home network.
- HR (Home Routed): User plane traffic is routed back to the home network's UPF through the visited network. 

We have a home network and a visited network, where the inter-network communications is entirely handled by Open5GS. The UE is registered in the home network.

## Notes

It'd be helpful to figure out the steps of how Open5GS and srsRAN handles the initial communication with each other. The cell PLMN validation process should happen there.

In practice, we can try entering a mismatched PLMN in the gNB configuration file and see which service reports errors. It will likely be the one that we want to modify.

Based on eyeballing service responsibilities, I would guess AUSF and UDM/UDR are our targets. 