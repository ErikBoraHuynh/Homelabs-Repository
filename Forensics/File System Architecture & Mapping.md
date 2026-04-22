# File System Architecture & Mapping

### Objective

- **To demonstrate the discrepancy between the User View (Active Files) and the Forensic View (Deleted Files)** by utilizing forensic math to manually mount partitions and recover unallocated data that the operating system no longer recognizes.

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

**1. placeholder**

Entered

placeholder

***
