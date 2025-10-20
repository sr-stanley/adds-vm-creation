<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Creating and configuring a Domain Controller VM and Client VM within Azure VMs</h1>

This tutorial outlines the creation of a **Domain Controller Virtual Machine** and **Client Virtual Machine** within **Azure**. After creation, we will configure DNS settings so that both VMs can communicate over the network.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- PowerShell
- DNS Configuration

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>Pre-requisite</h2>

Open and log in to Azure: [https://azure.microsoft.com/](https://azure.microsoft.com/)

**If you do not already have an account, sign up for a free trial.**

## Table of Contents
- [Step 1 - Create a Resource Group](#step-1---create-a-resource-group)
- [Step 2 - Create a Virtual Network and Subnet](#step-2---create-a-virtual-network-and-subnet)
- [Step 3 - Create the Domain Controller Virtual Machine](#step-3---create-the-domain-controller-virtual-machine)
- [Step 4 - Create the Client Virtual Machine](#step-4---create-the-client-virtual-machine)
- [Step 5 - Set the Domain Controller’s Private IP Address to Static in Azure](#step-5---set-the-domain-controllers-private-ip-address-to-static-in-azure)
- [Step 6 - Login to DC-1 Virtual Machine via Remote Desktop Connection](#step-6---login-to-dc-1-virtual-machine-via-remote-desktop-connection)
- [Step 7 - Disable Windows Firewall Inside the Domain Controller VM](#step-7---disable-windows-firewall-inside-the-domain-controller-vm)
- [Step 8 - Configure the Client VMs DNS settings to DC-1 VMs private IP address](#step-8---configure-the-client-vms-dns-settings-to-dc-1-vms-private-ip-address)
- [Step 9 - Confirm Client VM can Communicate to DC-1 VM](#step-9---confirm-client-vm-can-communicate-to-dc-1-vm)

---

### Step 1 - Create a Resource Group
In Microsoft Azure, a **resource group** is a logical container that holds related resources for an Azure solution. You can think of it like a folder that helps you organize and manage all the services that belong to a specific project, application, or environment.

 - At the top of the screen, in the search bar, type in `resource group` and select resource group under `services`.

[pic 0.0]

- Click `Create` → Select your subscription → Name your resource group → Select your region → Click `Review + Create` → Click `Create` again.

[pic 0.1]

---

### Step 2 - Create a Virtual Network and Subnet
A **Virtual Network** (VNet) in Microsoft Azure is a software-defined, private network that enables secure communication between Azure resources, on-premises systems, and the internet. It functions like a traditional on-premises network which supports subnets, IP address ranges, routing, and security controls while providing isolation, segmentation, and advanced connectivity options such as VPN gateways and ExpressRoute.

A **subnet** in Microsoft Azure is a logical subdivision of a Virtual Network (VNet) that organizes and isolates resources within the cloud. It divides a larger network into smaller, manageable sections, each with its own IP address range, security policies, and routing rules to control and secure network traffic.

- At the top of the screen, in the search bar, type in `virtual network` and select virtual networks under `services`.

[pic 0.2]

- Click `Create` → Select your subscription and the resource group you just created → Name your virtual network → Select your region (recommend using the same one as you did for the resource group creation) → Click `Review + Create` → Click `Create` again.

**Wait for deployment to finish**

[pic 0.3]

---

### Step 3 - Create the Domain Controller Virtual Machine
Technically, we are creating a Windows Server that will later be promoted to a domain controller. A **domain controller** is a server within a Windows Active Directory domain that centrally manages identity, authentication, and authorization. It enforces security policies, stores user credentials, and replicates directory data to ensure consistent access and control across the network.


- At the top of the screen, in the search bar, type in `virtual machine` and select virtual machines under `services`.

[pic 0.4]

- Click `Create` and select `Virtual Machine` and configure the following details.

**Basics**
- Resource Group → Select the one you created earlier.
- Virtual Machine Name → DC-1.
- Region → Same as your Virtual Network.
- Image → Windows Server 2022 Datacenter: Azure Edition - x64 Gen2.
- Size → Standard_D2s_v3 - 2 vcpus, 8 GiB memory.
- Create a Username and Password.
- Licensing → Check the two licensing boxes. A second box will pop up once you check the first box.
 - Box 1: `Would you like to use an existing Windows Server license?`
 - Box 2: `I confirm I have an eligible Windows Server license with Software Assurance or Windows Server subscription to apply this Azure Hybrid Benefit.`

[pic 0.5]

**Networking**
- Virtual Network → Select the one you created earlier.
- Click `Review + Create` → Click `Create` again.

**Wait for deployment to finish.**

[pic 0.6]

---

### Step 4 - Create the Client Virtual Machine
The **Client** virtual machine is a VM that is joined to the domain managed by the domain controller. It acts as a domain member computer, allowing users to log in using their domain credentials and access shared resources, policies, and security settings controlled by Active Directory.

- At the top of the screen, in the search bar, type in `virtual machine` and select virtual machines under `services`.
- Click `Create` and select `Virtual Machine` and configure the following details.

**Basics**
- Resource Group → Select the one you created earlier.
- Virtual Machine Name → Client-1
- Region → Same as your Virtual Network.
- Image → Windows 10 Pro, version 22H2 = x64 Gen2. (If you don't see it in the list click "See all images").
- Size → Standard_D2s_v3 - 2 vcpus, 8 GiB memory.
- Create a Username and Password.
- Licensing → Check the licensing box → `I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights.`

[pic 0.7]

**Networking**
- Virtual Network → Select the one you created earlier.
- Click `Review + Create` → Click `Create` again. 

**Wait for deployment to finish.**

[pic 0.8]

---

### Step 5 - Set the Domain Controller’s Private IP Address to Static in Azure

**Important for DNS Dependency**
> Before joining any client VMs to the domain, ensure the Domain Controller’s private IP address is set to **Static**.  
> If the Domain Controllers IP changes, client DNS resolution will fail and domain authentication will break.  
> Setting the DC-1 private IP to static ensures reliable communication and name resolution across the lab.

**Open the Virtual Machines services inside of Azure. Click and open your DC-1 Virtual Machine.**

- Click the drop down for `Networking`.
- Select `Network setting`s.
- Click the `Network interface / IP configuration`.

[pic 0.9]

- Click on `ipconfig1` → Private IP address settings, select `Static` → Click `Save`.

[pic 1.0]

---

### Step 6 - Login to DC-1 Virtual Machine via Remote Desktop Connection
If you are on a Mac you will need to download **Microsoft Remote Desktop** from the app store.

**First, inside of Azure get the Public IP address for your DC-1 VM.**

[pic 1.1]

**Next, open `Remote Desktop Connection` and click the drop down arrow for `Show Options` and enter:**
- Computer → Public IP for your DC-1 VM.
- Username → The username you created when setting up the DC-1 VM. 
- Click `Connect` and you will be prompted to enter the password for the DC-1 VM that you created earlier.
- Enter the password and click `OK`. Then click `Yes` to connect to your DC-1 VM via Remote Desktop Connection.

[pic 1.2]

- Once logged into the Domain Controller, **Server Manager** will load up automatically.

[pic 1.3]

---

### Step 7 - Disable Windows Firewall Inside the Domain Controller VM
Normally you would not permanently disable the firewall on a domain controller or other servers in a real-life production environment. In this mock scenario, the firewall is disabled temporarily to make communication and troubleshooting easier while setting up Active Directory and DNS. This avoids issues where firewall rules might block required traffic between the domain controller and client machine. In real-world settings, you would configure the firewall to allow only necessary ports and services, not disable it completely.

- Right click the start menu and select `Run` → Enter `wf.msc` to open Windows Firewall.
- Select `Properties` on the right hand menu → Turn off the Firewall State under **Domain Profile**, **Private Profile** and **Public Profile** → Click `Apply` then `OK`.

[pic 1.4]

---

### Step 8 - Configure the Client VMs DNS Settings to DC-1 VMs Private IP Address
**Reminder:** Because DC-1’s private IP is static, we can safely point the client VM’s DNS to that address for domain communication.

**First, minimize the Remote Desktop Connection and go back into Azure → Select your DC-1 VM and locate the Private IP Address and copy it.**

[pic 1.5]

**Next, open your Client Virtual Machine.**
- Click the drop down for `Networking`.
- Select `Network settings`.
- Click the `Network interface / IP configuration`.

[pic 1.6]

- On the left hand menu under `Settings` select DNS servers → Click `Custom` → Paste the DC-1 VMs Private IP Address into the box → Click `Save`.

[pic 1.7]

**To confirm the settings are activated lets reset the Client VM.**
- Select the Client VM.
- Check the box.
- Click `Restart`.

[pic 1.8]

---

### Step 9 - Confirm Client VM can Communicate to DC-1 VM
Login to the Client VM and confirm that the client can communicate with the domain controller by pinging DC-1’s private IP address via PowerShell or Command Prompt. Receiving replies from the ping means the network connection between the two VMs is working correctly. This is a basic but important connectivity test before continuing with domain setup.

**To login to the Client VM follow the same steps as you did in Step 6.**

First, inside of Azure get the Public IP address for your Client VM.
Next, open `Remote Desktop Connection` and click the drop down arrow for `Show Options` and enter:
- Computer → Public IP for your Client VM.
- Username → The username you created when setting up the Client VM.
- Click `Connect` and you will be prompted to enter the password for the Client VM that you created earlier.
- Enter the password and click `OK`. Then click `Yes` to connect to your Client VM via Remote Desktop Connection.

*If this is your first time logging into your Client VM you will need to click through the startup configurations*

**Once logged onto your Client VM open up Windows PowerShell from the start menu.**
- Type the command `ping` followed by the Private IP Address for you DC-1 VM. If configured correctly it should return `Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)`. If it returns `Destination host unreachable.` the two VMs may have been set up in different Virtual Networks or the DC-1's firewall is still active and blocking the ping.

[pic 1.9]

**Next, lets confirm the DNS Server shows DC-1's Private IP Address.**
- Type the command `ipconfig /all`. The DNS Servers should return the DC-1's VM Private IP Address.

[pic 2.0]

---

## This concludes Part 1 - configurations and creation of the Domain Controller Virtual Machine and the Client Virtual Machine. In the next part we will install and configure Active Directory in our Domain Controller, add the Client VM to the domain and create and configure users inside of Active Directory for our mock scenario environment.
