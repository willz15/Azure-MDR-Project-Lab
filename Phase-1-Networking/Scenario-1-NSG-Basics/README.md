# Scenario 1: The "Hole" in the Network
## Phase 1: Networking & Traffic Patterns

### The Business Problem
"Globex Corp" moved data to Azure, but their sensitive SQL database is currently exposed to the public internet.

### The Objective
Deploy a Virtual Network (VNet) and use Network Security Groups (NSG) with Service Tags to restrict traffic.

### Technical Tasks
* **Step 1:** Create VNet with `Frontend` and `Database` subnets.
* **Step 2:** Deploy test VMs.
* **Step 3:** Create NSG to Deny internet traffic but Allow `AppService` Service Tags.
* **Step 4:** Verify by attempting to connect from an unauthorized IP.
