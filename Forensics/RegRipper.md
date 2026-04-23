# RegRipper: The Registry Audit (Win7)

### Objective

- **Perform a comprehensive forensic audit** on the laptop of a suspect named "Dug." by extracting and analyzing core registry hives to reconstruct system configurations, hardware history, and user activity.

### Skills Learned

- **Windows Registry Forensics:** Gained expertise in parsing the SYSTEM, SOFTWARE, and NTUSER.DAT hives to uncover hidden system states and user-specific artifacts.

- **Automated Artifact Extraction:** Leveraged RegRipper plugins to efficiently target and extract specific forensic data points without manual hex carving.

### Tools Used

- Introduction to Forensics Ubuntu Workstation VM
- RegRipper (rip.pl)

### Findings

- **Hardware Connection Trail:** Audited the USBSTOR registry key to identify the specific hardware serial numbers of USB devices that had been physically connected to the laptop.

- **Intent-Based Evidence:** Discovered "Typed URLs" in the suspect’s personal registry hive, providing direct evidence of specific websites accessed through intentional manual input rather than background redirects.

- **Evidence of File Interaction:** Reconstructed a timeline of recently accessed documents using the RecentDocs plugin, confirming that the suspect interacted with specific .txt files during the period of interest.

***

**Phase 0: Environment Setup & Decompression**

The Windows 7 evidence is currently "locked" inside a compressed file. I must extract it into its original folder structure before I can analyze the hives.

Entered `cd ~/Desktop/cases/01_Filesystems/` to navigate to the Cases folder.

Entered `tar -xzvf Win7_Laptop.tar.gz` to extract (Unzip) the Win7 Laptop image.

Entered `cd Win7_Laptop/ mkdir -p ~/Desktop/Lab05_Results` to enter the new directory and create an output folder on my Desktop.

<img width="1920" height="920" alt="Forensics05 1" src="https://github.com/user-attachments/assets/7e6b6e67-886e-4d0f-ad40-20ccde78f4e4" />

***

**Phase 1: System-Wide Audit (Machine Info)**

I'll use rip.pl to pull system data. I'll also use the > symbol to save our results into text files so we can read them easily in the next step.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p winver > ~/Desktop/Lab05_Results/os_info.txt` to check the OS Type and Version.

Entered `rip.pl -r Windows/System32/config/SYSTEM -p timezone > ~/Desktop/Lab05_Results/time_config.txt` to check the Timezone Settings.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p networkcards > ~/Desktop/Lab05_Results/network_info.txt` to identify the Network Cards.

<img width="1920" height="920" alt="Forensics05 2" src="https://github.com/user-attachments/assets/464035f0-a661-4a02-a54f-9924e8e9b382" />

<img width="1920" height="919" alt="Forensics05 2 1" src="https://github.com/user-attachments/assets/4d497021-1955-4b75-b1e4-b70a14884a75" />

<img width="1920" height="918" alt="Forensics05 2 2" src="https://github.com/user-attachments/assets/65004528-cf9a-4faa-ac96-b424706d8156" />

***

**Phase 2: User & Hardware Attribution**

We need to map the users on the system and find the unique serial numbers of physical USB devices.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p profilelist > ~/Desktop/Lab05_Results/users_sids.txt` to list all User Profiles and SIDs.

Entered `rip.pl -r Windows/System32/config/SYSTEM -p usbstor > ~/Desktop/Lab05_Results/usb_history.txt` to audit USB Mass Storage History.

<img width="1920" height="918" alt="Forensics05 3" src="https://github.com/user-attachments/assets/ad995578-6328-4c78-a7b4-0661557e74f6" />

<img width="1920" height="919" alt="Forensics05 3 1" src="https://github.com/user-attachments/assets/05febad2-c231-4029-8478-e86dfc7ee983" />

***

**Phase 3: Behavioral Analysis (Dug's Activities)**

Now I'll look at the suspect's personal NTUSER.DAT hive found in his user directory.

Entered `rip.pl -r Users/Dug/NTUSER.DAT -p typedurls > ~/Desktop/Lab05_Results/browser_intent.txt` to check Typed URLs (Browsing Intent).

Entered `rip.pl -r Users/Dug/NTUSER.DAT -p recentdocs > ~/Desktop/Lab05_Results/file_access.txt` to check Recent Documents (.txt files).

<img width="1920" height="920" alt="Forensics05 4" src="https://github.com/user-attachments/assets/4b82bec5-4368-4711-a90e-3fb9596b600c" />

<img width="1920" height="919" alt="Forensics05 4 1" src="https://github.com/user-attachments/assets/eefb46c4-eef9-49c6-9813-0b217d681b3e" />

***

**Phase 4: Reading the Evidence**

The Windows 7 computer is Enterprise Service Pack 1 and it was installed in 2016-02-25 14:48:49Z according to the os_info.txt file.

<img width="1920" height="919" alt="Forensics05 5" src="https://github.com/user-attachments/assets/db3ee8ee-3beb-4da8-9ba3-9dd660825a8a" />

***

The Time Zone is Eastern Standard Timeard Time and the ActiceTimeBias is 240 minutes according to the time_config.txt file.

<img width="1920" height="918" alt="Forensics05 5 1" src="https://github.com/user-attachments/assets/c2b6fb81-8a69-469c-a87f-05ff3da9d61a" />

***

The brand/model of the Wireless Network Card is Intel(R) PRO/Wireless 3945ABG Network Connection according to the network_info.txt file.

<img width="1920" height="919" alt="Forensics05 5 2" src="https://github.com/user-attachments/assets/841422eb-9bcd-4906-8356-d45bfae8a93a" />

***

The SID S-1-5-21-1243504476-1526258261-327839578-1001 belongs to the user “Dug” according to the users_sids.txt file.

<img width="1920" height="920" alt="Forensics05 5 3" src="https://github.com/user-attachments/assets/73b0b9fd-9247-4634-9c88-7287002d5038" />

***

The SanDisk device’s Serial Number is 0123456789ABCDEF&0 according to the usb_history.txt file.

<img width="1920" height="918" alt="Forensics05 5 4" src="https://github.com/user-attachments/assets/2c197227-f97a-4b63-95b2-f1577528b2a6" />

***

URLS that Dug manually typed into Internet Explorer according to the browser_intent.txt file include: http://reddit.com/ and http://go.microsoft.com/fwlink/?LinkId=69157

<img width="1920" height="919" alt="Forensics05 5 5" src="https://github.com/user-attachments/assets/2f960a61-671f-4cb1-a679-8588398e543a" />

***

Dug opened “best.txt” and “note.txt” according to the file_access.txt file.

<img width="1920" height="918" alt="Forensics05 5 6" src="https://github.com/user-attachments/assets/3e772eea-c8bc-4a08-a0fb-a7c607f21cdb" />
