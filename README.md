# Azure-Load-Balancer
![Overview](https://i.imgur.com/wPOZXtx.jpg)

 We set up a basic load balancer to distribute traffic across two backend Virtual Machines hosting web servers and test its functionality. This involves:

1. **Creating a virtual network with a subnet.**
2. **Setting up two identical Virtual Machines and installing Web Server (IIS) on each.**
3. **Configuring a public load balancer with a frontend IP, backend pools, rules with Health probe.**

Finally, we'll test to see how the load balancer distributes traffic by accessing the public frontend IP from a web browser.

Check out my YouTube video below for detailed steps.

[![Watch the video](https://img.youtube.com/vi/N-QBBm2R1zg/0.jpg)](https://youtu.be/N-QBBm2R1zg) 

Assuming that you already have Azure access and have created a subscription and a resource group, those details are omitted in the following steps. Usernames, passwords, regions, and resource names can be selected based on your preference.

### Step 1: Creating a Virtual Network (VNet)
Configure IP Addresses with a subnet:
- In the "IP Addresses" tab, enter the address range `10.1.0.0/16`.
- Delete the default subnet name and create a new subnet `BACKENDPOOL` with the address range of `10.1.0.0/24`.
  
![Imgur](https://imgur.com/iAUqtmB.jpg)

### Step 2: Creating Virtual Machines
- **Availability Set**: Create a new availability set with 2 fault and update domains.
- **Image**: Windows Server 2019 Data Center – x64 Gen2.
![Imgur](https://imgur.com/nEuhPCa)

- **VM Size**: Choose a size such as Standard B2s.
- **Inbound Ports**: Allow HTTP (port 80) and RDP (port 3389).
- **Disk**: Select "Standard SSD".
- **Networking Configuration**:
  - Select the virtual network `MyVNET` created in the first step.
  - Choose the subnet `BACKENDPOOL`.
  - Create a new public IP with Basic SKU and Static IP assignment.
![Imgur](https://imgur.com/QKEG4wH)
- **Creating the Second VM**:
  - Follow the same steps to create another virtual machine, `VM2`, with similar configurations.

### Step 3: Installing Web Server (IIS) on VMs
1. **Connect to VM1 via RDP**:
   - Download and open the RDP file, then connect. Enter the VM1 password.
![Imgur](https://imgur.com/GETuXNp)

2. **Install Web Server (IIS) on VM1**:
   - Open PowerShell and run the following command:
     ```powershell
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
     ```
![Imgur](https://imgur.com/LptPIxI)

3. **Create a Default HTML Page on VM1**:
   - Create a simple HTML file named `default.html` with the content "Welcome to VM1."
   - Save the file in `C:\inetpub\wwwroot`.

4. **Repeat the above steps for VM2**:
   - Connect to VM2 and repeat the steps, changing the content to "Welcome to VM2."

### Step 4: Creating and Configuring the Load Balancer
1. **Navigate to Load Balancers**:
   - **SKU**: Select "Basic."
   - **Type**: Choose "Public."
   - **Tier**: "Regional."
2. **Configure Frontend IP**:
   - Enter `Myfrontend` for the name.
   - Create a new public IP address with Static IP assignment.
![Imgur](https://imgur.com/RV8Wzp1)
3. **Create a Backend Pool**:
   - Type a name and select the virtual network `MyVNET`.
   - Add IP configurations to the backend pool by selecting `VM1` and `VM2`.
![Imgur](https://imgur.com/Va3AcTr)
4. **Set Load Balancing Rules**:
   - Add a new rule with:
     - **Frontend IP**: `Myfrontend`.
     - **Backend Pool**: Select the name of the backend pool created in the previous step.
     - **Protocol**: TCP.
     - **Port and Backend Port**: 80.
![Imgur](https://imgur.com/WEQmLfs)
5. **Configure Health Probe**:
   - **Protocol**: TCP.
   - **Port**: 80.
   - **Interval**: 5 seconds.
![Imgur](https://imgur.com/ffBkPm7)

### Step 5: Testing the Load Balancer
1. Go to the load balancer's frontend IP configuration to find the public IP.
![Imgur](https://imgur.com/UBEniDp)
3. Open an internet browser and enter `http://<public-ip-address>/default.html`.
4. Refresh the browser to see alternating responses from `VM1` and `VM2`.
![Imgur](https://imgur.com/ffBkPm7)

### Summary
A public load balancer in Azure provides numerous advantages, ensuring high availability, scalability, and performance optimization for your applications.

- **High Availability**: By distributing traffic across multiple VMs or instances, it minimizes downtime and maintains consistent performance.
- **Scalability**: Automatically adjusts to handle varying traffic levels, ensuring smooth performance during peak times.
- **Enhanced Performance**: Balances load evenly, preventing any single server from becoming overwhelmed, resulting in optimized application performance.
- **Redundancy and Failover**: Routes traffic away from failed servers to healthy ones, maintaining accessibility even during server issues.
- **Global Reach**: Directs traffic to the nearest available server, reducing latency and improving content delivery speed worldwide.
- **Security Integration**: Works with Azure Firewall and Network Security Groups to secure incoming traffic and ensure compliance with security policies.
- **Cost Efficiency**: Optimizes resource usage and reduces the overall cost of maintaining and scaling applications.
- **Health Monitoring**: Built-in health probes monitor application instances, automatically removing unhealthy ones from the pool.
- **Simplified Management**: Centralized management tools make it easy to configure and manage public load balancers alongside other Azure resources.

To learn more, check out the [Microsoft documentation](https://learn.microsoft.com/en-us/azure/load-balancer/load-balancer-overview).
