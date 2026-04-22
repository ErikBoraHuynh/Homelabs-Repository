# File System Architecture & Mapping

### Objective

- **Demonstrate the discrepancy between the User View (Active Files) and the Forensic View (Deleted Files)** by utilizing forensic math to manually mount partitions and recover unallocated data that the operating system no longer recognizes.

### Skills Learned

- **Forensic Mathematics:** Calculating partition byte offsets using the formula $\text{Sector Number} \times \text{Sector Size}$ to facilitate manual mounting of disk images.

- **Data Recovery & Carving:** Identifying and restoring "deleted" files by interacting directly with the file system's metadata layer rather than the OS's logical layer.

- **File Signature Analysis:** Performing scientific authentication of recovered data by matching Hex Headers (Magic Numbers) against the Compound File Binary Format to verify file extensions.

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM
- The Sleuth Kit (fls)

### Findings

- **User View vs. Forensic View:** A standard ls -l command only revealed 2 active files, while a forensic fls analysis uncovered a total of 29 files (27 of which were deleted), highlighting the massive gap in visibility for non-forensic users.
  
- **Architectural Discrepancy:** Proved that while standard utilities view disks in bytes, forensic tools utilize Logical Block Addressing (LBA), requiring an offset of $128 \times 512 = 65,536$ bytes to successfully map the target partition.
  
- **Data Overwriting Risks:** Confirmed that even if a file is "deleted," it remains recoverable until the OS overwrites that specific sector with new data (e.g., system logs or temp files), reinforcing the need for immediate forensic imaging during an investigation.


***

**Phase 1: Setup & Partition Mapping**

I see here that the Start Sector number is 128. If I multiply that by the bytes which is 512, I get the byte offset of 65536.

Entered `mkdir -p ~/Desktop/Forensics/Lab04 && cd ~/Desktop/Forensics/Lab04`to create my Investigation Folder.

Entered `mmls /home/sansforensics/Desktop/Forensics/usb_dd/usb.dd.001` to analyze the disk layout.

<img width="1920" height="915" alt="Forensics04 1" src="https://github.com/user-attachments/assets/53ff3c89-68ff-4778-adf3-01a01fc56ac0" />

***

**Phase 2: The User View (Consolidating Active Files)**

I mount the drive using my calculated Byte Offset to see what a standard user sees. I will then copy those files into my investigation folder.

Entered `sudo mkdir -p /mnt/live_view` to create a temporary mount point.

Entered `sudo mount -o loop,offset=65536 /home/sansforensics/Desktop/Forensics/usb_dd/usb.dd.001 /mnt/live_view` to mount using my calculated math of the byte offset 65536.

Entered `cp /mnt/live_view/readme.* ~/Desktop/Forensics/Lab04/` to copy the "active" files to my Lab04 folder.

<img width="1920" height="919" alt="Forensics04 2" src="https://github.com/user-attachments/assets/d9dcd48b-1dc9-45a7-8a80-9f654fe3ee26" />

<img width="1920" height="920" alt="Forensics04 2 1" src="https://github.com/user-attachments/assets/21b51316-97b5-41ba-81d7-3f18ea413c21" />

***

**Phase 3: The Forensic View (Identifying Deleted Data)**

I use a forensic tool from The Sleuth Kit to look into the Master File Table to find data that has no pointers in the visible volume. The asterisk (*) next to a file confirms the file was deleted.

Entered `fls -r -o 128 /home/sansforensics/Desktop/Forensics/usb_dd/usb.dd.001` to triage the raw filesystem using the Start Sector (128).

<img width="1920" height="918" alt="Forensics04 3" src="https://github.com/user-attachments/assets/b617bce7-1d72-4b32-a884-54f95fc8cdb2" />

<img width="1920" height="920" alt="Forensics04 3 1" src="https://github.com/user-attachments/assets/076ccbf3-2166-47ae-9fe3-00451df5e310" />

***

**Phase 4: Extraction & Identification**

I extract the deleted file into our workspace and verify its identity as a .doc file after finding it's Magic Number (0xD0CF11E0) in the Hex Header.

Entered `icat -o 128 /home/sansforensics/Desktop/Forensics/usb_dd/usb.dd.001 88 > ~/Desktop/Forensics/Lab04/rescued_file.doc` to extract Inode 88 directly into your Lab04 folder.

Entered `xxd rescued_file.doc | head -n 1` to view the Hex Header.

<img width="1920" height="919" alt="Forensics04 4" src="https://github.com/user-attachments/assets/ccf6fbea-c6f3-42f5-9fe7-d09d9f6717dd" />

***

**Phase 5: Comparison of Results**

I compare the files visible on the drive volume versus the files now in my workspace.

Entered `ls -l /mnt/live_view` to list the files on the mounted drive.

Entered `ls -l ~/Desktop/Forensics/Lab04` to list the files in my investigation folder.

<img width="1920" height="919" alt="Forensics04 5" src="https://github.com/user-attachments/assets/5dfdf93f-592a-471e-b70b-dcaca95b9e60" />
