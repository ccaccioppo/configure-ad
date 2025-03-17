# configure-ad

<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This guide walks you through setting up an on-premises Active Directory environment using Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/FiST5JT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We will set up two virtual machines: one with Windows Server 2022, named DC-1, and the other with Windows 10, named Client1. Both virtual machines will be connected to the same virtual network (VNet). Following this, we will configure the IP address on the domain controller (DC-1), switching it from a dynamic assignment to a static one. This step is crucial to ensure that the client machine can successfully join the domain and utilize DC-1 as its DNS server.
</p>
<br />

<p>
<img src="https://i.imgur.com/wIfUJdl.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we will use Remote Desktop Protocol (RDP) to connect to the domain controller and proceed to disable the firewall for the domain, private, and public profiles. To accomplish this, right-click on the Windows Start button, choose "Run," and enter wf.msc in the dialog box. Once the Windows Defender Firewall window opens, verify that the firewall is deactivated for all three profiles: domain, private, and public.
</p>
<br />

<p>
<img src="https://i.imgur.com/ER2Lw3V.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we will update the DNS server settings on Client1 by configuring it to use the static private IP address of DC-1. This change will be made in the networking settings within the Azure portal. Once the update is complete, restart the virtual machines to ensure the new DNS configuration is applied effectively.
</p>
<br />

<p>
<img src="https://i.imgur.com/0zFPyiA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/xm02kle.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, the Remote Desktop Protocol (RDP) will be used for the sake of making a connection by us to the Client1 virtual machine. After we are connected onward, we will attempt to ping the IP address of DC-1 by using PowerShell. Upon such connection, we will try to ping the entire IP address. The ping should work since the firewall was previously disabled on DC-1, enabling responses to ICMP requests. Also, we will definitely execute the ipconfig /all command on Client1 to confirm that DC-1 is properly configured as the specific DNS server for the virtual machine.
</p>
<br />

<p>
<img src="https://i.imgur.com/oBYt50h.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we'll install Active Directory Domain Services (AD DS) using the Server Manager on the domain controller.
</p>
<br />

<p>
<img src="https://i.imgur.com/1UY0lrq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We'll promote DC-1 to a Domain Controller and set up a new forest with the domain name mydomain.com.
</p>
<br />

<p>
<img src="https://i.imgur.com/lRyBbvA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we'll open Active Directory Users and Computers and create two organizational units, _EMPLOYEES and _ADMINS, by right-clicking mydomain.com, selecting New, and then choosing Organizational Unit.
</p>
<br />

<p>
<img src="https://i.imgur.com/lj5a6an.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the Admins OU, we'll create a new user named Ken Doe with the username ken_admin. 
</p>
<br />

<p>
<img src="https://i.imgur.com/lHZsq7U.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we'll add Ken Doe as a Domain Admin. Right-click the user, select Properties, navigate to the Member Of tab, and click Add. In the "Enter object names" field, type Domain Admins and press Enter to complete the process.
</p>
<br />

<p>
<img src="https://i.imgur.com/x1tbaQ1.png height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, log out of DC-1 and reconnect using RDP with the credentials mydomain.com\ken_admin and the assigned password. This account will be used for all future logins to DC-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/sWL89qz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we’ll join the Client1 VM to the domain. Start by connecting to the system via Remote Desktop Protocol (RDP). Once logged in, right-click the Windows logo and select System. From there, click on Rename this PC (Advanced). In the next window, select Change, choose the Domain option, and enter the domain name mydomain.com. After completing these steps, the VM will restart to apply the changes.
</p>
<br />

<p>
<img src="https://i.imgur.com/wsOjP9T.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to DC-1 and open Active Directory Users and Computers. Create another organizational unit named _CLIENTS under mydomain.com.
</p>
<br />

<p>
<img src="https://i.imgur.com/HgTs346.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, log into the Client1 VM as ken_admin. Right-click the Windows logo, select System, then choose Remote Desktop. Click Select users that can remotely access this PC and add Domain Users to allow them RDP access to the VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/DoCgqOP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 Sign in to DC-1 using the ken_admin account and launch PowerShell ISE with administrator privileges. Create a new script file, paste the provided code into it, and run the script. Watch as the accounts are generated automatically.
</p>
<br />

<p>
<img src="https://i.imgur.com/Ca3ShWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once you’ve executed the script, launch Active Directory Users and Computers (ADUC) and check to confirm that the new accounts have been successfully created in the correct _EMPLOYEES organizational unit.
</p>
<br />

<p>
<img src="https://i.imgur.com/PWd6tW8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Finally, sign in to the Client1 VM using one of the user accounts generated by the PowerShell script. Use the username and the default password, which is "Password1." Once logged in, open PowerShell to confirm that you are signed in as one of the users created by the script.
</p>
<br />
