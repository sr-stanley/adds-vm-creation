<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>


<h1>Creating and configuring a Domain Controller VM and Client VM within Azure VMs</h1>
This tutorial outlines the creation of a Domain Controller Virtual Machine and Client Virtual Machine within Azure. After creation, we will configure DNS settings so that both VMs can communicate via network connection.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

## Table of Contents
- [Step 1 - Create a Resource Group](#step-1--create-a-resource-group)

Open and log in to Azure: azure.microsoft.com
If you do not already have an account, sign up for a free trial.

--------------------------------------------------

### Step 1 - Create a Resource Group
In Microsoft Azure, a resource group is a logical container that holds related resources for an Azure solution. You can think of it like a folder that helps you organize and manage all the services that belong to a specific project, application, or environment.

 - At the top of the screen, in the search bar, type in "resource group" and select resource group under *services*.

[pic 0.0]

- Click create → select your subscription → name your resource group → select your region → click Review + Create → click create again.

[pic 0.1]

--------------------------------------------------

Step 2 - Create a Virtual Network and Subnet
(What is a virtual network?)
(What is a subnet?)

- At the top of the screen, in the search bar, type in "virtual network" and select virtual networks under *services*.

[pic 0.2]

- Click `Create` → Select your subscription and the resource group you just created → name your virtual network → select your region (recommend using the same one as you did for the resource group creation) → click Review + Create → click create again. **Wait for deployment to finish**

- Click `Create` → 
- Select your subscription and the resource group you just created →
- Name your virtual network → Select your region (recommend using the same one as you did for the resource group creation) →
- Click `Review + Create` → Click `Create` again.
**Wait for deployment to finish**

[pic 0.3]

--------------------------------------------------

Step 3 - Create the Domain Controller Virtual Machine
(What is a Domain Controller? What are the roles?)

- At the top of the screen, in the search bar, type in "virtual machine" and select virtual machines under *services*.

[pic 0.4]

Click create and select Virtual Machine and configure the following details.
**Basics**
- Resource Group → Select the one you created earlier.
- Virtual Machine Name → DC-1
- Region → Same as your Virtual Network.
- Image → Windows Server 2022 Datacenter: Azure Edition - x64 Gen2.
- Size → Standard_D2s_v3 - 2 vcpus, 8 GiB memory.
- Create a Username and Password.
- Licensing → Check the two licensing boxes. A second box will pop up once you check the first box.
Box 1: "Would you like to use an existing Windows Server license?" 
Box 2: "I confirm I have an eligible Windows Server license with Software Assurance or Windows Server subscription to apply this Azure Hybrid Benefit".

[pic 0.5]

**Networking**
- Virtual Network → Select the one you created earlier.

Click "Review + Create" → click create again. Wait for deployment to finish.

[pic 0.6]

--------------------------------------------------

Step 4 - Create the Client Virtual Machine

- At the top of the screen, in the search bar, type in "virtual machine" and select virtual machines under *services*.

Click create and select Virtual Machine and configure the following details.
**Basics**
- Resource Group → Select the one you created earlier.
- Virtual Machine Name → Client-1
- Region → Same as your Virtual Network.
- Image → Windows 10 Pro, version 22H2 = x64 Gen2. (If you dont see it in the list click "See all images").
- Size → Standard_D2s_v3 - 2 vcpus, 8 GiB memory.
- Create a Username and Password.
- Licensing → Check the licensing box → "I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights.".

[pic 0.7]

**Networking**
- Virtual Network → Select the one you created earlier.

Click "Review + Create" → click create again. Wait for deployment to finish.

[pic 0.8]

--------------------------------------------------

Step 5 - Set the domain controller’s (DC1) private IP address to static in Azure.

This is important because:
- The domain controller acts as a server and DNS server.
- If its IP changes (as it can with a dynamic IP), clients that rely on its IP for DNS will lose connection.
- Setting the IP to static ensures the clients can always find and communicate with the domain controller reliably.
- This stability is essential for proper server and domain operations.

Open the Virtual Machines services inside of Azure.

Click and open your DC-1 Virtual Machine.
- Click the drop down for Networking.
- Select Network settings.
- Click the Network interface / IP configuration.

[pic 0.9]

- Click on ipconfig1 → Private IP address settings, select "Static" → Click "Save".

[pic 1.0]

--------------------------------------------------

Step 6 - Login to DC-1 Virtual Machine via Remote Desktop Connection. (If you are on a Mac you will need to download "Microsoft Remote Desktop" from the app store.)

First, inside of Azure get the Public IP address for your DC-1 VM.

[pic 1.1]

Next, open "Remote Desktop Connection" and click the drop down arrow for "Show Options" and enter:
- Computer → Public IP for your DC-1 VM.
- Username → The username you created when setting up the DC-1 VM. 

Click "Connect" and you will be prompted to enter the password for the DC-1 VM that you created earlier. Enter the password and click "OK". Then click "Yes" to connect to your DC-1 VM via Remote Desktop Connection.

[pic 1.2]

Once logged into the Domain Controller, Server Manager will load up automatically.

[pic 1.3]

--------------------------------------------------

Step 7 - Disable Windows Firewall Inside the Domain Controller VM

Normally you would not permanently disable the firewall on a domain controller or other servers in a real-life production environment.

In this mock scenario, the firewall is disabled temporarily to make communication and troubleshooting easier while setting up Active Directory and DNS. This avoids issues where firewall rules might block required traffic between the domain controller and client machine.

In real-world settings, you would configure the firewall to allow only necessary ports and services, not disable it completely.

- Right click the start menu and select "Run" → Enter "wf.msc" to open Windows Firewall.
- Select Properties on the right hand menu → Turn off the Firewall State under Domain Profile, Private Profile and Public Profile → Click Apply then OK.

[pic 1.4]

--------------------------------------------------

Step 8 - Configure the Client VM's DNS settings to DC-1 VM's private IP address.

First, minimize the Remote Desktop Connection and go back into Azure → Select your DC-1 VM and locate the Private IP Address and copy it.

[pic 1.5]

Next, open your Client Virtual Machine.
- Click the drop down for Networking.
- Select Network settings.
- Click the Network interface / IP configuration.

[pic 1.6]

On the left hand menu under "Settings" select DNS servers → Click "Custom" → Paste the DC-1 VM's Private IP Address into the box → Click "Save".

[pic 1.7]

To confirm the settings are active lest reset the Client VM.
- Select the Client VM.
- Check the box.
- Click Restart.

[pic 1.8]

--------------------------------------------------

Step 9 - Confirm Client VM can Communicate to DC-1 VM

Login to the Client VM and confirm that the client can communicate with the domain controller by pinging DC-1’s private IP address via PowerShell or Command Prompt. Receiving replies from the ping means the network connection between the two VMs is working correctly. This is a basic but important connectivity test before continuing with domain setup.

To login to the Client VM follow the same steps as you did in Step 6.
First, inside of Azure get the Public IP address for your Client VM.

Next, open "Remote Desktop Connection" and click the drop down arrow for "Show Options" and enter:
- Computer → Public IP for your Client VM.
- Username → The username you created when setting up the Client VM. 

Click "Connect" and you will be prompted to enter the password for the Client VM that you created earlier. Enter the password and click "OK". Then click "Yes" to connect to your DC-1 VM via Remote Desktop Connection.

If this is your first time logging into your Client VM you will need to click through the startup configurations

Once logged onto your Client VM open up Windows PowerShell from the start menu.
Type the command "ping" followed by the Private IP Address for you DC-1 VM. If configured correctly it should return "Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)". If it returns "Destination host unreachable." the two VM's may have been set up in different Virtual Networks or the DC-1's firewall is still active and blocking the ping.

[pic 1.9]

Next, lets confirm the DNS Server shows DC-1's Private IP Address.
Type the command "ipconfig /all". The DNS Servers should return the DC-1's VM Private IP Address.

[pic 2.0]



This concludes Part 1; configurations and creation of the Domain Controller Virtual Machine and the Client Virtual Machine. In the next part we will install and configure Active Directory in our Domain Controller, add the Client VM to the domain and create and configure Users inside of Active Directory for our mock scenario environment.




















<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
