Scenario 1.2: The "Traffic Cop" – Implementing Forced Tunneling
Phase 1: Networking & Traffic Patterns

The Business Problem
"Globex Corp" has a strict compliance policy: no server should communicate directly with the internet. Even if an NSG allows the traffic, the company requires that all outbound packets are diverted to a central Security VM (acting as a transparent firewall) for inspection before they exit the Azure network.

The Objective
Override Azure's default "System Routes" by implementing User-Defined Routes (UDR). You will prove that you can control the Next Hop of a packet, forcing it to visit a specific security appliance instead of taking the default shortcut to the internet.
