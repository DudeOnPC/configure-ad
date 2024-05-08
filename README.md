<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 Pro, version 22H2 - Gen2

<h2>High-Level Deployment and Configuration Steps</h2>

**Setup Resources in Azure**

1. Create the Domain Controller VM (Windows Server 2022) named “DC-1”
  - Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/3423044a-2340-4f67-8fea-a5417f6a510f)
![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/9439f364-a9f8-4c76-803a-7945530a3c16)

2. Set Domain Controller’s NIC Private IP address to be static
![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/58aad523-a28b-4ddb-8ce8-99f287e49a68)

3. Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in Step 1
4. Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher

![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/86015ae7-f7a9-4880-92e3-4718bf143120)

![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/bee9e4b7-63e8-4447-ba7d-b921cdcad350)

**Ensure Connectivity between the client and Domain Controller**

5. Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)
![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/b6ba0ecf-6b7b-47e9-9fd6-3578d98b3fe1)

6. Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/cbcce57e-13a8-424f-ba1e-0166b2c58d23)

7. Check back at Client-1 to see the ping succeed

![image](https://github.com/DudeOnPC/configure-ad/assets/167653474/d5b7c264-cfe9-45af-afd5-ddcb5d93fdae)

**Install Active Directory**

8. Login to DC-1 and install Active Directory Domain Services
9. Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)
10. Restart and then log back into DC-1 as user: mydomain.com\labuser

**Create an Admin and Normal User Account in AD**

11. In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
12. Create a new OU named “_ADMINS”
13. Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
14. Add jane_admin to the “Domain Admins” Security Group
15. Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
16. User jane_admin as your admin account from now on


**Join Client-1 to your domain (mydomain.com)**

17. From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
18. From the Azure Portal, restart Client-1
19. Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
20. Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain
21. Create a new OU named “_CLIENTS” and drag Client-1 into there (Step is not really necessary, just for organizational purposes. I guess I skipped this in the lab!)


**Setup Remote Desktop for non-administrative users on Client-1**

22. Log into Client-1 as mydomain.com\jane_admin and open system properties
23. Click “Remote Desktop”
24. Allow “domain users” access to remote desktop
25. You can now log into Client-1 as a normal, non-administrative user now
26. Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)

**Create a bunch of additional users and attempt to log into client-1 with one of the users**

27. Login to DC-1 as jane_admin
28. Open PowerShell_ise as an administrator
29. Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
30. Run the script and observe the accounts being created
31. When finished, open ADUC and observe the accounts in the appropriate OU
32. Attempt to log into Client-1 with one of the accounts (take note of the password in the script)
