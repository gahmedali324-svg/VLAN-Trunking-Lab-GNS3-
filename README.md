A hands-on networking lab that segments one IP subnet into two VLANs across two switches, joined by an 802.1Q trunk link. Built and tested in GNS3 with virtual PCs.

Overview

All four PCs share the same subnet (192.168.100.0/24), but VLANs separate them at Layer 2:

Hosts in the same VLAN can communicate, even when they are on different switches (the trunk carries both VLANs).
Hosts in different VLANs cannot communicate, even though they share an IP subnet.
Topology

Topology

Device	Connected To	IP Address	VLAN
PC1	ESW1 (fa1/1)	192.168.100.2/24	10
PC2	ESW1 (fa1/2)	192.168.100.3/24	20
PC3	ESW2 (fa1/1)	192.168.100.4/24	10
PC4	ESW2 (fa1/2)	192.168.100.5/24	20

Trunk link: ESW1 and ESW2 are connected by an 802.1Q trunk that carries VLANs 10 and 20.

Tools Used
GNS3
Ethernet switch modules (ESW1, ESW2)
VPCS (Virtual PC Simulator)
PuTTY / telnet console access
Configuration

Run on both switches (legacy vlan database mode, since the IOS image is older):

vlan database
 vlan 10
 vlan 20
 apply
 exit

Access ports (same on both switches):

configure terminal
interface fa1/1
 switchport mode access
 switchport access vlan 10
 exit
interface fa1/2
 switchport mode access
 switchport access vlan 20
 exit

Trunk port (ESW1 uses fa1/15; configure the matching inter-switch port on ESW2):

interface fa1/15
 switchport mode trunk
 switchport trunk encapsulation dot1q
 exit

PC addressing (VPCS):

PC1> ip 192.168.100.2/24
PC2> ip 192.168.100.3/24
PC3> ip 192.168.100.4/24
PC4> ip 192.168.100.5/24
Verification
Test	VLANs	Expected	Result
PC1 → PC3	10 → 10 (across trunk)	Reachable	✅ Success
PC4 → PC2	20 → 20 (across trunk)	Reachable	✅ Success
PC1 → PC2	10 → 20	Blocked	✅ Not reachable
PC4 → PC1	20 → 10	Blocked	✅ Not reachable

Screenshots:

Same-VLAN ping, PC1 to PC3
Same-VLAN ping, PC4 to PC2
ESW1 VLAN configuration
ESW2 VLAN configuration
What I Learned
How VLANs isolate traffic at Layer 2, even inside a single IP subnet
How access ports and trunk ports differ, and why a trunk is needed to carry multiple VLANs between switches
How to configure VLANs with the legacy vlan database syntax on an older IOS image
Troubleshooting CLI errors by reading the error markers and adjusting the command syntax
Possible Improvements
Add inter-VLAN routing (router-on-a-stick or a Layer 3 switch) so VLAN 10 and VLAN 20 can communicate in a controlled way
Add more VLANs and restrict the trunk with an allowed-VLAN list
Save and attach the final switch configurations
Author

Syed Ahmed Ali Gillani LinkedIn
