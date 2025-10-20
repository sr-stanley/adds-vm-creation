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

<img width="597" height="648" alt="0 0 resource group" src="https://github.com/user-attachments/assets/3e778765-9958-40d8-9f20-81b07bc6bb37" />
<br><br>

- Click `Create` → Select your subscription → Name your resource group → Select your region → Click `Review + Create` → Click `Create` again.
<br>
<img width="777" height="905" alt="0 1 name and create" src="https://github.com/user-attachments/assets/5f42b4ab-ca1f-4e65-b982-8f5218dba49d" />

---

### Step 2 - Create a Virtual Network and Subnet
A **Virtual Network** (VNet) in Microsoft Azure is a software-defined, private network that enables secure communication between Azure resources, on-premises systems, and the internet. It functions like a traditional on-premises network which supports subnets, IP address ranges, routing, and security controls while providing isolation, segmentation, and advanced connectivity options such as VPN gateways and ExpressRoute.

A **subnet** in Microsoft Azure is a logical subdivision of a Virtual Network (VNet) that organizes and isolates resources within the cloud. It divides a larger network into smaller, manageable sections, each with its own IP address range, security policies, and routing rules to control and secure network traffic.

- At the top of the screen, in the search bar, type in `virtual network` and select virtual networks under `services`.

<img width="513" height="370" alt="0 2 virtual network" src="https://github.com/user-attachments/assets/7d89038d-0d35-474e-aa14-e2391d8d9a80" />
<br><br>

- Click `Create` → Select your subscription and the resource group you just created → Name your virtual network → Select your region (recommend using the same one as you did for the resource group creation) → Click `Review + Create` → Click `Create` again.

**Wait for deployment to finish**

<img width="1590" height="508" alt="0 3 virtual network deploy" src="https://github.com/user-attachments/assets/43ac7d8a-a2e2-4948-a579-0db371e3464c" />

---

### Step 3 - Create the Domain Controller Virtual Machine
Technically, we are creating a Windows Server that will later be promoted to a domain controller. A **domain controller** is a server within a Windows Active Directory domain that centrally manages identity, authentication, and authorization. It enforces security policies, stores user credentials, and replicates directory data to ensure consistent access and control across the network.


- At the top of the screen, in the search bar, type in `virtual machine` and select virtual machines under `services`.

<img width="514" height="400" alt="0 4 virtual machine" src="https://github.com/user-attachments/assets/070d2e0a-3577-460e-b3c0-92488fb84e41" />

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

<img width="761" height="493" alt="0 5 image and size domain" src="https://github.com/user-attachments/assets/e109dd6f-1da5-4e05-afb6-53cd71b63de0" />

**Networking**
- Virtual Network → Select the one you created earlier.
- Click `Review + Create` → Click `Create` again.

**Wait for deployment to finish.**

<img width="1347" height="535" alt="0 6 dcvm delpoy" src="https://github.com/user-attachments/assets/591e0d8e-c6c6-4e50-afd7-2486f51ca888" />

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

<img width="760" height="492" alt="0 7 image and size client" src="https://github.com/user-attachments/assets/e1ac50c1-2328-4e2c-9038-5575903c0f06" />

**Networking**
- Virtual Network → Select the one you created earlier.
- Click `Review + Create` → Click `Create` again. 

**Wait for deployment to finish.**

<img width="1244" height="524" alt="0 8 cvm deploy" src="https://github.com/user-attachments/assets/c11f77e5-a411-4542-96af-0668b7617127" />

---

### Step 5 - Set the Domain Controller’s Private IP Address to Static in Azure

**Important for DNS Dependency**
> Before joining any client VMs to the domain, ensure the Domain Controller’s private IP address is set to **Static**.  
> If the Domain Controllers IP changes, client DNS resolution will fail and domain authentication will break.  
> Setting the DC-1 private IP to static ensures reliable communication and name resolution across the lab.

**Open the Virtual Machines services inside of Azure. Click and open your DC-1 Virtual Machine.**

- Click the drop down for `Networking`.
- Select `Network settings`.
- Click the `Network interface / IP configuration`.

<img width="1439" height="506" alt="0 9 nic settings" src="https://github.com/user-attachments/assets/4f993c73-5681-4733-8e0e-72967f3adf00" />
<br><br>

- Click on `ipconfig1` → Private IP address settings, select `Static` → Click `Save`.

<br>
<img width="1897" height="683" alt="1 0 ipconfig static" src="https://github.com/user-attachments/assets/63609b5f-bae0-495f-97f5-0800f235eba2" />

---

### Step 6 - Login to DC-1 Virtual Machine via Remote Desktop Connection
If you are on a Mac you will need to download **Microsoft Remote Desktop** from the app store.

**First, inside of Azure get the Public IP address for your DC-1 VM.**

<img width="1611" height="305" alt="1 1 dcvm public ip" src="https://github.com/user-attachments/assets/03810f47-a0dc-4860-85f1-3427f1678507" />

**Next, open `Remote Desktop Connection` and click the drop down arrow for `Show Options` and enter:**
- Computer → Public IP for your DC-1 VM.
- Username → The username you created when setting up the DC-1 VM. 
- Click `Connect` and you will be prompted to enter the password for the DC-1 VM that you created earlier.
- Enter the password and click `OK`. Then click `Yes` to connect to your DC-1 VM via Remote Desktop Connection.

<img width="860" height="478" alt="1 2 dcvm login" src="https://github.com/user-attachments/assets/80c44514-0e2a-42c3-a3da-ed3a3574a96f" />

- Once logged into the Domain Controller, **Server Manager** will load up automatically.

<img width="1910" height="741" alt="1 3 server manager" src="https://github.com/user-attachments/assets/4b25de20-a3e4-4642-811b-c8e022a6ea53" />

---

### Step 7 - Disable Windows Firewall Inside the Domain Controller VM
Normally you would not permanently disable the firewall on a domain controller or other servers in a real-life production environment. In this mock scenario, the firewall is disabled temporarily to make communication and troubleshooting easier while setting up Active Directory and DNS. This avoids issues where firewall rules might block required traffic between the domain controller and client machine. In real-world settings, you would configure the firewall to allow only necessary ports and services, not disable it completely.

- Right click the start menu and select `Run` → Enter `wf.msc` to open Windows Firewall.
- Select `Properties` on the right hand menu → Turn off the Firewall State under **Domain Profile**, **Private Profile** and **Public Profile** → Click `Apply` then `OK`.

<img width="1443" height="768" alt="1 4 disable windows firewall" src="https://github.com/user-attachments/assets/6bb5455d-c069-44d5-8806-c9a7c918ec30" />

---

### Step 8 - Configure the Client VMs DNS Settings to DC-1 VMs Private IP Address
**Reminder:** Because DC-1’s private IP is static, we can safely point the client VM’s DNS to that address for domain communication.

**First, minimize the Remote Desktop Connection and go back into Azure → Select your DC-1 VM and locate the Private IP Address and copy it.**

<img width="1309" height="806" alt="1 5 dcvm private ip" src="https://github.com/user-attachments/assets/1a1a8c6a-618a-4658-8908-040e345cb31e" />

**Next, open your Client Virtual Machine.**
- Click the drop down for `Networking`.
- Select `Network settings`.
- Click the `Network interface / IP configuration`.

<img width="1689" height="506" alt="1 6 client nic settings" src="https://github.com/user-attachments/assets/b09239bd-d9fe-4c30-a634-385037978769" />

- On the left hand menu under `Settings` select DNS servers → Click `Custom` → Paste the DC-1 VMs Private IP Address into the box → Click `Save`.

<img width="1296" height="565" alt="1 7 config client priv ip" src="https://github.com/user-attachments/assets/d6ea6e82-c635-4b33-b7f2-966e81ce52f3" />

**To confirm the settings are activated lets reset the Client VM.**
- Select the Client VM.
- Check the box.
- Click `Restart`.

<img width="1640" height="318" alt="1 8 restart client" src="https://github.com/user-attachments/assets/9edc657b-5d5f-4cae-bed5-ee197b12be49" />

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

<img width="854" height="306" alt="1 9 ping" src="https://github.com/user-attachments/assets/6138fee7-5c97-44d4-8381-81722e49fe78" />

**Next, lets confirm the DNS Server shows DC-1's Private IP Address.**
- Type the command `ipconfig /all`. The DNS Servers should return the DC-1's VM Private IP Address.

<img width="857" height="492" alt="2 0 ipconfig" src="https://github.com/user-attachments/assets/c1b86b9e-f4b0-4f98-b793-7a30e50f9314" />

---

## This concludes Part 1 - configurations and creation of the Domain Controller Virtual Machine and the Client Virtual Machine. In the next part we will install and configure Active Directory in our Domain Controller, add the Client VM to the domain and create and configure users inside of Active Directory for our mock scenario environment.
