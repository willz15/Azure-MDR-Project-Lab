**Scenario 1: Protocol Isolation & Subnet Micro-segmentation**
Phase 1: Networking & Traffic Patterns

**The Objective**
The goal of this lab was to move beyond basic connectivity and implement Protocol-Level Isolation. I set out to prove that I could selectively allow diagnostic traffic (ICMP) while strictly denying management access (SSH) between subnets, simulating a high-security environment where servers should be "seen but not touched".

**Architecture Overview**
My topology consists of a single Virtual Network (vnet1) segmented into two functional subnets: FrontEnd (10.0.1.0/24) and Backend (10.0.2.0/24). By default, resources in these subnets can communicate freely. To implement Zero-Trust, I applied Network Security Groups (NSGs) directly at the subnet level.

Phase-1-Networking/Scenario-1-NSG-Basics/Topology.png

**The Execution & "The Struggle"**
1. Securing the Perimeter
Initially, I tried to SSH into my linux-frontend VM using its Public IP. It immediately denied access. I remembered I added a NSG to my FrontEnd Subnet.

Solution I fixed this by adding an Inbound security rule to the FrontEnd-NSG:

Priority 100

Name: AllowSSH

Port: 22 (TCP)

Action: Permit

Phase-1-Networking/Scenario-1-NSG-Basics/Frontend-NSG.png


2. Internal Protocol Isolation

For the Backend-NSG, I wanted to showcase the granularity of the control. So, in the rules setup, I have explicitly segregated the management traffic from the diagnostic traffic:

Rule 100 DenySSH: Block all TCP on Port 22, in order to avoid lateral movement.

Rule 110 AllowICMP: I allowed the traffic of ICMP so that the server could still be monitored via Ping.

Phase-1-Networking/Scenario-1-NSG-Basics/Backend-NSG.png

The Findings: Confirmation of Proof

Once inside the linux-frontend VM, I did the final tests against the internal IP of the linux-backend: 10.0.2.4. SSH Test: The connection was refused as the DenySSH rule went into effect. Ping (ICMP) Test: This ping was successful, which validated that the AllowICMP rule was working.

I also used network watcher IP flow and this determined SSH was denied by my NSG rule anmes DenySSH

Phase-1-Networking/Scenario-1-NSG-Basics/Ping.png
Phase-1-Networking/Scenario-1-NSG-Basics/networkwatcher-SSHDeny.png

****Synthesis ****

Multiple NSG: Through this lab, I learned the flow of NSGs and how they prioritize depending where the traffic is originating from and where the NSGs are placed.

Rule Priority is King: By setting my "Deny" rule to Priority 100, I ensured this rule kicked in before my default "AllowVnetInBound" rule (65000), effectively poking a hole in default settings by denying them. Operational Stealth: In this model, the IT group can observe the status of the server (Ping). At the same time, the management interface access (SSH) remains unavailable for the purpose of lateral attacks.
