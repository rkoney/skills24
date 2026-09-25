**CISCO NETWORKING**

**WORLD SKILLS 2026 — COMPETITION CHEAT SHEET**

## Layer 2 • Layer 3 • Services • Security • VPN • Troubleshooting

Designed for speed, accuracy, fault isolation and command verification during practical networking tasks.

| **WORKFLOW**  | **DO THIS FIRST**                                                        |
| ------------- | ------------------------------------------------------------------------ |
| 1\. Read      | Identify VLANs, IPs, trunks, routing protocol, gateway, expected path.   |
| 2\. Configure | Use the smallest correct command set; avoid changing unrelated features. |
| 3\. Verify    | Check state, neighbors, routes, counters and end-to-end reachability.    |
| 4\. Isolate   | Start at the failing layer; compare both ends of every link/adjacency.   |
| 5\. Save      | Only after verification: copy running-config startup-config.             |

Important: command syntax varies by IOS/IOS-XE release and platform. Treat templates as competition memory aids and confirm feature support on the actual device.

# 1\. Competition Command Operating System

| **QUESTION**                           | **FAST ANSWER**                                                       |
| -------------------------------------- | --------------------------------------------------------------------- |
| Is the interface physically up?        | show interfaces status / show ip interface brief                      |
| Is the VLAN/trunk correct?             | show vlan brief / show interfaces trunk                               |
| Is STP forwarding?                     | show spanning-tree vlan &lt;id&gt;                                    |
| Is EtherChannel formed?                | show etherchannel summary                                             |
| Is the neighbor up?                    | show cdp neighbors detail / show lldp neighbors detail                |
| Is routing installed?                  | show ip route / show ipv6 route                                       |
| Is the protocol adjacency up?          | show ip ospf neighbor / show ip eigrp neighbors / show ip bgp summary |
| Is the control-plane learning correct? | show mac address-table / show arp / show ip cef                       |
| Is security dropping traffic?          | show access-lists / show port-security / show ip dhcp snooping        |
| Can I prove end-to-end?                | ping → traceroute → protocol-specific verification                    |

Golden troubleshooting order: Physical → VLAN/Trunk → STP → EtherChannel → SVI/Gateway → Routing → Policy/Security → Application.

# 2\. Rapid Command Bank

| **TASK**     | **COMMANDS**                                                                                              |
| ------------ | --------------------------------------------------------------------------------------------------------- |
| Interface    | show ip interface brief \| show interfaces &lt;int&gt; \| show interfaces counters errors                 |
| Switchport   | show interfaces &lt;int&gt; switchport                                                                    |
| VLAN         | show vlan brief \| show vlan id &lt;id&gt;                                                                |
| Trunk        | show interfaces trunk                                                                                     |
| MAC          | show mac address-table \| show mac address-table dynamic interface &lt;int&gt;                            |
| STP          | show spanning-tree summary \| show spanning-tree vlan &lt;id&gt;                                          |
| EtherChannel | show etherchannel summary \| show etherchannel port-channel                                               |
| CDP          | show cdp neighbors detail                                                                                 |
| UDLD         | show udld interface &lt;int&gt;                                                                           |
| ARP          | show ip arp \| show arp                                                                                   |
| Routes       | show ip route \| show ip route &lt;prefix&gt;                                                             |
| OSPF         | show ip ospf neighbor \| show ip ospf interface brief \| show ip protocols                                |
| EIGRP        | show ip eigrp neighbors \| show ip eigrp topology \| show ip protocols                                    |
| BGP          | show ip bgp summary \| show ip bgp &lt;prefix&gt; \| show ip bgp neighbors &lt;peer&gt; advertised-routes |
| IPv6         | show ipv6 interface brief \| show ipv6 route \| show ipv6 neighbors                                       |
| Logs         | show logging \| terminal monitor                                                                          |
| Config       | show running-config \| section &lt;feature&gt;                                                            |
| Save         | copy running-config startup-config                                                                        |

# Enable Secret

To enable secret type 8 or 9 use the command below:

**_enable algorithm-type sha256 secret &lt;enter-password-here&gt;_**

**_Note: sha256 stands for the key 8_**

# 3\. Layer 2 — Switch Administration

## MAC Address Table

Architecture / packet-flow view:

Host A ---- SW1 Fa0/1

|

+--- MAC table

VLAN 10 -> 0011.2233.4455 -> Fa0/1

Configuration template:

show mac address-table

show mac address-table dynamic

show mac address-table dynamic vlan 10

show mac address-table dynamic interface fa0/1

! Clear learned entries when testing

clear mac address-table dynamic

Troubleshooting:

• Confirm the host is generating traffic.

• Check the VLAN and ingress interface.

• If the MAC moves between ports, investigate a loop, redundant link or downstream switch.

• A MAC learned on an unexpected port is often the fastest clue.

Verification:

show mac address-table dynamic

show mac address-table dynamic vlan 10

show mac address-table address 0011.2233.4455

Competition speed check: Check VLAN + interface + MAC before changing anything.

## Errdisable Recovery

Architecture / packet-flow view:

Fault

|

+--> Port error condition

|

+--> err-disabled

|

+--> fix cause --> recover / bounce port

Configuration template:

show errdisable recovery

show errdisable detect

show interfaces status err-disabled

! Example recovery timer

errdisable recovery cause bpduguard

errdisable recovery interval 30

interface gi1/0/10

shutdown

no shutdown

Troubleshooting:

• Read logs first: show logging | include ERR_DISABLE.

• Fix the cause before recovery.

• Common causes: BPDU guard, port-security, UDLD, link-flap, storm-control.

Verification:

show interfaces status err-disabled

show errdisable recovery

show logging | include ERR|ERR_DISABLE

Competition speed check: Never repeatedly bounce a port without identifying the trigger.

## L2 MTU

Architecture / packet-flow view:

Frame

+---------+-----------------------------+

| Ethernet| Payload |

+---------+-----------------------------+

<= configured L2 MTU

Configuration template:

show system mtu

show interfaces gi1/0/1 | include MTU

! Platform dependent example

system mtu 9000

Troubleshooting:

• Check both ends and intermediate devices.

• A mismatch may create fragmentation, drops or application-specific failures.

• Exact commands differ by Catalyst family and IOS-XE release.

Verification:

show system mtu

show interfaces &lt;int&gt; | include MTU

show interfaces &lt;int&gt; | include giant|baby|error

Competition speed check: Use the platform-specific MTU command; do not blindly paste system mtu commands.

## CDP

Architecture / packet-flow view:

SW1 ================= SW2

CDP hello

Device ID

Port ID

Platform / IP

Configuration template:

cdp run

interface gi1/0/1

cdp enable

Troubleshooting:

• No neighbor: check CDP enabled on both sides, link state and whether the remote device supports CDP.

• Use LLDP when interoperability is required.

Verification:

show cdp neighbors

show cdp neighbors detail

show cdp interface

Competition speed check: CDP is Cisco-centric; LLDP is the multi-vendor alternative.

## UDLD

Architecture / packet-flow view:

Fiber link

SW1 ================= SW2

normal bidirectional

X

one-way condition

↓

UDLD detects it

Configuration template:

udld enable

interface gi1/0/1

udld port aggressive

Troubleshooting:

• Check optic/fiber polarity and transceiver health.

• Aggressive mode can place a one-way link into err-disabled state.

• Verify support and syntax for the interface type.

Verification:

show udld

show udld interface gi1/0/1

show interfaces status err-disabled

Competition speed check: Use UDLD mainly to protect fiber links from unidirectional failures.

## Access Ports

Architecture / packet-flow view:

PC

|

| untagged

v

SW Gi1/0/10

\[ACCESS VLAN 10\]

Configuration template:

vlan 10

name USERS

interface gi1/0/10

switchport mode access

switchport access vlan 10

spanning-tree portfast

Troubleshooting:

• Verify the port is actually access mode.

• Check VLAN existence and assignment.

• Do not enable PortFast on switch-to-switch links.

Verification:

show interfaces gi1/0/10 switchport

show vlan brief

show spanning-tree interface gi1/0/10 detail

Competition speed check: PortFast is for edge/host ports.

## 802.1Q Trunk + Native VLAN

Architecture / packet-flow view:

SW1 trunk ================= SW2

VLANs 10,20,30

Native VLAN 99

802.1Q tags

Configuration template:

vlan 99

name NATIVE

interface gi1/0/1

switchport trunk encapsulation dot1q

switchport mode trunk

switchport trunk native vlan 99

switchport trunk allowed vlan 10,20,30,99

Troubleshooting:

• On newer Catalyst platforms, encapsulation is fixed to 802.1Q and the encapsulation command may be unavailable.

• Native VLAN must match on both ends.

• Allowed VLAN lists must permit the VLANs actually needed.

Verification:

show interfaces trunk

show interfaces gi1/0/1 switchport

show vlan brief

Competition speed check: Compare both ends line-by-line: mode, native VLAN and allowed VLANs.

## Manual VLAN Pruning

Architecture / packet-flow view:

TRUNK

SW1 ================================= SW2

allow 10,20,30 allow 10,20

X VLAN 30

Configuration template:

interface gi1/0/1

switchport mode trunk

switchport trunk allowed vlan 10,20,30

! Remove VLAN 30:

switchport trunk allowed vlan remove 30

Troubleshooting:

• Check both ends and all downstream trunks.

• A missing VLAN from the allowed list can look like an STP or routing failure.

Verification:

show interfaces trunk

show interfaces gi1/0/1 switchport

Competition speed check: Prune only what the topology does not need.

## Normal vs Extended VLAN Range

Architecture / packet-flow view:

Normal: 1 ---------------- 1005

Extended: 1006 -------- 4094

|

+-- platform/VTP considerations

Configuration template:

vlan 100

name USERS

vlan 2000

name DC_SERVERS

Troubleshooting:

• Check whether the platform supports the requested VLAN ID.

• Extended-range VLAN behavior can depend on VTP mode/version and platform.

Verification:

show vlan brief

show vlan id 2000

show vtp status

Competition speed check: Remember 1006–4094 as the extended range.

## Voice VLAN

Architecture / packet-flow view:

PC -------- IP Phone -------- SW

| voice VLAN 20

\+ data VLAN 10

Configuration template:

vlan 10

vlan 20

interface gi1/0/10

switchport mode access

switchport access vlan 10

switchport voice vlan 20

spanning-tree portfast

Troubleshooting:

• Verify phone receives voice VLAN information.

• Check DHCP scopes and option requirements if used.

• Do not confuse access VLAN with voice VLAN.

Verification:

show interfaces gi1/0/10 switchport

show vlan brief

show mac address-table interface gi1/0/10

Competition speed check: Voice VLAN is an access-port feature, not a trunk between switches.

## Private VLANs

Architecture / packet-flow view:

PVLAN primary

|

+---+---+

| |

isolated community

| |

Host A Host B/C

Configuration template:

vlan 100

private-vlan primary

vlan 101

private-vlan isolated

vlan 102

private-vlan community

vlan 100

private-vlan association 101,102

interface gi1/0/10

switchport mode private-vlan host

switchport private-vlan host-association 100 101

Troubleshooting:

• Verify primary/secondary association.

• Check host role and promiscuous/community/isolated behavior.

• Syntax varies by platform.

Verification:

show vlan private-vlan

show interfaces switchport

show vlan id 100

Competition speed check: Use PVLANs when hosts share a subnet but need Layer-2 isolation.

# 4\. EtherChannel and STP

## LACP EtherChannel

Architecture / packet-flow view:

SW1 SW2

Gi1/0/1 =================== Gi1/0/1

Gi1/0/2 =================== Gi1/0/2

\\\_**Port-Channel \_**/

LACP

Configuration template:

interface range gi1/0/1-2

channel-group 1 mode active

interface port-channel 1

switchport mode trunk

switchport trunk allowed vlan 10,20,30

Troubleshooting:

• Both sides must agree on channel parameters.

• Check VLAN/trunk configuration on the Port-Channel, not only members.

• LACP active/passive can form; passive/passive cannot.

Verification:

show etherchannel summary

show etherchannel port-channel

show interfaces port-channel 1

show lacp neighbor

Competition speed check: Look for Po1(SU) and member ports bundled (P).

## Static EtherChannel

Architecture / packet-flow view:

SW1 ==Gi1/0/1==\\

\==Gi1/0/2==+== Po10 ==+== SW2

Configuration template:

interface range gi1/0/1-2

channel-group 10 mode on

interface port-channel 10

switchport mode trunk

Troubleshooting:

• Static mode has no LACP negotiation.

• A parameter mismatch can cause traffic loss or inconsistent bundling.

• Use LACP where negotiation and protection are desired.

Verification:

show etherchannel summary

show etherchannel detail

Competition speed check: Never mix channel-group mode on with LACP on the opposite end.

## Layer 3 EtherChannel

Architecture / packet-flow view:

R1

| \\

| \\ routed members

+---+==== Po1 ====+---+

|

R2

Configuration template:

interface range gi0/0/0-1

no switchport

channel-group 1 mode active

interface port-channel 1

no switchport

ip address 10.0.0.1 255.255.255.252

Troubleshooting:

• Confirm members are routed ports.

• Check IP address is on the Port-Channel, not member interfaces.

• Verify routing adjacency over the Port-Channel.

Verification:

show etherchannel summary

show ip interface brief

show interfaces port-channel 1

Competition speed check: Think: members carry frames; Port-Channel owns the L3 identity.

## EtherChannel Load Balancing

Architecture / packet-flow view:

Traffic flows

A->B \\

C->D +--> hash --> member 1/2/3/4

E->F /

Configuration template:

show etherchannel load-balance

! Example platform-specific setting:

port-channel load-balance src-dst-ip

Troubleshooting:

• One flow normally hashes to one member; four links do not guarantee one flow at 4× speed.

• Check whether the hash uses source/destination MAC, IP or ports.

Verification:

show etherchannel load-balance

show etherchannel port-channel

Competition speed check: Capacity is distributed by flows, not by splitting one TCP flow across all members.

## EtherChannel Misconfiguration Guard

Architecture / packet-flow view:

Expected:

SW1 ==== Po1 ==== SW2

Mismatch:

SW1 Po1 &lt;====&gt; SW2 independent links

|

consistency fault

Configuration template:

show etherchannel summary

show logging | include EC|ETHERCHANNEL|PAgP|LACP

show interfaces counters errors

Troubleshooting:

• Compare channel-group mode, trunk/access state, native VLAN, allowed VLANs, speed/duplex and LACP/PAgP parameters.

• Do not force traffic through a partially formed bundle.

Verification:

show etherchannel summary

show etherchannel detail

show logging

Competition speed check: Use the summary output first; then drill into the specific member.

## Multichassis EtherChannel Use Cases

Architecture / packet-flow view:

Dual-homed host/switch

/ \\

/ \\

SW-A SW-B

\\ /

\\== MC-LAG ==/

(StackWise Virtual / VSS / vPC-like designs)

Configuration template:

! Design concept — exact commands depend on platform.

! Goal: present one logical L2/LAG endpoint while

! terminating member links on multiple physical chassis.

Troubleshooting:

• Use when you need dual physical chassis for redundancy while retaining a single logical bundle.

• Verify platform-specific technology and peer-link/keepalive requirements.

Verification:

show etherchannel summary

show switch virtual

show vpc

show redundancy

Competition speed check: Know the concept and the platform syntax; do not mix StackWise Virtual, VSS and vPC commands.

## STP Root Bridge and Cost

Architecture / packet-flow view:

Root SW

/ \\

cost 4 cost 19

/ \\

SW2-------------SW3

cost 4

Configuration template:

spanning-tree vlan 10 priority 4096

interface gi1/0/1

spanning-tree cost 4

spanning-tree port-priority 64

Troubleshooting:

• Root is elected using the lowest Bridge ID: priority first, then MAC.

• Port cost influences path selection; port priority breaks ties.

• Check root port/designated port roles.

Verification:

show spanning-tree vlan 10

show spanning-tree vlan 10 root

Competition speed check: Lower STP priority/cost is preferred.

## PortFast + BPDU Guard

Architecture / packet-flow view:

PC ---- Access Port ---- SW

PortFast

BPDU Guard

X unexpected BPDU -> err-disable

Configuration template:

interface gi1/0/10

spanning-tree portfast

spanning-tree bpduguard enable

! Global alternative:

spanning-tree portfast edge default

spanning-tree portfast edge bpduguard default

Troubleshooting:

• Use on true edge/host ports.

• BPDU Guard protects the edge port if a switch is connected.

Verification:

show spanning-tree interface gi1/0/10 detail

show errdisable recovery

show logging | include BPDU

Competition speed check: PortFast speeds host convergence; BPDU Guard protects against accidental switch attachment.

## BPDU Filter

Architecture / packet-flow view:

Edge port

Host ---- SW

BPDU filter

(suppresses BPDUs)

Configuration template:

interface gi1/0/10

spanning-tree portfast

spanning-tree bpdufilter enable

Troubleshooting:

• Use with care: filtering BPDUs can hide a loop.

• Understand the difference between global/default and interface configuration.

Verification:

show spanning-tree interface gi1/0/10 detail

show running-config interface gi1/0/10

Competition speed check: BPDU Guard is generally the safer edge protection mechanism.

## Loop Guard + Root Guard

Architecture / packet-flow view:

Root Guard:

Unexpected superior BPDU

↓

root-inconsistent

↓

protects root placement

Loop Guard:

Missing BPDUs

↓

loop-inconsistent

↓

prevents alternate port

from incorrectly forwarding

Configuration template:

interface gi1/0/1

spanning-tree guard root

interface gi1/0/2

spanning-tree guard loop

Troubleshooting:

• Root Guard is used where a port must never become a path toward the root.

• Loop Guard protects against unidirectional/STP BPDU loss on non-edge links.

Verification:

show spanning-tree inconsistentports

show spanning-tree vlan 10

show logging | include ROOT|LOOP

Competition speed check: Guard the correct side: root guard protects root placement; loop guard protects STP stability.

## PVST+ / Rapid PVST+ / MST

Architecture / packet-flow view:

VLAN 10 -> STP instance

VLAN 20 -> STP instance

MST:

VLAN 10,20,30 --> MSTI 1

VLAN 40,50 --> MSTI 2

Configuration template:

! Rapid PVST+

spanning-tree mode rapid-pvst

! MST

spanning-tree mode mst

spanning-tree mst configuration

name CAMPUS

revision 1

instance 1 vlan 10,20,30

instance 2 vlan 40,50

Troubleshooting:

• All switches in an MST region need matching name, revision and VLAN-to-instance mapping.

• PVST/RPVST can run one logical tree per VLAN; MST maps VLANs to fewer instances.

Verification:

show spanning-tree summary

show spanning-tree mst configuration

show spanning-tree mst

Competition speed check: Memorize: PVST = per VLAN; Rapid PVST = rapid per VLAN; MST = mapped VLAN groups.

# 5\. Layer 3 — DHCP, DNS, FHRP and Routing

## DNS

Architecture / packet-flow view:

Client ---> DNS Server ---> recursive lookup

| |

+-- query ---->|

<--- answer ---+

Configuration template:

ip name-server 10.10.10.53 10.10.10.54

ip domain lookup

ip domain name example.local

Troubleshooting:

• Check reachability to DNS server.

• Check UDP/TCP 53 and local resolver settings.

• Use nslookup/host from an endpoint when available.

Verification:

show hosts

show running-config | include name-server|domain

ping &lt;dns-server&gt;

Competition speed check: DNS failure can masquerade as an application or Internet failure.

## DHCP Server

Architecture / packet-flow view:

Client --DHCPDISCOVER--> Broadcast

<--DHCPOFFER-----

\--DHCPREQUEST--->

<--DHCPACK-------

Configuration template:

ip dhcp excluded-address 10.10.10.1 10.10.10.20

ip dhcp pool USERS

network 10.10.10.0 255.255.255.0

default-router 10.10.10.1

dns-server 10.10.10.53

Troubleshooting:

• Check VLAN/SVI state and DHCP pool network.

• Check excluded addresses and available bindings.

• If the server is remote, configure DHCP relay.

Verification:

show ip dhcp pool

show ip dhcp binding

show ip dhcp conflict

Competition speed check: DHCP = DORA: Discover, Offer, Request, Acknowledge.

## DHCP Relay

Architecture / packet-flow view:

Client VLAN 10

|

SVI 10.10.10.1

|

ip helper-address

|

WAN

|

DHCP Server

Configuration template:

interface vlan 10

ip address 10.10.10.1 255.255.255.0

ip helper-address 10.20.20.10

Troubleshooting:

• Check SVI is up/up.

• Confirm helper address points to the correct server.

• Verify routing both ways between relay and server.

Verification:

show running-config interface vlan 10

show ip interface vlan 10

show ip route 10.20.20.10

Competition speed check: Helper relay forwards several UDP broadcasts, not just DHCP.

## HSRP

Architecture / packet-flow view:

Virtual IP 10.10.10.1

|

+--------+--------+

| |

R1 Active R2 Standby

10.10.10.2 10.10.10.3

Configuration template:

interface vlan 10

ip address 10.10.10.2 255.255.255.0

standby 10 ip 10.10.10.1

standby 10 priority 110

standby 10 preempt

Troubleshooting:

• Check same group and virtual IP.

• Check priorities and preempt.

• Check interface tracking if configured.

Verification:

show standby brief

show standby vlan 10

Competition speed check: HSRP active/standby; hosts use the virtual IP as default gateway.

## VRRP

Architecture / packet-flow view:

Virtual Router IP

|

+----+----+

| |

R1 Master R2 Backup

Configuration template:

interface vlan 10

ip address 10.10.10.2 255.255.255.0

vrrp 10 ip 10.10.10.1

vrrp 10 priority 120

Troubleshooting:

• Verify group, virtual IP and priority.

• Syntax varies by platform/release.

Verification:

show vrrp

show vrrp brief

Competition speed check: VRRP is an open-standard first-hop redundancy protocol.

## GLBP

Architecture / packet-flow view:

Virtual Gateway

|

+-----------+-----------+

| |

AVG AVFs

gateway forward traffic

controller across routers

Configuration template:

interface vlan 10

ip address 10.10.10.2 255.255.255.0

glbp 10 ip 10.10.10.1

glbp 10 priority 120

glbp 10 preempt

Troubleshooting:

• Check GLBP group state and AVG/AVF roles.

• Confirm hosts use the virtual gateway address.

Verification:

show glbp

show glbp brief

Competition speed check: GLBP can distribute hosts across multiple forwarders; HSRP/VRRP normally provide active/standby forwarding.

## Static Routing

Architecture / packet-flow view:

LAN A -- R1 -------- R2 -- LAN B

route ---> 10.20.20.0/24

Configuration template:

ip route 10.20.20.0 255.255.255.0 10.0.12.2

! Floating static route:

ip route 10.20.20.0 255.255.255.0 10.0.13.2 200

Troubleshooting:

• Check next-hop reachability.

• Check administrative distance for backup routes.

• Use a default route only when appropriate.

Verification:

show ip route 10.20.20.0

show ip route static

show ip cef 10.20.20.10

Competition speed check: Static route = destination + mask + next hop/exit interface + optional AD.

## EIGRP Classic and Named Mode

Architecture / packet-flow view:

R1 -------- R2 -------- R3

\\\\\_**\_**\_**\_**\_**\_**\___/

EIGRP AS 100

hello/hold

DUAL

Configuration template:

router eigrp 100

network 10.0.0.0 0.0.255.255

passive-interface default

no passive-interface gi0/0

! Named mode:

router eigrp CAMPUS

address-family ipv4 unicast autonomous-system 100

network 10.0.0.0 0.0.255.255

af-interface default

passive-interface

af-interface gi0/0

no passive-interface

Troubleshooting:

• Check AS number and interface addressing.

• Check passive interfaces.

• Inspect topology for successor/feasible successor behavior.

Verification:

show ip eigrp neighbors

show ip eigrp topology

show ip protocols

show ip route eigrp

Competition speed check: Neighbor first, topology second, route table third.

## EIGRPv6 and Authentication

Here is the complete EIGRP for IPv6 (EIGRPv6) configuration, including both Classic Mode (with native IPv6 IPsec authentication) and Named Mode (which supports traditional key-chains).

## Method 1: Named Mode using Key-Chain Authentication (Recommended)

## In modern Cisco IOS/IOS-XE, EIGRP Named Mode allows you to use a traditional key-chain for IPv6 authentication instead of requiring IPsec

## Step 1: Enable Global IPv6 Unicast Routing

Router(config)# ipv6 unicast-routing

## Step 2: Define the Key-Chain

Router(config)# key chain EIGRP_KEYS

Router(config-keychain)# key 1

Router(config-keychain-key)# key-string MyCiscoPass123

Router(config-keychain-key)# exit

Router(config-keychain)# exit

## Step 3: Configure EIGRPv6 Named Mode with Key-Chain

Router(config)# router eigrp MY_EIGRP

Router(config-router)# address-family ipv6 autonomous-system 100

Router(config-router-af)# eigrp router-id 1.1.1.1

Router(config-router-af)# af-interface default

Router(config-router-af-interface)# authentication mode md5

Router(config-router-af-interface)# authentication key-chain EIGRP_KEYS

Router(config-router-af-interface)# exit

Router(config-router-af)# exit

Router(config-router)# exit

## Step 4: Enable EIGRP on Interfaces

Router(config)# interface GigabitEthernet0/0/0

Router(config-if)# ipv6 address 2001:db8:1::1/64

Router(config-if)# ipv6 eigrp 100

Router(config-if)# exit

Router(config)# interface GigabitEthernet0/0/1

Router(config-if)# ipv6 address 2001:db8:2::1/64

Router(config-if)# ipv6 eigrp 100

Router(config-if)# exit

**Note:** With Named Mode, authentication settings defined under af-interface default automatically apply to all interfaces enabled for EIGRP AS 100.

## Method 2: Classic Mode using IPsec AH Authentication

If you are using **Classic EIGRP Mode** (ipv6 router eigrp &lt;AS&gt;), EIGRPv6 does not support key-chains directly on the interface. Instead, it relies on native IPv6 IPsec AH/ESP authentication.

## Complete Classic Mode Configuration

! Global Enable

ipv6 unicast-routing

! Global Routing Process

ipv6 router eigrp 100

eigrp router-id 1.1.1.1

no shutdown

exit

! Interface Configuration with IPsec AH Authentication

interface GigabitEthernet0/0/0

ipv6 address 2001:db8:100::1/64

ipv6 eigrp 100

ipv6 authentication mode eigrp 100 ipsec ah sha-1 1234567890ABCDEF1234567890ABCDEF12345678

exit

## Verification Commands

## Verify Key-Chain Details

show key chain

## Verify EIGRP Neighbor Adjacencies

show ipv6 eigrp neighbors

## Verify Interface EIGRP Status & Authentication

show ipv6 eigrp interfaces detail GigabitEthernet0/0/0

## Verify IPv6 Routing Table

show ipv6 route eigrp

## OSPFv2

Architecture / packet-flow view:

R1 ===== R2 ===== R3

Area 0

LSAs / SPF

Configuration template:

router ospf 1

router-id 1.1.1.1

network 10.0.12.0 0.0.0.3 area 0

interface gi0/0

ip ospf 1 area 0

ip ospf network point-to-point

Troubleshooting:

• Check area, network type, timers, authentication and MTU.

• Duplicate router IDs can cause problems.

• Use passive-interface for user LANs unless adjacency is required.

Verification:

show ip ospf neighbor

show ip ospf interface brief

show ip ospf database

show ip route ospf

Competition speed check: OSPF adjacency must be FULL (except expected 2-way on some broadcast roles).

## OSPFv3

Architecture / packet-flow view:

IPv6 LAN -- R1 ===== R2 -- IPv6 LAN

OSPFv3

Configuration template:

ipv6 unicast-routing

router ospfv3 1

router-id 1.1.1.1

interface gi0/0

ipv6 address 2001:db8:12::1/64

ospfv3 1 ipv4 area 0

ospfv3 1 ipv6 area 0

Troubleshooting:

• Check IPv6 unicast routing.

• Check link-local addresses and OSPFv3 process/area.

• Exact address-family syntax varies by IOS-XE release.

Verification:

show ospfv3 neighbor

show ospfv3 interface brief

show ipv6 route ospf

Competition speed check: OSPFv3 uses IPv6 link-local neighbor relationships; router ID is still 32-bit.

# 6\. BGP — iBGP, eBGP and Policy

## BGP Peer Relationships

Architecture / packet-flow view:

eBGP iBGP

AS 65001 AS 65001

R1 -------- R2 R1 -------- R2

AS 65002 same AS

TTL normally 1 multihop/loopback common

Configuration template:

router bgp 65001

bgp router-id 1.1.1.1

neighbor 10.0.12.2 remote-as 65002

neighbor 10.0.0.2 remote-as 65001

Troubleshooting:

• Check TCP/179 reachability.

• Verify remote-as, update-source and multihop when applicable.

• iBGP requires an internal design for route propagation; eBGP changes AS_PATH.

Verification:

show ip bgp summary

show ip bgp neighbors 10.0.12.2

show tcp brief

Competition speed check: First question: Is the BGP TCP session Established?

## BGP Path Selection — Competition Memory

Architecture / packet-flow view:

Routes arrive

|

v

Weight

Local Preference

Locally originated

AS_PATH

Origin

MED

eBGP over iBGP

IGP metric to next hop

tie-breakers

Configuration template:

router bgp 65001

neighbor 10.0.12.2 weight 200

neighbor 10.0.12.2 route-map SET-LP in

route-map SET-LP permit 10

set local-preference 200

Troubleshooting:

• Compare competing paths with show ip bgp &lt;prefix&gt;.

• Remember Weight is local to the router; Local Preference is AS-wide policy.

• AS_PATH length is compared after earlier attributes in the simplified Cisco sequence.

Verification:

show ip bgp &lt;prefix&gt;

show ip bgp &lt;prefix&gt; bestpath

show ip bgp neighbors &lt;peer&gt; routes

Competition speed check: Memory line: Weight → Local Pref → Local Origin → AS_PATH → Origin → MED → eBGP/iBGP → IGP metric → tie-breakers.

## BGP Routing Policy / Prefix Filtering

Architecture / packet-flow view:

Neighbor ---> inbound policy ---> RIB

Neighbor <--- outbound policy <--- RIB

prefix-list

route-map

Configuration template:

ip prefix-list ONLY-10 permit 10.10.10.0/24

route-map FILTER-IN permit 10

match ip address prefix-list ONLY-10

router bgp 65001

neighbor 10.0.12.2 route-map FILTER-IN in

Troubleshooting:

• Check whether the policy is applied in the intended direction.

• Verify match conditions and route-map sequence.

• Check advertised-routes and received-routes.

Verification:

show route-map

show ip prefix-list

show ip bgp neighbors 10.0.12.2 routes

show ip bgp neighbors 10.0.12.2 advertised-routes

Competition speed check: Policy debugging = match condition + sequence + direction.

## AS-Path Manipulation

Architecture / packet-flow view:

Outbound path:

AS65001 --> AS65002 --> AS65003

prepend:

65001 65001 65001 --> upstream

(longer path, less attractive)

Configuration template:

route-map PREPEND permit 10

set as-path prepend 65001 65001 65001

router bgp 65001

neighbor 10.0.12.2 route-map PREPEND out

Troubleshooting:

• AS-path prepend normally makes a path look longer to external ASes.

• Confirm policy is applied outbound and inspect the received AS_PATH from the remote side.

Verification:

show route-map PREPEND

show ip bgp neighbors 10.0.12.2 advertised-routes

show ip bgp &lt;prefix&gt;

Competition speed check: Prepending influences inbound path selection; it does not directly set a universal priority.

## BGP Convergence and Scalability

Architecture / packet-flow view:

Full mesh iBGP:

R1-----R2

|\\ /|

| \\ / |

| \\ / |

R3-----R4

Scalable:

RR1 -------- clients

RR2 -------- clients

Configuration template:

router bgp 65001

neighbor 10.0.0.2 remote-as 65001

neighbor 10.0.0.2 route-reflector-client

Troubleshooting:

• Use route reflectors to reduce iBGP full-mesh requirements.

• Check update-source/loopback reachability and next-hop behavior.

• Use summarization/policy carefully to reduce churn.

Verification:

show ip bgp summary

show ip bgp neighbors &lt;peer&gt; | include route-reflector|update-source

show ip route &lt;next-hop&gt;

Competition speed check: Know the difference between adjacency scalability and route-policy scalability.

# 7\. VPN

## Site-to-Site IPsec — Concept

Architecture / packet-flow view:

LAN A -- R1 == encrypted IPsec tunnel == R2 -- LAN B

IKE / IPsec / ESP

Configuration template:

! Generic IOS-XE-style concept; exact syntax is platform dependent.

crypto ikev2 proposal PROP

encryption aes-cbc-256

integrity sha256

group 14

crypto ipsec transform-set TS esp-aes 256 esp-sha256-hmac

mode tunnel

Troubleshooting:

• Check peer reachability, IKE policy/proposal, authentication, transform/SA, interesting traffic and NAT exemptions.

• For modern designs, prefer IKEv2 when supported.

Verification:

show crypto ikev2 sa

show crypto ipsec sa

show crypto session

show crypto session detail

Competition speed check: VPN troubleshooting: underlay reachability → IKE → IPsec SA → traffic selectors/policy → counters.

\================================================================================

SITE-TO-SITE VPN CHEAT SHEET

(Policy-Based vs. Route-Based Configurations)

\================================================================================

1\. POLICY-BASED VPN (Crypto Map Approach)

\--------------------------------------------------------------------------------

Architecture:

\[Local Subnet: 10.1.1.0/24\]

|

(GigabitEthernet0/0 - 1.1.1.1/30)

\[Router A\] &lt;======= IPSec Tunnel (Crypto Map) =======&gt; \[Router B\]

(GigabitEthernet0/0 - 2.2.2.2/30)

|

\[Remote Subnet: 10.2.2.0/24\]

Configuration (Router A):

! Phase 1: ISAKMP Policy

crypto isakmp policy 10

encr aes 256

hash sha256

group 14

lifetime 86400

crypto isakmp key SECRET123 address 2.2.2.2

! Phase 2: Transform Set & Interesting Traffic ACL

crypto ipsec transform-set TS-POLICY esp-aes 256 esp-sha256-hmac

mode tunnel

ip access-list extended ACL-VPN-TRAFFIC

permit ip 10.1.1.0 0.0.255.255 10.2.2.0 0.0.255.255

! Crypto Map Definition & Interface Application

crypto map CMAP-POLICY 10 ipsec-isakmp

set peer 2.2.2.2

set transform-set TS-POLICY

match address ACL-VPN-TRAFFIC

interface GigabitEthernet0/0

crypto map CMAP-POLICY

Verification & Troubleshooting:

show crypto isakmp sa ! Check Phase 1 state (QM_IDLE = successful)

show crypto ipsec sa ! Verify encapsulation/decapsulation counters

show crypto map ! Verify interface bindings and ACL matching

debug crypto isakmp ! Troubleshoot Phase 1 negotiation

debug crypto ipsec ! Troubleshoot Phase 2 negotiation

2\. ROUTE-BASED VPN (VTI / IPsec VTI Approach)

\--------------------------------------------------------------------------------

Architecture:

\[Local Subnet: 10.1.1.0/24\]

|

\[Router A\] ---- (Tunnel0: 172.16.1.1/30) ---- \[Router B\]

1.1.1.1/30 &lt;===== IPsec Virtual Tunnel =====&gt; 2.2.2.2/30

| |

+------------ Static / Dynamic Route --------+

|

\[Remote Subnet: 10.2.2.0/24\]

Configuration (Router A):

! Phase 1: IKEv2 Profile (Modern & Standard for Route-Based)

crypto ikev2 proposal IKEV2-PROP

encryption aes-cbc-256

integrity sha256

group 14

crypto ikev2 policy IKEV2-POL

proposal IKEV2-PROP

crypto ikev2 keyring KEYRING

peer ROUTER-B

address 2.2.2.2

pre-shared-key SECRET123

crypto ikev2 profile IKEV2-PROFILE

match identity remote address 2.2.2.2 255.255.255.255

identity local address 1.1.1.1

authentication local pre-share

authentication remote pre-share

keyring local KEYRING

! Phase 2: IPsec Profile

crypto ipsec transform-set TS-VTI esp-aes 256 esp-sha256-hmac

mode tunnel

crypto ipsec profile IPSEC-VTI-PROFILE

set transform-set TS-VTI

set ikev2-profile IKEV2-PROFILE

! Virtual Tunnel Interface (VTI)

interface Tunnel0

ip address 172.16.1.1 255.255.255.252

tunnel source GigabitEthernet0/0

tunnel destination 2.2.2.2

tunnel mode ipsec ipv4

tunnel protection ipsec profile IPSEC-VTI-PROFILE

! Routing over VTI

ip route 10.2.2.0 255.255.255.0 Tunnel0

Verification & Troubleshooting:

show crypto ikev2 sa ! Check Phase 1 IKEv2 status

show crypto ipsec sa interface tunnel0 ! Check VTI IPsec SAs and packet counters

show ip interface brief Tunnel0 ! Verify line protocol state of the VTI

ping 10.2.2.1 source 10.1.1.1 ! Test end-to-end reachability across the VTI

## Remote Access VPN — Concept

Architecture / packet-flow view:

Remote User

|

Internet

|

VPN Gateway

|

Auth/AAA + Address Pool

|

Internal Network

Configuration template:

! Platform-specific example only.

! Define authentication, address pool, tunnel policy,

! split/full tunnel, DNS and security policy.

Troubleshooting:

• Check authentication, address assignment, tunnel establishment and authorization.

• Remote-access syntax differs significantly between IOS-XE, ASA and FTD.

Verification:

show crypto session

show aaa servers

show users

show logging

Competition speed check: Separate tunnel establishment from post-login access policy.

\================================================================================

REMOTE ACCESS VPN CONFIGURATION TEMPLATE

(Cisco AnyConnect / Secure Client - SSL & IKEv2 Flexible VPN)

\================================================================================

ARCHITECTURE DIAGRAM

\--------------------------------------------------------------------------------

\[Remote User\] \[Internet\] \[Headend Router / ASA\]

(AnyConnect Client) &lt;==============================&gt; (GigabitEthernet0/0 - 198.51.100.1)

IP Pool: 10.100.10.0/24 SSL / IPsec Encryption |

\[Internal LAN\]

10.1.0.0/16

\--------------------------------------------------------------------------------

CONFIGURATION TEMPLATE (Cisco IOS-XE / Router Headend)

\--------------------------------------------------------------------------------

! 1. Local Pool for Remote Client IP Allocation

ip local pool VPN-CLIENT-POOL 10.100.10.10 10.100.10.250

! 2. Split Tunneling Access List (Directs only corporate traffic over VPN)

ip access-list extended ACL-SPLIT-TUNNEL

permit ip 10.1.0.0 0.0.255.255 any

! 3. AAA Authentication & Authorization Setup

aaa new-model

aaa authentication login VPN-AUTH local

aaa authorization network VPN-AUTHOR local

! Local Fallback User (For local testing without TACACS/RADIUS)

username vpnuser privilege 15 secret SuperSecretPassword123!

! 4. PKI Certificate / Self-Signed Cert (For SSL Handshake)

crypto pki trustpoint SELF-SIGNED-TP

enrollment selfsigned

subject-name CN=vpn.company.com

rsakey-pair VPN-KEY

crypto pki enroll SELF-SIGNED-TP

! 5. WebVPN / AnyConnect Gateway Setup

webvpn gateway ANYCONNECT-GW

ip interface GigabitEthernet0/0 port 443

ssl trustpoint SELF-SIGNED-TP

inservice

! 6. WebVPN Context & Client Configuration

webvpn context ANYCONNECT-CONTEXT

ssl authenticate verify certificate mandatory

!

policy group ANYCONNECT-POLICY

functions svc

svc address-pool VPN-CLIENT-POOL netmask 255.255.255.0

svc default-domain company.local

svc dns-server primary 10.1.1.10

svc split-tunnel-policy tunnel-specified

svc split-tunnel-acl ACL-SPLIT-TUNNEL

svc keep-alive 30

!

default-group-policy ANYCONNECT-POLICY

aaa authentication login-list VPN-AUTH

aaa authorization network-list VPN-AUTHOR

gateway ANYCONNECT-GW

inservice

\--------------------------------------------------------------------------------

VERIFICATION & TROUBLESHOOTING

\--------------------------------------------------------------------------------

1\. Verification Commands:

show webvpn session user &lt;username&gt; ! View active user VPN session details

show webvpn session context all ! Monitor all connected remote clients

show ip local pool VPN-CLIENT-POOL ! Check IP pool utilization and assigned leases

show crypto session remote-access ! Verify active SA encryption and traffic counters

2\. Troubleshooting Commands:

debug webvpn gateway ! Troubleshoot SSL/TLS handshake & connections

debug webvpn aaa ! Debug user authentication/authorization steps

debug webvpn session ! Track session establishment failures

# DMVPN

## Architecture Overview

- **Topology:** Dual-Hub or Single-Hub DMVPN Phase 3 with Spoke-to-Spoke dynamic tunnels.
- **IPsec:** IKEv2 FlexVPN/IPsec with Pre-Shared Key (PSK) using IPsec Profile.
- **Underlay:** ISP/Internet WAN Interfaces (GigabitEthernet0/0/0).
- **Overlay Subnet:** 10.255.255.0/24 (Tunnel 0).

## Hub Configuration (DMVPN Hub)

```
! =========================================================
! 1. IPSEC & IKEv2 CONFIGURATION (HUB)
! =========================================================
crypto ikev2 proposal IKEv2-PROPOSAL
 encryption aes-cbc-256
 integrity sha256
 group 19
crypto ikev2 policy IKEv2-POLICY
 match fvrf any
 proposal IKEv2-PROPOSAL
crypto ikev2 keyring IKEv2-KEYRING
 peer DMVPN-SPOKES
  address 0.0.0.0 0.0.0.0
  pre-shared-key local DMVPNP@ssw0rd!
  pre-shared-key remote DMVPNP@ssw0rd!
crypto ikev2 profile IKEv2-PROFILE
 match identity remote any
 authentication local pre-share
 authentication remote pre-share
 keyring local IKEv2-KEYRING
crypto ipsec transform-set TS-AES256-SHA256 esp-aes 256 esp-sha256-hmac
 mode transport
crypto ipsec profile IPSEC-DMVPN-PROFILE
 set transform-set TS-AES256-SHA256
 set ikev2-profile IKEv2-PROFILE
! =========================================================
! 2. mGRE & NHRP TUNNEL CONFIGURATION (HUB)
! =========================================================
interface Tunnel0
 description DMVPN Phase 3 Hub Interface
 ip address 10.255.255.1 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip tcp adjust-mss 1360

 ! NHRP Configurations for Phase 3
 ip nhrp network-id 100
 ip nhrp authentication DMVPNKey
 ip nhrp redirect                     ! Enables Phase 3 Spoke-to-Spoke shortcut redirection
 ip nhrp map multicast dynamic        ! Accepts dynamic multicast registrations from spokes

 ! GRE & Security Settings
 tunnel source GigabitEthernet0/0/0
 tunnel mode gre multipoint
 tunnel key 100
 tunnel protection ipsec profile IPSEC-DMVPN-PROFILE
```

## 2\. Spoke Configuration (DMVPN Spoke)

```
! =========================================================
! 1. IPSEC & IKEv2 CONFIGURATION (SPOKE)
! =========================================================
crypto ikev2 proposal IKEv2-PROPOSAL
 encryption aes-cbc-256
 integrity sha256
 group 19
crypto ikev2 policy IKEv2-POLICY
 match fvrf any
 proposal IKEv2-PROPOSAL
crypto ikev2 keyring IKEv2-KEYRING
 peer DMVPN-HUB
  address 1.1.1.1                    ! Public IP of the Hub
  pre-shared-key local DMVPNP@ssw0rd!
  pre-shared-key remote DMVPNP@ssw0rd!
crypto ikev2 profile IKEv2-PROFILE
 match identity remote any
 authentication local pre-share
 authentication remote pre-share
 keyring local IKEv2-KEYRING
crypto ipsec transform-set TS-AES256-SHA256 esp-aes 256 esp-sha256-hmac
 mode transport
crypto ipsec profile IPSEC-DMVPN-PROFILE
 set transform-set TS-AES256-SHA256
 set ikev2-profile IKEv2-PROFILE
! =========================================================
! 2. mGRE & NHRP TUNNEL CONFIGURATION (SPOKE)
! =========================================================
interface Tunnel0
 description DMVPN Phase 3 Spoke Interface
 ip address 10.255.255.10 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip tcp adjust-mss 1360

 ! NHRP Configurations for Phase 3
 ip nhrp network-id 100
 ip nhrp authentication DMVPNKey
 ip nhrp shortcut                     ! Enables Phase 3 Spoke-to-Spoke dynamic shortcut creation

 ! Map Spoke to Hub Static Address
 ip nhrp nhs 100.255.255.1 nbma 1.1.1.1
 ip nhrp map 10.255.255.1 1.1.1.1
 ip nhrp map multicast 1.1.1.1        ! Sends multicast (EIGRP/OSPF) to Hub

 ! GRE & Security Settings
 tunnel source GigabitEthernet0/0/0
 tunnel mode gre multipoint
 tunnel key 100
 tunnel protection ipsec profile IPSEC-DMVPN-PROFILE
```

## 3\. Dynamic Routing Protocols on DMVPN

### Option A: EIGRP (Recommended for DMVPN Phase 3)

#### Hub EIGRP Config

```
router eigrp 100
 network 10.255.255.0 0.0.0.255
 network 192.168.1.0 0.0.0.255       ! Local LAN
 no auto-summary
 interface Tunnel0
  no ip split-horizon eigrp 100      ! Required on Hub to advertise Spoke routes to other Spokes
```

#### Spoke EIGRP Config

```
router eigrp 100
 network 10.255.255.0 0.0.0.255
 network 192.168.10.0 0.0.0.255      ! Spoke LAN
 no auto-summary
```

### Option B: OSPF

#### Hub OSPF Config

```
router ospf 1
 router-id 1.1.1.1
 network 10.255.255.0 0.0.0.255 area 0
 network 192.168.1.0 0.0.0.255 area 0
interface Tunnel0
 ip ospf network point-to-multipoint ! Ensures single subnet without needing DR/BDR election issues
```

#### Spoke OSPF Config

```
router ospf 1
 router-id 10.10.10.10
 network 10.255.255.0 0.0.0.255 area 0
 network 192.168.10.0 0.0.0.255 area 0
interface Tunnel0
 ip ospf network point-to-multipoint
```

## Verification & Troubleshooting Commands

- **Verify NHRP Mappings and Dynamic Tunnels:**

```
show ip nhrp
show ip nhrp dynamic
```

- **Verify IPsec Security Associations (SAs):**

```
show crypto ipsec sa
show crypto ikev2 sa
```

- **Verify Dynamic Spoke-to-Spoke Tunnel Creation:**

Trigger ping traffic directly between two spoke LAN hosts, then check:

```
show ip nhrp shortcut
show dmvpn
```

# 8\. Management and Operations

## SSH

Architecture / packet-flow view:

Admin PC ---- TCP/22 ---- Switch/Router

|

SSHv2

|

local / AAA

Configuration template:

hostname SW1

ip domain name example.local

username admin privilege 15 secret &lt;PASSWORD&gt;

crypto key generate rsa modulus 2048

ip ssh version 2

line vty 0 4

login local

transport input ssh

Troubleshooting:

• Check hostname/domain before key generation.

• Confirm VTY transport and authentication.

• Use AAA/TACACS+/RADIUS where required.

Verification:

show ip ssh

show ssh

show users

show running-config | section line vty

Competition speed check: SSH failure: IP reachability → TCP/22 → keys/version → VTY → authentication.

## SNMP

Architecture / packet-flow view:

NMS ---- UDP/161 ---- Device

NMS <--- traps 162 --- Device

Configuration template:

! Prefer SNMPv3 for authenticated/encrypted management.

snmp-server group NMS v3 priv

snmp-server user nmsuser NMS v3 auth sha &lt;AUTH&gt; priv aes 128 &lt;PRIV&gt;

Troubleshooting:

• Check source/interface reachability, ACLs, credentials and SNMP version.

• Traps/informs are separate from polling.

Verification:

show snmp

show snmp user

show snmp group

show access-lists

Competition speed check: Polling commonly uses UDP/161; traps/informs use UDP/162.

## NTP

Architecture / packet-flow view:

NTP Server 1 ----\\

NTP Server 2 -----+--> Switch/Router clock

NTP Server 3 ----/

Configuration template:

ntp server 10.10.10.10

ntp server 10.10.10.11 prefer

Troubleshooting:

• Check reachability and UDP/123.

• Verify stratum/synchronization status.

• Time mismatch can affect logs, certificates and authentication.

Verification:

show ntp status

show ntp associations

show clock detail

Competition speed check: Do not trust timestamps until NTP is synchronized.

## IP SLA

Architecture / packet-flow view:

R1

|\\

| \\ IP SLA probe

| \\----> R2 / Internet

|

track object

|

static/default route

Configuration template:

ip sla 10

icmp-echo 10.0.12.2 source-interface gi0/0

frequency 5

ip sla schedule 10 life forever start-time now

track 10 ip sla 10 reachability

Troubleshooting:

• Check probe reachability and source interface.

• Use tracking to remove or prefer routes based on probe state.

Verification:

show ip sla statistics

show ip sla summary

show track

Competition speed check: IP SLA measures reachability/performance; tracking turns the result into routing logic.

## SPAN

Architecture / packet-flow view:

Source port ----\\

+--> SPAN destination --> Analyzer

Source VLAN -----/

Configuration template:

monitor session 1 source interface gi1/0/10 both

monitor session 1 destination interface gi1/0/24

Troubleshooting:

• Destination port is dedicated to the analyzer.

• Check source direction and whether the platform supports the requested source type.

Verification:

show monitor session 1

show interfaces gi1/0/24

Competition speed check: SPAN is invaluable when counters/logs do not explain packet behavior.

## ACL

\================================================================================

ACCESS CONTROL LIST (ACL) CONFIGURATION CHEAT SHEET

(Standard, Extended, Time-Based, Named, & Dynamic/Reflexive)

\================================================================================

ARCHITECTURE & CONCEPT DIAGRAM

\--------------------------------------------------------------------------------

\[ Inbound ACL Evaluation \]

|

( Packet Arrives )

|

\[ Matches Permit Entry? \] -- YES --> \[ Process / Forward \]

|

NO

|

\[ Matches Deny Entry? \] -- YES --> \[ Drop Packet \]

|

NO

|

\[ Explicit / Implicit \] ---------> \[ Drop Packet \]

( Deny Any Any )

\* Rule of Thumb for Placement:

\- Standard ACLs: Place as CLOSE TO THE DESTINATION as possible.

\- Extended ACLs: Place as CLOSE TO THE SOURCE as possible.

\--------------------------------------------------------------------------------

1\. STANDARD ACCESS LISTS (Numbered & Named)

Filter traffic based ONLY on Source IP address.

\--------------------------------------------------------------------------------

! --- Numbered Standard ACL (1-99, 1300-1999) ---

access-list 10 permit 192.168.10.0 0.0.0.255

access-list 10 deny any log

! --- Named Standard ACL (Preferred) ---

ip access-list standard ACL-STD-MANAGEMENT

remark \*\* Restrict SSH/Telnet Access to Management Subnet \*\*

10 permit 10.1.100.0 0.0.0.255

20 permit host 10.2.1.50

30 deny any log

! --- Interface Application ---

interface GigabitEthernet0/1

ip access-group ACL-STD-MANAGEMENT in

\--------------------------------------------------------------------------------

2\. EXTENDED ACCESS LISTS (Numbered & Named)

Filter traffic based on Source, Destination, Protocol (IP/TCP/UDP/ICMP),

and Port Numbers.

\--------------------------------------------------------------------------------

! --- Numbered Extended ACL (100-199, 2000-2699) ---

access-list 100 permit tcp 10.1.10.0 0.0.0.255 host 192.168.1.50 eq 443

access-list 100 deny ip any any log

! --- Named Extended ACL (Preferred) ---

ip access-list extended ACL-EXT-CORP-POLICY

remark \*\* Allow Web and DNS, Block Everything Else \*\*

10 permit tcp 10.1.0.0 0.0.255.255 host 8.8.8.8 eq domain

20 permit udp 10.1.0.0 0.0.255.255 host 8.8.8.8 eq domain

30 permit tcp 10.1.0.0 0.0.255.255 any eq 80

40 permit tcp 10.1.0.0 0.0.255.255 any eq 443

50 permit icmp 10.1.0.0 0.0.255.255 host 10.1.0.1 echo

60 deny ip any any log

! --- Interface Application ---

interface GigabitEthernet0/0

ip access-group ACL-EXT-CORP-POLICY in

\--------------------------------------------------------------------------------

3\. TIME-BASED ACCESS LISTS

Activate or deactivate ACL rules based on system time / NTP schedule.

\--------------------------------------------------------------------------------

! --- Step 1: Define Time Range ---

time-range WORK-HOURS

periodic weekdays 08:00 to 17:00

time-range OFF-HOURS

periodic weekend 00:00 to 23:59

! --- Step 2: Apply Time Range to Extended ACL ---

ip access-list extended ACL-TIME-RESTRICTED

remark \*\* Allow Social Media / Entertainment during Off-Hours Only \*\*

10 permit tcp 10.1.10.0 0.0.0.255 host 198.51.100.25 eq 443 time-range OFF-HOURS

remark \*\* Work Traffic Allowed During Work Hours \*\*

20 permit tcp 10.1.10.0 0.0.0.255 10.2.0.0 0.0.255.255 time-range WORK-HOURS

30 deny ip any any log

! --- Interface Application ---

interface GigabitEthernet0/1

ip access-group ACL-TIME-RESTRICTED in

\--------------------------------------------------------------------------------

4\. REFLEXIVE (STATEFUL) ACCESS LISTS

Automatically generate temporary outbound rules to allow return traffic.

\--------------------------------------------------------------------------------

! --- Outbound ACL (Generates dynamic session rules) ---

ip access-list extended ACL-OUTBOUND-TRAFFIC

permit tcp 10.1.10.0 0.0.0.255 any reflect STATEFUL-SESSION-TABLE

permit udp 10.1.10.0 0.0.0.255 any reflect STATEFUL-SESSION-TABLE

permit icmp 10.1.10.0 0.0.0.255 any reflect STATEFUL-SESSION-TABLE

! --- Inbound ACL (Evaluates return traffic against temporary session rules) ---

ip access-list extended ACL-INBOUND-TRAFFIC

evaluate STATEFUL-SESSION-TABLE

deny ip any any log

! --- Interface Application ---

interface GigabitEthernet0/0

ip access-group ACL-OUTBOUND-TRAFFIC out

ip access-group ACL-INBOUND-TRAFFIC in

\--------------------------------------------------------------------------------

VERIFICATION & TROUBLESHOOTING

\--------------------------------------------------------------------------------

1\. Verification Commands:

show access-lists ! Display all ACLs with match counters

show ip access-lists &lt;name|number&gt; ! Display specific ACL details and hit counts

show time-range ! Verify time-range active/inactive status

show ip interface &lt;interface&gt; ! Verify which ACL is applied to an interface

2\. Troubleshooting Commands & Editing ACLs:

! --- Reordering / Modifying Sequence Numbers ---

ip access-list extended ACL-EXT-CORP-POLICY

no 20 ! Remove rule at sequence number 20

15 permit udp 10.1.0.0 0.0.255.255 host 1.1.1.1 eq 53 ! Insert at seq 15

! --- Debugging Packet Hits ---

debug ip packet &lt;acl-number&gt; detail ! Debug packets matching specific ACL

clear access-list counters ! Reset ACL match/hit counters to zero

# 9\. NAT

## Static NAT

Architecture / packet-flow view:

Inside Local 10.10.10.10

|

NAT

|

Inside Global 203.0.113.10

|

Internet

Configuration template:

ip nat inside source static 10.10.10.10 203.0.113.10

interface gi0/0

ip nat inside

interface gi0/1

ip nat outside

Troubleshooting:

• Check inside/outside designation.

• Verify route to global address and return path.

• Check for ACLs/security policy blocking traffic.

Verification:

show ip nat translations

show ip nat statistics

show ip route 203.0.113.10

Competition speed check: Static NAT = fixed one-to-one mapping.

## PAT / NAT Overload

Architecture / packet-flow view:

10.0.0.10:50000 \\

10.0.0.11:50001 +--> Public IP:high ports --> Internet

10.0.0.12:50002 /

Configuration template:

access-list 10 permit 10.0.0.0 0.0.0.255

ip nat inside source list 10 interface gi0/1 overload

Troubleshooting:

• Check ACL match, inside/outside interfaces and default route.

• Inspect translations and NAT statistics.

Verification:

show ip nat translations

show ip nat statistics

show access-lists 10

Competition speed check: PAT allows many inside hosts to share one public address using ports.

# 10\. Network Security

## PACL

Architecture / packet-flow view:

Ingress frame

|

v

Port ACL

|

permit / deny

|

Switch forwarding

Configuration template:

ip access-list extended EDGE-IN

permit ip 10.10.10.0 0.0.0.255 any

interface gi1/0/10

ip access-group EDGE-IN in

Troubleshooting:

• Check ACL direction and interface.

• Remember ACL processing is first-match, implicit deny at the end.

Verification:

show access-lists EDGE-IN

show running-config interface gi1/0/10

Competition speed check: Apply only the ACL direction and attachment required by the task.

## VACL

Architecture / packet-flow view:

VLAN 10 traffic

Host A ---> \[VACL\] ---> Host B

|

action

forward / drop

Configuration template:

ip access-list extended VACL-MATCH

permit ip 10.10.10.0 0.0.0.255 any

vlan access-map FILTER 10

match ip address VACL-MATCH

action forward

vlan filter FILTER vlan-list 10

Troubleshooting:

• Check access-map sequence, match clause and VLAN filter attachment.

• VACL syntax is platform dependent.

Verification:

show vlan access-map

show vlan filter

show access-lists VACL-MATCH

Competition speed check: VACLs apply policy to traffic within or through a VLAN.

## DHCP Snooping

Architecture / packet-flow view:

DHCP Server

|

trusted trunk

|

SW

/ \\

untrusted untrusted

clients clients

Configuration template:

ip dhcp snooping

ip dhcp snooping vlan 10

interface gi1/0/48

ip dhcp snooping trust

Troubleshooting:

• Trust only interfaces that legitimately carry DHCP server/relay responses.

• Check rate limiting and option 82 behavior if relevant.

Verification:

show ip dhcp snooping

show ip dhcp snooping binding

show ip dhcp snooping statistics

Competition speed check: Trusted = toward legitimate DHCP infrastructure; untrusted = user-facing.

## IP Source Guard

Architecture / packet-flow view:

Client port

|

+--> IP Source Guard

|

DHCP snooping binding

|

source verified

Configuration template:

interface gi1/0/10

ip verify source

Troubleshooting:

• Usually depends on DHCP snooping bindings for dynamic verification.

• Check binding exists for the endpoint.

Verification:

show ip dhcp snooping binding

show ip verify source

show interfaces gi1/0/10

Competition speed check: Source Guard blocks unauthorized source IP/MAC combinations on access ports.

## Dynamic ARP Inspection

Architecture / packet-flow view:

ARP packet

|

v

DAI checks

|

DHCP Snooping binding

|

valid --> forward

invalid -> drop

Configuration template:

ip arp inspection vlan 10

interface gi1/0/48

ip arp inspection trust

Troubleshooting:

• DAI commonly relies on DHCP snooping bindings.

• Trust only infrastructure ports that should bypass normal validation.

Verification:

show ip arp inspection vlan 10

show ip arp inspection interfaces

show ip arp inspection statistics

show ip dhcp snooping binding

Competition speed check: Security dependency chain: DHCP Snooping → IP Source Guard / DAI.

## Port Security

Architecture / packet-flow view:

Host ---- Access Port

|

+-- max MACs

+-- sticky MAC

+-- violation action

Configuration template:

interface gi1/0/10

switchport mode access

switchport port-security

switchport port-security maximum 2

switchport port-security mac-address sticky

switchport port-security violation restrict

Troubleshooting:

• Check learned secure MACs and violation counters.

• Violation modes differ: protect/restrict/shutdown.

• Recover err-disabled ports only after fixing the cause.

Verification:

show port-security

show port-security interface gi1/0/10

show port-security address

show interfaces status err-disabled

Competition speed check: Port security is local to the access port; do not confuse it with DHCP snooping.

Here are complete Cisco IOS / IOS-XE configuration templates for **NetFlow v9**, **Flexible NetFlow (FNF)**, and **IPFIX**.

**Option 1: Flexible NetFlow (FNF) with NetFlow v9**

Flexible NetFlow (FNF) is the modern standard for Cisco IOS/IOS-XE. It uses a 3-part structure: a **Flow Record** (defines what data to collect), a **Flow Exporter** (defines where and how to send the data using v9 format), and a **Flow Monitor** (combines record + exporter and attaches to an interface).

**Step 1: Configure the Flow Exporter**

flow exporter EXPORTER-V9

destination 192.168.1.50

source GigabitEthernet0/0/0

transport udp 2055

export-protocol netflow-v9

option exporter-stats

option interface-table

**Step 2: Configure the Flow Record**

flow record FNF-RECORD-V9

match ipv4 source address

match ipv4 destination address

match ipv4 protocol

match transport source-port

match transport destination-port

match interface input

collect interface output

collect counter bytes long

collect counter packets long

collect timestamp sys-uptime first

collect timestamp sys-uptime last

**Step 3: Configure the Flow Monitor**

flow monitor FNF-MONITOR-V9

exporter EXPORTER-V9

record FNF-RECORD-V9

cache timeout active 60

cache timeout inactive 15

**Step 4: Apply to Interface**

interface GigabitEthernet0/0/1

ip flow monitor FNF-MONITOR-V9 input

ip flow monitor FNF-MONITOR-V9 output

**Option 2: IPFIX (Internet Protocol Flow Information Export)**

IPFIX is the IETF open standard based on NetFlow v9. To export using IPFIX, set the export-protocol in the flow exporter to ipfix.

**Step 1: Configure the IPFIX Flow Exporter**

flow exporter EXPORTER-IPFIX

destination 192.168.1.50

source GigabitEthernet0/0/0

transport udp 4739

export-protocol ipfix

option exporter-stats

option interface-table

**Step 2: Configure the Flow Record (IPv4 & IPv6 Supported)**

flow record FNF-RECORD-IPFIX

match ipv4 source address

match ipv4 destination address

match ipv4 protocol

match transport source-port

match transport destination-port

collect counter bytes long

collect counter packets long

collect timestamp sys-uptime first

collect timestamp sys-uptime last

**Step 3: Configure the Flow Monitor**

flow monitor FNF-MONITOR-IPFIX

exporter EXPORTER-IPFIX

record FNF-RECORD-IPFIX

cache timeout active 60

cache timeout inactive 15

**Step 4: Apply to Interface**

interface GigabitEthernet0/0/1

ip flow monitor FNF-MONITOR-IPFIX input

ip flow monitor FNF-MONITOR-IPFIX output

**Option 3: Legacy Traditional NetFlow v9 (Classic NetFlow)**

If using older Cisco IOS software that does not support Flexible NetFlow structures, use traditional global/interface configuration commands.

! Global Configuration

ip flow-export destination 192.168.1.50 2055

ip flow-export source GigabitEthernet0/0/0

ip flow-export version 9

ip flow-cache timeout active 1

ip flow-cache timeout inactive 15

! Apply to Interface

interface GigabitEthernet0/0/1

ip flow ingress

ip flow egress

**Verification & Troubleshooting Commands**

- **View Active Flow Cache Entries:**

show flow monitor FNF-MONITOR-V9 cache

- **Verify Exporter Status & Statistics:**

show flow exporter EXPORTER-V9 statistics

- **Verify Configured Flow Monitors:**

show flow monitor summary

- **Verify Legacy NetFlow Status (Traditional):**

show ip flow export

show ip cache flow

## 1\. IPv4 DHCP Configurations

### A. IPv4 DHCP Server with Options

Configures the router as a local DHCP server with default gateway, DNS servers, domain name, lease time, and custom DHCP options (e.g., Option 150 for Cisco IP Phones or Option 43 for Wireless APs).

```
! Exclude reserved IP addresses from being leased
ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.10.250 192.168.10.254
! Define the IPv4 DHCP Pool
ip dhcp pool LAN_USERS_V4
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8 1.1.1.1
 domain-name example.com
 lease 7 0 0                           ! Days Hours Minutes (7 days)

 ! Common DHCP Options:
 option 150 ip 192.168.10.250          ! Option 150: TFTP Server for Cisco IP Phones
 option 43 hex f104.c0a8.0a01          ! Option 43: WLC IP Address in Hex (e.g., 192.168.10.1)
```

### B. IPv4 DHCP Relay (Helper Address)

When the DHCP server resides on a different subnet, configure ip helper-address on the local SVI or interface facing the clients.

```
interface GigabitEthernet0/0/1
 description Client Facing Interface
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 10.10.10.50         ! Unicasts DHCP broadcasts to the remote server
```

### C. IPv4 DHCP Client

Configures a router interface to dynamically obtain its IPv4 address via DHCP.

interface GigabitEthernet0/0/0

```
 description WAN / ISP Facing Interface
 ip address dhcp
 no shutdown
```

## 2\. IPv6 DHCP Configurations (DHCPv6)

IPv6 supports both **Stateful DHCPv6** (assigns IP addresses and options) and **Stateless DHCPv6** (assigns options like DNS while SLAAC assigns the IP address).

### A. Stateful IPv6 DHCP Server (Assigns IP Addresses + Options)

```
! Enable global IPv6 routing
ipv6 unicast-routing
! Define the Stateful DHCPv6 Pool
ipv6 dhcp pool STATEFUL_LAN_V6
 address prefix 2001:db8:1000:10::/64 lifetime 172800 86400 ! Valid/Preferred lifetime in seconds
 dns-server 2001:4860:4860::8888 2001:4860:4860::8844
 domain-name example.com
 option 17 hex 0000058300020002        ! Option 17: Vendor-specific info (if required)
! Apply to Interface with Managed Config (M-bit) flag
interface GigabitEthernet0/0/1
 description Stateful IPv6 LAN Interface
 ipv6 address 2001:db8:1000:10::1/64
 ipv6 nd managed-config-flag           ! M-bit=1: Tells hosts to use Stateful DHCPv6 for IP
 ipv6 nd prefix 2001:db8:1000:10::/64 no-advertise ! Disables SLAAC address generation
 ipv6 dhcp server STATEFUL_LAN_V6
```

### B. Stateless IPv6 DHCP Server (SLAAC for IP + DHCPv6 for Options)

Plaintext

```
! Define Stateless DHCPv6 Pool (No address prefix block needed)
ipv6 dhcp pool STATELESS_LAN_V6
 dns-server 2001:4860:4860::8888
 domain-name example.com
! Apply to Interface with Other Config (O-bit) flag
interface GigabitEthernet0/0/1
 description Stateless IPv6 LAN Interface
 ipv6 address 2001:db8:1000:20::1/64
 ipv6 nd other-config-flag             ! O-bit=1: Tells hosts to get DNS/options via DHCPv6
 ipv6 dhcp server STATELESS_LAN_V6
```

### C. IPv6 DHCP Relay

Relays IPv6 DHCP requests to a central remote IPv6 DHCP server.

interface GigabitEthernet0/0/1

```
 description Client Facing Interface
 ipv6 address 2001:db8:1000:10::1/64
 ipv6 dhcp relay destination 2001:db8:2000::50 GigabitEthernet0/0/0
```

### D. IPv6 DHCP Client

Configures an interface to request an IPv6 address (and optionally DNS settings) via DHCPv6.

```
interface GigabitEthernet0/0/0
 description WAN Facing Interface
 ipv6 enable
 ipv6 address dhcp                     ! Obtains dynamic IPv6 address via Stateful DHCPv6
 ipv6 dhcp client request vendor       ! Requests DHCPv6 options
```

## 3\. Verification & Troubleshooting Commands

### For IPv4 DHCP

- **View Active Leases:**

```
show ip dhcp binding
```

- **Check Pool Usage & Statistics:**

```
show ip dhcp pool
```

- **View Address Conflicts:**

```
show ip dhcp conflict
```

### For IPv6 DHCP

- **View Active IPv6 Leases:**

```
show ipv6 dhcp binding
```

- **View DHCPv6 Pool Details:**

```
show ipv6 dhcp pool
```

- **Verify Interface DHCPv6 Settings:**

```
show ipv6 dhcp interface
```

# 11\. Fault Isolation Matrix

| **SYMPTOM**                                | **CHECK FIRST**                   | **THEN**                                             |
| ------------------------------------------ | --------------------------------- | ---------------------------------------------------- |
| Host has no connectivity                   | show interfaces status            | VLAN → access VLAN → STP → gateway → ARP             |
| VLAN works locally but not across switches | show interfaces trunk             | allowed VLAN → native VLAN → STP → MAC               |
| EtherChannel down                          | show etherchannel summary         | mode → member consistency → LACP → trunk config      |
| STP unexpected root                        | show spanning-tree vlan X         | priority → root ID → path cost → port priority       |
| OSPF no adjacency                          | show ip ospf neighbor             | IP/mask → area → network type → MTU → timers/auth    |
| EIGRP no neighbor                          | show ip eigrp neighbors           | AS → interface → passive → ACL → K-values            |
| BGP Idle/Active                            | show ip bgp summary               | TCP/179 → remote-as → source/update → route to peer  |
| DHCP clients fail                          | show ip dhcp binding              | VLAN/SVI → helper → DHCP scope → trust               |
| DAI drops traffic                          | show ip arp inspection statistics | DHCP snooping binding → trust → ARP validation       |
| SSH fails                                  | show ip ssh                       | TCP/22 → VTY → transport → AAA/local auth            |
| VPN tunnel down                            | show crypto ikev2 sa              | underlay → IKE → IPsec → policy/NAT                  |
| NAT fails                                  | show ip nat translations          | inside/outside → ACL match → route → return path     |
| Intermittent packet loss                   | show interfaces counters errors   | duplex/optic → STP → EtherChannel → MTU → congestion |

# 12\. Verification Checklist — Before You Say 'Done'

• Interfaces: expected links are up/up and no abnormal errors/drops.

• VLANs: required VLANs exist and host ports are correctly assigned.

• Trunks: mode, native VLAN and allowed VLAN list match the design.

• STP: intended root is elected and required ports are forwarding.

• EtherChannel: Port-Channel is formed and all intended members are bundled.

• SVIs/gateways: correct IP/mask, up/up, HSRP/VRRP/GLBP state correct.

• Routing: expected prefixes are in the RIB and use the intended next hop.

• Dynamic routing: neighbor/adjacency state is healthy and routes are learned.

• Security: ACLs, DHCP snooping, DAI, Source Guard and port security counters are expected.

• Services: DNS, DHCP, NTP, SNMP and management access are reachable.

• End-to-end: ping the gateway, remote subnet and application endpoint; traceroute if needed.

• Configuration: save only after successful verification.

# 13\. Last-Minute Memorisation Sheet

| **MEMORISE**    | **FAST ASSOCIATION**                                           |
| --------------- | -------------------------------------------------------------- |
| DORA            | DHCP: Discover → Offer → Request → ACK                         |
| STP             | Root = lowest Bridge ID; root port = best path to root         |
| LACP            | Active/active or active/passive; passive/passive does not form |
| HSRP            | Virtual IP; Active/Standby                                     |
| VRRP            | Virtual IP; Master/Backup                                      |
| GLBP            | Virtual gateway + multiple forwarders                          |
| OSPF            | Neighbor → FULL → LSDB → SPF → route                           |
| EIGRP           | Neighbor → topology → successor → route                        |
| BGP             | TCP/179; policy-driven path selection                          |
| NAT             | Inside local ↔ inside global                                   |
| PAT             | Many inside hosts share public IP using ports                  |
| DHCP Snooping   | Trust DHCP infrastructure; build bindings                      |
| DAI             | Validate ARP using bindings                                    |
| IP Source Guard | Validate source IP/MAC on access port                          |
| Port Security   | Limit/learn/violate MAC addresses                              |
| SPAN            | Copy packets to analyzer                                       |
| NTP             | Time synchronization                                           |
| IP SLA          | Measure; tracking can act on result                            |
| CDP             | Cisco neighbor discovery                                       |
| UDLD            | Detect unidirectional links                                    |

# 14\. Practical Command Habits

• Use show running-config interface &lt;interface&gt; to inspect one interface quickly.

• Use show interfaces &lt;interface&gt; switchport before changing access/trunk behavior.

• Use show spanning-tree vlan &lt;id&gt; to identify root, roles, cost and state.

• Use show etherchannel summary before troubleshooting individual bundle members.

• For routing, separate three questions: Is the neighbor up? Is the route learned? Is the route installed?

• For BGP, always inspect the actual prefix with show ip bgp &lt;prefix&gt; instead of relying only on the summary.

• For security features, inspect both configuration and counters; a configured feature that is not matching traffic is not proven.

• When a change fixes the problem, verify the original symptom again — do not stop at a green protocol state.

• Record the final working configuration and save it.