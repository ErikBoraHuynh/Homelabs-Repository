# Evidence Intake & Integrity

### Objective

- Perform a forensically sound acquisition and integrity verification of a bit-for-bit disk image using cryptographic hashing and initial metadata triage within a SIFT environment.

### Skills Learned

- **Integrity Verification:** Utilizing cryptographic hashing (MD5) to validate that evidence has not been altered or corrupted during transit or extraction.

- **Data Triage & Metadata Analysis:** Performing "live" analysis on unmounted disk images to extract human-readable strings and volume labels without altering the file system.

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM

### Findings

- **Prioritizing Data Preservation:** The foundational step of a digital investigation is the protection of evidence; analysis should only begin once a "frozen" state is established and verified.

- **Cryptographic Verification:** Matching the MD5 hash of usb.dd.001 against the source repository confirmed that the evidence remained mathematically identical throughout the intake process.

- **Non-Invasive Triage:** Utilizing the strings command demonstrated that critical metadata, such as the Volume Label, can be extracted from a raw disk image without the risks associated with mounting the file system.

- **"Look But Don't Touch" Methodology:** This lab reinforced the industry standard of maintaining a strict chain of custody and ensuring that the original evidence is never altered during the forensic workflow.

***

### Understanding Bit-for-Bit Replicas

In digital forensics, a bit-for-bit replica (also known as a physical image) is a sector-by-sector duplication of a storage device. Unlike a standard logical copy, where you simply copy-paste visible files, a bit-for-bit replica captures the entire state of the drive including:

**Unallocated Space:** Areas of the disk where files were deleted but the data remains until overwritten.

**Slack Space:** The unused space at the end of a file cluster that can contain remnants of previous data.

**File System Metadata:** Hidden structures that track file permissions, timestamps, and deleted entries.

By using the .dd format in this lab, I ensured that the evidence was a "frozen" snapshot of the original media, allowing for deep analysis without the risk of altering the source data.

***

**1. Acquisition of the Evidence**

Entered `wget https://raw.githubusercontent.com/frankwxu/digital-forensics-lab/main/Basic_Computer_Skills_for_Forensics/dd_image/usb_dd.7z` to download a specific compressed forensic disk image called "dd_image" with an archive file called "usb_dd.7z" from a GitHub repository to my SIFT Workstation VM to simulate working with a bit-for-bit replica of a USB drive (Evidence).

<img width="1920" height="922" alt="Forensics 1" src="https://github.com/user-attachments/assets/e67e47f6-d37c-4b40-bc81-0b07aa7895d4" />

***

**2. Extracting the Evidence**

Entered `7z x usb_dd.7z` to extract the files from our downloaded "usb_dd.7z" archive while preserving its original folder structure and directory paths.

<img width="1920" height="923" alt="Forensics 2" src="https://github.com/user-attachments/assets/34668bd9-8230-421d-8439-f217376b447b" />

***

**3. Ensuring the Evidence's Integrity**

Entered `md5sum usb.dd.001` to calculate the MD5 hash of the disk image to prove that the data has not been altered since the moment we acquired it. We ensure that the hash shown below matches the hash in the original file on GitHub: "0adce985af5dd1e0d3595a50a5a9a427" which it does.

<img width="1920" height="920" alt="Forensics 3" src="https://github.com/user-attachments/assets/c7d8d9d2-8c39-46cf-a412-281089619df8" />

***

**4. Identifying the "Volume Label"**

Our goal here is to quickly identify details such as the name of the drive or the types of files it contains. A Volume Label is the human-readable name of the drive. We can find it in the drive's "Passport" section using the strings command. Entered `strings usb.dd.001 | head -n 20` to display the first 20 human-readable text sequences found within the disk image file.

<img width="1920" height="921" alt="Forensics 4" src="https://github.com/user-attachments/assets/ffbb3cb8-23eb-4fdc-a91c-e1c5689f2173" />
