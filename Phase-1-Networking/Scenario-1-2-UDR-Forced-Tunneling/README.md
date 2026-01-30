Scenario 1.2: The "Traffic Cop" – Implementing Forced Tunneling
Phase 1: Networking & Traffic Patterns

The Business Problem
"Globex Corp" has a strict compliance policy: no server should communicate directly with the internet. Even if an NSG allows the traffic, the company requires that all outbound packets are diverted to a central Security VM (acting as a transparent firewall) for inspection before they exit the Azure network.

The Objective
Override Azure's default "System Routes" by implementing User-Defined Routes (UDR). You will prove that you can control the Next Hop of a packet, forcing it to visit a specific security appliance instead of taking the default shortcut to the internet.

Architecture Overview My topology consists of a single Virtual Network (vnet1) segmented into 3 subnets. I have one windows server, and two linux boxes each in their own subnets.
By default all these subnets can commuicate to each other and the windows server has a public IP allowing it to communicate to the internet. 

Phase-1-Networking/Scenario-1-2-UDR-Forced-Tunneling/Topology.png

To have the windows server commuicate to the internet by going through the linux server acting as a NVA I began by creating a Route Table with a default route (0.0.0.0/0) pointing to my Network Virtual Appliance (NVA). To maintain access, I added a specific /32 rule allowing RDP traffic from my home IP directly to the internet as without this I was not able to access the window server due to the default route. I also enabled IP forwarding on the NVA’s Network Interface (NIC) within the Azure Portal.


Phase-1-Networking/Scenario-1-2-UDR-Forced-Tunneling/NIC-IPForwarding.png
Phase-1-Networking/Scenario-1-2-UDR-Forced-Tunneling/Route.png

With the infrastructure in place, I verified that RDP was working and successfully established an SSH connection from my Windows server to the Linux NVA. On the NVA, I set up iptables and enabled kernel-level forwarding. Despite this, ping attempts to 8.8.8.8 were unsuccessful.

To better isolate the problem, I proceeded to test connectivity to a third server on Subnet 3. This ping was successful, and I verified that the traffic was, in fact, being routed through the NVA. To further validate the configuration, I used Azure’s Effective Routes and Next Hop tools, which verified that all internet-bound traffic was being properly sent to the NVA.

Phase-1-Networking/Scenario-1-2-UDR-Forced-Tunneling/Connection-Troubleshooting.png

I then thought of using tcmdump, while tcpdump on the Linux server showed active traffic for the Subnet 3 pings, it remained silent when I attempted to ping 8.8.8.8.

The final resolution was to create an Inbound NSG rule for the NVA to allow traffic from the 10.0.0.0/24 subnet. The reason for this was that although the traffic was routed to the NVA, the NSG was dropping the packet because the packet was not addressed to the NVA. The reason why my ping worked to subnet three is becuase of AllowVnetInBound which allows communication to all resources within the Vnet. 

Phase-1-Networking/Scenario-1-2-UDR-Forced-Tunneling/Forward-NSGRule.png

