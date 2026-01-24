Scenario 1: Protocol Isolation & Subnet Micro-segmentation
Phase 1: Networking & Traffic Patterns

The Objective
The goal of this lab was to move beyond basic connectivity and implement Protocol-Level Isolation. I set out to prove that I could selectively allow diagnostic traffic (ICMP) while strictly denying management access (SSH) between subnets, simulating a high-security environment where servers should be "seen but not touched".

Architecture Overview
My topology consists of a single Virtual Network (vnet1) segmented into two functional subnets: FrontEnd (10.0.1.0/24) and Backend (10.0.2.0/24). By default, resources in these subnets can communicate freely. To implement Zero-Trust, I applied Network Security Groups (NSGs) directly at the subnet level.

[Insert Assigned-NSG.png here]
