# Configuration and Verification Evidence 
 
[Back to the project overview](README.md) 
 
## 1. VLANs, trunking, and routing 
 
### VLAN access ports on SW2 
 
![SW2 output showing the Support and Operations VLANs and their access ports](images/02-vlan-access-ports.png) 
 
`show vlan brief` shows SW2 ports `Fa0/1`, `Fa0/2`, and `Fa0/10` in 
VLAN 10 (Support), and `Fa0/5` in VLAN 20 (Operations). 
 
### Trunk on SW1 
 
![SW1 output showing GigabitEthernet0/2 trunking and forwarding VLANs 10 and 20](03-trunk-links.png) 
 
`show interfaces trunk` shows that SW1's `Gi0/2` uses 802.1Q and that 
VLANs 10 and 20 are allowed, active, and forwarding on that port. 
 
### Router subinterfaces 
 
![Router output showing both VLAN gateway subinterfaces up](images/04-router-subinterfaces.png) 
 
`show ip interface brief` shows `G0/1.10` at `192.168.10.1` and 
`G0/1.20` at `192.168.20.1`. Both subinterfaces are `up/up`. 
 
### Ping test 
 
![Ping to 192.168.10.51 showing four replies and zero packet loss](images/05-inter-vlan-ping.png) 
 
The ping to `192.168.10.51` received four replies with 0% packet loss. 
The capture shows the destination and results. The source PC's IP 
is outside the captured area. 
 
## 2. DHCP and DHCP relay 
 
### DHCP pools on SRV 
 
![Packet Tracer DHCP settings showing the saved Support and Operations pools](images/06-dhcp-pools.png) 
 
DHCP is enabled. `SUPPORTPOOL` starts at `192.168.10.50` and supplies 
gateway `192.168.10.1`. `OPSPOOL` starts at `192.168.20.50` and supplies 
gateway `192.168.20.1`. Both pools use mask `255.255.255.0`, advertise 
DNS server `192.168.10.10`, and allow up to 20 clients each. 
 
### DHCP relay on R1 
 
![Router configuration showing the DHCP helper on the VLAN 20 subinterface](images/07-dhcp-relay-config.png) 
 
The running configuration shows `encapsulation dot1Q 20` and 
`ip helper-address 192.168.10.10` under `GigabitEthernet0/1.20`. 
This forwards DHCP requests from Operations to the server in VLAN 10. 
 
### Operations client lease 
 
![OPS 02 showing a VLAN 20 address and a DHCP server in VLAN 10](images/08-operations-dhcp-lease.png) 
 
On `OPS 02`, `ipconfig /all` shows `192.168.20.51/24` with gateway 
`192.168.20.1`. The DHCP and DNS server fields both show `192.168.10.10`. 
Together with the router configuration, this documents DHCP relay 
between the two VLANs. 
 
### Support client lease 
 
![SUPPORT 01 showing a VLAN 10 address with its gateway and DNS settings](images/09-support-dhcp-lease.png) 
 
On `SUPPORT 01`, `ipconfig /all` shows `192.168.10.50/24` with gateway 
`192.168.10.1`. The DHCP and DNS server fields both show `192.168.10.10`. 
 
## 3. DNS and HTTP 
 
### DNS A record 
 
![Server DNS settings showing thecle.net mapped to 192.168.10.10](images/11-dns-record.png) 
 
The DNS service is enabled and has an A record that maps `thecle.net` 
to `192.168.10.10`. 
 
### Client DNS lookup 
 
![Client nslookup showing that thecle.net resolves to 192.168.10.10](images/12-dns-resolution.png) 
 
`nslookup thecle.net` queried the DNS server at `192.168.10.10` 
and returned `192.168.10.10` for the hostname. 
 
### Internal website by hostname 
 
![OPS 01 displaying the Infrastructure Lab Portal at http://thecle.net](images/13-portal-by-hostname.png) 
 
On `OPS 01`, the Packet Tracer browser opened `http://thecle.net` 
and displayed **Infrastructure Lab Portal**. This shows an Operations 
client reaching the Support VLAN's web server by hostname.
thecle.net
Home
readme.md

