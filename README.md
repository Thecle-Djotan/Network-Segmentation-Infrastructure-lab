# Segmented Network and Infrastructure Services Lab 
 
## Project overview 
 
I built a Cisco Packet Tracer network for two departments, Support and 
Operations. I used VLANs to separate the departments, configured a router 
to forward traffic between them, and set up a shared server for DHCP, 
DNS, and an internal website. 
 
## Network topology 
 
![Packet Tracer topology showing R1, two switches, the shared server, and the Support and Operations clients](images/01-Topology.png) 
 
| Department | VLAN | Subnet | Default gateway | DHCP pool | 
|---|---:|---|---|---| 
| Support | 10 | `192.168.10.0/24` | `192.168.10.1` | `SUPPORTPOOL` | 
| Operations | 20 | `192.168.20.0/24` | `192.168.20.1` | `OPSPOOL` | 
 
The shared server, `SRV`, is in VLAN 10 at `192.168.10.10`. 
It provides DHCP, DNS, and HTTP. R1 connects to SW2, and a trunk 
between SW1 and SW2 carries both VLANs. 
 
## Implementation 
 
1. Connected R1, SW1, SW2, the server, and the client devices. 
2. Created VLAN 10 for Support and VLAN 20 for Operations. Assigned 
   access ports according to the department of the connected device. 
3. Configured 802.1Q trunking to carry both VLANs between the switches 
   and to the router. 
4. Configured `G0/1.10` and `G0/1.20` on R1 as the default gateways 
   for Support and Operations, respectively. 
5. Configured DHCP on `SRV`. `SUPPORTPOOL` starts at `192.168.10.50` 
   and `OPSPOOL` starts at `192.168.20.50`. Each pool supports up 
   to 20 clients and supplies `192.168.10.10` as the DNS server. 
6. Added `ip helper-address 192.168.10.10` on R1's `G0/1.20` 
   to forward Operations DHCP requests to the server in VLAN 10. 
7. Created a DNS A record that maps `thecle.net` to `192.168.10.10` 
   and hosted the Infrastructure Lab Portal on the server. 
 
## Test results 
 
| Check | Observed result | 
|---|---| 
| Support DHCP | `SUPPORT 01` showed `192.168.10.50/24` with gateway `192.168.10.1`. | 
| Operations DHCP relay | `OPS 02` showed `192.168.20.51/24` with gateway `192.168.20.1`. Its DHCP and DNS server was `192.168.10.10`. | 
| Ping test | The ping to `192.168.10.51` returned four replies with 0% packet loss. | 
| DNS resolution | `nslookup thecle.net` returned `192.168.10.10`. | 
| Internal website | `OPS 01` opened `http://thecle.net` and displayed **Infrastructure Lab Portal**. | 
 
[View configuration screenshots and test evidence](verification.md) 
 
## What I learned 
 
- A VLAN can span multiple switches. Access ports assign devices to a VLAN, 
  while a trunk carries traffic for multiple VLANs between network devices. 
- Each VLAN needs its own default gateway to reach another subnet. 
  I used two subinterfaces on one physical router port to provide those gateways. 
- The initial DHCP broadcast stays inside the client's VLAN. Support 
  clients can reach the DHCP server directly. Operations clients need 
  R1 to relay their DHCP requests to the server in VLAN 10. 
- A DNS lookup checks whether the hostname resolves to the expected IP. 
  Opening the portal by hostname checks that a client can also reach 
  and use the HTTP service. 
 

Home
verification.md
verification.md
One daily markdown page: verification.md
