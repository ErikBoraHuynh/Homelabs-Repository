# RegRipper: The Registry Audit (Win7)

### Objective

- **Perform a comprehensive forensic audit** of a Windows 7 system by extracting and analyzing core registry hives to reconstruct system configurations, hardware history, and user activity.

### Skills Learned

- **Windows Registry Forensics:** Gained expertise in parsing the SYSTEM, SOFTWARE, and NTUSER.DAT hives to uncover hidden system states and user-specific artifacts.

- **Automated Artifact Extraction:** Leveraged RegRipper plugins to efficiently target and extract specific forensic data points without manual hex carving.

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM
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

img

***

**Phase 1: System-Wide Audit (Machine Info)**

I'll use rip.pl to pull system data. I'll also use the > symbol to save our results into text files so we can read them easily in the next step.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p winver > ~/Desktop/Lab05_Results/os_info.txt` to check the OS Type and Version.

Entered `rip.pl -r Windows/System32/config/SYSTEM -p timezone > ~/Desktop/Lab05_Results/time_config.txt` to check the Timezone Settings.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p networkcards > ~/Desktop/Lab05_Results/network_info.txt` to identify the Network Cards.

img

***

**Phase 2: User & Hardware Attribution**

We need to map the users on the system and find the unique serial numbers of physical USB devices.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p profilelist > ~/Desktop/Lab05_Results/users_sids.txt` to list all User Profiles and SIDs.

Entered `rip.pl -r Windows/System32/config/SYSTEM -p usbstor > ~/Desktop/Lab05_Results/usb_history.txt` to audit USB Mass Storage History.

img

***

**Phase 3: Behavioral Analysis (Dug's Activities)**

Now I'll look at the suspect's personal NTUSER.DAT hive found in his user directory.

Entered `rip.pl -r Users/Dug/NTUSER.DAT -p typedurls > ~/Desktop/Lab05_Results/browser_intent.txt` to check Typed URLs (Browsing Intent).

Entered `rip.pl -r Users/Dug/NTUSER.DAT -p recentdocs > ~/Desktop/Lab05_Results/file_access.txt` to check Recent Documents (.txt files).

img

***

**Phase 4: Reading the Evidence**

Entered `cat ~/Desktop/Lab05_Results/X` and `grep -i "Dug" ~/Desktop/Lab05_Results/X`to open each previously created .txt file.

- The Windows 7 computer is Enterprise Service Pack 1 and it was installed in 2016-02-25 14:48:49Z according to the os_info.txt file.

- The Time Zone is Eastern Standard Timeard Time and the ActiceTimeBias is 240 minutes according to the time_config.txt file.

- The brand/model of the Wireless Network Card is Intel(R) PRO/Wireless 3945ABG Network Connection according to the network_info.txt file.

- The SID S-1-5-21-1243504476-1526258261-327839578-1001 belongs to the user “Dug” according to the users_sids.txt file.

- The SanDisk device’s Serial Number is 0123456789ABCDEF&0 according to the usb_history.txt file.

- URLS that Dug manually typed into Internet Explorer according to the browser_intent.txt file include: http://reddit.com/and http://go.microsoft.com/fwlink/?LinkId=69157

- Dug opened “best.txt” and “note.txt” according to the file_access.txt file.

Entered `rip.pl -r Windows/System32/config/SOFTWARE -p uninstall` to discover that 7zip was uninstalled from this laptop on 2016-03-18 18:45:24Z.

img

***
