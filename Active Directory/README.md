# Active Directory Homelab

## Objective

- **Build a functional, small-scale Active Directory environment** to understand how identity, authentication, and central management operate in an enterprise network.

- **Practice deploying and configuring core AD components** such as domain controllers, user accounts, OUs, and administrative tools.

- **Gain hands-on experience automating administrative tasks** with PowerShell.

### Skills Learned

- **Active Directory Deployment & Configuration**: Installed, promoted, and managed a Windows Server domain controller.

- **User & OU Management**: Created accounts and structured OUs within the Active Directory.

- **PowerShell Automation**: Automated bulk user creation and administrative tasks using scripts.

### Tools Used

- Windows Server 2019 VM
- Windows 10 VM
- Powershell

## Findings

- **Proper DNS configuration is critical** as misconfigured DNS caused login failures and domain join issues until corrected.

- **Automating user creation in PowerShell is far more efficient** than manually creating accounts in Active Directory Users and Computers.

- **The homelab reinforced how authentication flows in a Windows domain**, particularly the role of the domain controller in verifying credentials.

- **Hands-on practice highlighted how AD environments are structured in real organizations**, making enterprise security concepts easier to understand.

***

**Active Directory Diagram via Microsoft Visio**

<img width="753" height="573" alt="0" src="https://github.com/user-attachments/assets/918f9261-b607-4d85-8e4c-76f633bf0a65" />

Created an Active Directory Diagram in Microsoft Visio to help visualize this homelab. 

The Windows Server 2019 VM functions as the Domain Controller (DC) for the internal network, providing centralized authentication, identity management, and network services. Both the Domain Controller and the Windows 10 VM / Client reside on the same virtual internal network.

On the Domain Controller, several core enterprise services are configured to replicate a real-world Active Directory environment:

- Active Directory Domain Services (AD DS) for domain identity and authentication.

- DNS for domain name resolution.

- DHCP for dynamically assigning IP addresses to domain-connected hosts.

- NAT / Routing to provide controlled internet access to internal hosts.

The Windows 10 VM / Client acts as a company-owned device that relies entirely on the Domain Controller for network connectivity, authentication, and configuration. This mirrors how enterprise systems operate within a centralized domain environment. 

The Domain Controller also handles user and computer account creation, management, and distribution across the domain, ensuring that all domain-joined devices use the same centrally enforced identities, permissions, and policies.

#### Important Note for Routing and Remote Access (RRAS) on a Domain Controller

Configuring RRAS on a Domain Controller to route internal network traffic to the Internet is not recommended in real-world or production environments. Domain Controllers are security-critical systems, and exposing them to routing functions increases the attack surface, complicates hardening, and violates best-practice network design. Ideally, a dedicated router/firewall VM would act as the gateway for the Windows 10 Client VM to access the Internet.

However, because our homelab setup looks like this:

Internet
     -->
Host PC
     -->
VirtualBox NAT (for Windows Server 2019 / Domain Controller only)
     -->
Windows Server 2019 / Domain Controller (NAT + Internal Network)
     -->
Internal Network (isolated)
     -->
Windows 10 VM / Client

And because I did not configure port forwarding:
- Outbound traffic from our Windows Server 2019 VM / Domain Controller and Windows 10 VM / Client is allowed.
- Only inbound **response** traffic (for requests the Windows Server 2019 VM / Domain Controller and Windows 10 VM / Client initiated) is allowed.

Thus, for the purposes of this homelab demonstration, this setup is acceptable. The goal here is strictly to showcase and develop a strong foundational understanding of the objectives stated at the top of this README.md.
***

**1. Oracle VirtualBox: Windows Server 2019 VM Configuration**

I created and configured my Windows Server 2019 VM to have two different adapters. Adapter 1 has NAT for internet access and Adapter 2 has an Internal Network for the Windows 10 VM / Client.

<img width="761" height="469" alt="1" src="https://github.com/user-attachments/assets/90fa2d58-9d5b-4cd6-b7f2-06180bc3adae" />
<img width="758" height="467" alt="1 1" src="https://github.com/user-attachments/assets/c1eec9bb-8384-48b4-b30b-b936a86127d3" />

***

**2. Windows Server 2019 VM / Domain Controller: Manual Networking Configuration**

I renamed Adapter 1 (NAT) to INTERNET and Adapter 2 (Internal Network) to X_Internal_X. Assigned Adapter 2 (Internal Network) its own unique IP address, Subnet mask, and Preferred DNS server.

<img width="795" height="652" alt="2" src="https://github.com/user-attachments/assets/73b9416f-b20c-4a8d-b56a-11f770809590" />

***

**3. Windows Server 2019 VM / Domain Controller: Domain Controller Necessities Installation**

I opened Server Manager and proceeded to install Active Directory Domain Services, DHCP Server, DNS Server, and Remote Access. I additionally installed .NET Framework 3.5 and 4.7 in the Features section to ensure compatibility.

<img width="1025" height="869" alt="3" src="https://github.com/user-attachments/assets/bdbd71ec-47a1-491c-a1cb-69f36a3c8378" />

***

**4. Windows Server 2019 VM / Domain Controller: Configuring the VM as the Domain Controller**

After all installations were completed, I promoted the server to the Domain Controller, set the root domain name to mydomain.com, and created its password. Restarted the VM.

<img width="531" height="389" alt="4" src="https://github.com/user-attachments/assets/73a68c8b-c7b8-419f-83b4-44f05b9b510c" />

***

**5. Windows Server 2019 VM / Domain Controller: Organizational Unit and Admin User Account Creation**

I logged back in as the newly installed Domain Controller administrator account to create a new Organizational Unit called _ADMINS and create my own specific administrator user account.

<img width="1022" height="909" alt="5" src="https://github.com/user-attachments/assets/8689f62c-af35-4ef2-83a4-ff362a562e10" />
<img width="876" height="619" alt="5 1" src="https://github.com/user-attachments/assets/6cbe621e-9e87-44bc-8b70-622260b99533" />
<img width="884" height="675" alt="5 2" src="https://github.com/user-attachments/assets/1de10d0b-eb29-4aca-a58b-e657d67b0515" />

**(_ADMINS and _USERS are already there due to me completing the homelab beforehand)**

***

**6. Windows Server 2019 VM / Domain Controller: Configuring Routing and Remote Access (RRAS)**

I configured RRAS to allow devices in the internal network to connect to the internet through the Windows Server 2019 VM/our Domain Controller.

<img width="1023" height="711" alt="6 1 1" src="https://github.com/user-attachments/assets/2285446f-b7ba-4134-9e3f-f11a39020f93" />
<img width="434" height="309" alt="6" src="https://github.com/user-attachments/assets/637cbf7b-8b10-4d43-957e-fe85db08de13" /><br>
<img width="435" height="307" alt="6 1" src="https://github.com/user-attachments/assets/5a1ec17b-cb03-49db-9076-12af3907d5cb" /><br>
<img width="435" height="308" alt="6 2" src="https://github.com/user-attachments/assets/7ca1b3a0-41b9-4c41-961c-9c77be641893" /><br>
<img width="434" height="309" alt="6 3" src="https://github.com/user-attachments/assets/3dba1a7c-7d85-46cd-aab0-43a38a27c077" /><br>
<img width="435" height="308" alt="6 4" src="https://github.com/user-attachments/assets/0bf2a938-fe14-4a58-9020-e9e9721cf0bc" /><br>

***

**7. Windows Server 2019 VM / Domain Controller: Configuring Dynamic Host Configuration Protocol (DHCP)**

I configured DHCP following the values in the Active Directory Diagram to allow devices in the internal network to automatically be assigned their own IP address when connecting to the internet through the Windows Server 2019 VM/our Domain Controller.

<img width="1020" height="289" alt="7" src="https://github.com/user-attachments/assets/e4411bd8-a47d-462f-99c0-631aba51802f" />
<img width="804" height="343" alt="7 1" src="https://github.com/user-attachments/assets/fd041409-0edd-4350-94f3-e9fbf8fe7d05" />
<img width="614" height="431" alt="7 2" src="https://github.com/user-attachments/assets/ec49c358-9e0a-4434-873d-5266594127c7" />
<img width="615" height="426" alt="7 3" src="https://github.com/user-attachments/assets/5fc2d5ed-cf2c-4da8-bbec-0984970a90fa" />
<img width="615" height="428" alt="7 4" src="https://github.com/user-attachments/assets/29f702b8-b20c-4735-8e4e-3d34495c910c" />
<img width="615" height="429" alt="7 5" src="https://github.com/user-attachments/assets/adbb7a1c-f265-4d42-a5ed-f1cbb5de22a2" />
<img width="613" height="429" alt="7 6" src="https://github.com/user-attachments/assets/b81e742d-10b6-416f-8729-c1467813ba73" />
<img width="614" height="428" alt="7 7" src="https://github.com/user-attachments/assets/8b5daa15-a00f-4f7d-b57b-325280658e9e" />
<img width="615" height="429" alt="7 8" src="https://github.com/user-attachments/assets/ff6e5585-8a96-4ce6-8420-b2140c610903" />
<img width="615" height="427" alt="7 9" src="https://github.com/user-attachments/assets/124d2526-5e61-430c-b099-2c1f4f7f2b52" />
<img width="807" height="271" alt="7 10" src="https://github.com/user-attachments/assets/7acedb63-e676-4cec-ab9b-1d8d7f3f7b42" />

***

**8. Windows Server 2019 VM / Domain Controller: Deploying Example User List and PowerShell Automation Script**

I downloaded an example list of 100s of users called names.txt and the PowerShell automation script called 1_CREATE_USERS to automate the user account creation process from https://github.com/joshmadakor1/AD_PS. Added my name to the top of the list in names.txt to create a non-admin user account for myself. Opened PowerShell as Administrator and ran the PowerShell automation script directly in PowerShell.

<img width="1024" height="908" alt="8" src="https://github.com/user-attachments/assets/aef25198-b215-456a-bf40-2bddf0a88e25" />
<img width="751" height="515" alt="8 1" src="https://github.com/user-attachments/assets/1fd4fab4-b6d4-464b-9e60-1195ebdf46ba" />
<img width="1026" height="909" alt="8 2" src="https://github.com/user-attachments/assets/6bf7088f-8d29-47e1-93e2-db84f4fae7c7" />
<img width="905" height="634" alt="8 3" src="https://github.com/user-attachments/assets/a5bccd1c-1cee-4b79-b6ea-36f51c4a5319" />
<img width="904" height="633" alt="8 4" src="https://github.com/user-attachments/assets/0e6a8819-19da-4e67-8a94-2e82d05bedca" />

### PowerShell Script Explained

`$PASSWORD_FOR_USERS = "Password1"` sets the password for all users.

`$USER_FIRST_LAST_LIST = Get-Content .\names.txt` reads all the names from the names.txt file.

`$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force` converts the plain text password into a secure version that AD will accept because Active Directory requires passwords to be in a SecureString format.

`New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false` creates a new OU named _USERS (seen earlier) to store created users in. Also disables protection so you can easily remove it later if needed.

`foreach ($n in $USER_FIRST_LAST_LIST) {` starts a loop that processes each name from the file one user at a time.

`$first = $n.Split(" ")[0].ToLower()`  
`$last  = $n.Split(" ")[1].ToLower()` splits each line into two parts (first and last name) and converts both to lowercase for consistent usernames.

`$username = "$($first.Substring(0,1))$($last)".ToLower()` builds the username using the format: first initial + last name.

`Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan` prints onto PowerShell, indicating which user is currently being created when the script is run. This is helpful for debugging or tracking progress.

`New-AdUser -AccountPassword $password `
           -`GivenName $first `
           -`Surname $last `
           -`DisplayName $username `
           -`Name $username `
           -`EmployeeID $username `
           -`PasswordNeverExpires $true `
           -`Path "ou=_USERS,$(([ADSI]"").distinguishedName)" `
           -`Enabled $true `

creates a new AD user with:

- Password: the secure password defined earlier.

- First & Last Name: pulled from the text file.

- Username / DisplayName / EmployeeID: set to the generated username.

- Password never expires: useful for a homelab.

- Path: places the account in the _USERS OU. The ADSI call ensures the script dynamically detects the domain’s distinguished name (DN), no matter what domain is used (e.g., dc=homelab,dc=local).

- Enabled: immediately activates the account.

<img width="905" height="634" alt="8 5" src="https://github.com/user-attachments/assets/dc006531-0ac9-40aa-a5d4-de64ce826f2b" />
<img width="905" height="634" alt="8 6" src="https://github.com/user-attachments/assets/b7d73d7a-efb9-41eb-8918-142cefe51d4c" />
<img width="904" height="634" alt="8 7" src="https://github.com/user-attachments/assets/3c93a5ff-b9c3-4952-a60d-eb5d827d1036" />
<img width="905" height="634" alt="8 8" src="https://github.com/user-attachments/assets/b54ffc17-5f32-4875-81d9-69d0baeef6cf" />

**(Red text indicating ResourceExists is there due to me completing the homelab beforehand)**

***

**9. Windows Server 2019 VM: User Account Creation Verification and Filtering**

I verified the creation of all the user accounts in the Active Directory and additionally filtered for both my accounts within _USERS and mydomain.com.

<img width="882" height="651" alt="9" src="https://github.com/user-attachments/assets/818bda59-153a-4ae3-b218-63861c2c3b91" />
<img width="883" height="651" alt="9 1" src="https://github.com/user-attachments/assets/31c1b6fe-4d7d-4993-b8cb-bd4a94b255f1" />
<img width="883" height="651" alt="9 2" src="https://github.com/user-attachments/assets/a1d7cb35-6767-4b53-a3ad-2f406bdd1f50" />

***

**10. Oracle VirtualBox: Windows 10 Client VM Configuration** 

I created and configured my Windows 10 VM / Client to only use an Internal Network for Adapter 1, isolating it so it can initially communicate only with other machines within the same internal network.

<img width="766" height="474" alt="10" src="https://github.com/user-attachments/assets/facd61bc-2c29-45c9-b4b7-bd70c60e5d08" />

***

**11. Windows 10 VM / Client: DHCP, Outbound, and Inbound Response Verification**

When I created the Windows 10 VM / Client and configured its adapter to Internal Network, it entered the same Internal Network as our Windows 2019 Server VM / Domain Controller. Since I installed and configured Active Directory Domain Services, DHCP Server, DNS Server, and Remote Access, our Windows 10 VM / Client was able to automatically reach out to our Windows 2019 Server VM to request and obtain a unique IP address.

Entered `ipconfig` to verify that our Windows 10 VM / Client has obtained a unique IP address successfully.

Entered `ping www.google.com` to test if outbound requests from our Windows 10 VM / Client and inbound responses from the internet worked successfully.

Entered `ping mydomain.com` to test if outbound requests from our Windows 10 VM / Client and inbound responses from our Windows Server 2019 / Domain Controller worked successfully.

While our Windows 10 VM / Client is successfully communicating in the same internal network as our Windows Server 2019 / Domain Controller, I additionally renamed the Windows 10 VM / Client's Computer Name to CLIENT1 and it's Domain to mydomain.com.

<img width="1022" height="766" alt="11" src="https://github.com/user-attachments/assets/ece99f79-6934-492d-aa94-c27e41dcc7ba" />
<img width="1022" height="767" alt="11 1" src="https://github.com/user-attachments/assets/19d76e94-6ff4-4154-85d2-c4698eb4cbe3" />
<img width="1022" height="766" alt="11 2" src="https://github.com/user-attachments/assets/f47dd292-1ef9-4990-80d3-59a714e1bc4e" />
<img width="1021" height="766" alt="11 3" src="https://github.com/user-attachments/assets/0abbf97e-441d-4cb1-a2dc-428194be3dae" />

***

**12. Windows Server 2019 VM / Domain Controller: CLIENT1.mydomain.com Verification**

I verified from the Domain Controller side that DHCP was working and the Windows 10 Client VM is listed as a device in the Active Directory with the correct values I configured (172.16.0.100-200) as shown in the Active Directory Diagram.

<img width="1020" height="289" alt="7" src="https://github.com/user-attachments/assets/af280646-d517-4c44-a36b-22f3567bdcd1" />
<img width="971" height="726" alt="12" src="https://github.com/user-attachments/assets/e228345f-4e0b-4f63-a6ae-9067f24c2398" />

***

**13. Windows 10 VM / Client: Logging in With Provisioned Active Directory Accounts**

I can now simulate logging in as a newly onboarded employee (Their name was included in the names.txt list, and their account was automatically created using the PowerShell provisioning script on the Domain Controller). From the Windows 10 client VM, I can now log in as that user on a company-owned device just as it would occur during a real onboarding process.

<img width="1022" height="765" alt="13" src="https://github.com/user-attachments/assets/01b0d9a9-d91b-4265-995d-6d56cb01d42a" />
<img width="1020" height="765" alt="13 1" src="https://github.com/user-attachments/assets/ffa7d122-8708-438c-a876-a895a47c978b" />
<img width="1023" height="765" alt="13 2" src="https://github.com/user-attachments/assets/f63b1629-01e8-411f-bab9-b0f764748371" />
<img width="1023" height="766" alt="13 3" src="https://github.com/user-attachments/assets/1d80e5c8-3379-46b0-9e12-6950b1539298" />

