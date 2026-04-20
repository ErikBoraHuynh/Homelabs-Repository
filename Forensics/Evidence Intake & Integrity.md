# Evidence Intake & Integrity

## Objective

- 

- 

- 

### Skills Learned

-

-

-

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM
-
-
-

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

***

**Conclusion**

This lab highlighted that the first step in any investigation isn't looking at files, but protecting the data. Proving that the usb.dd.001 image matched the GitHub source hash was an important integrity check. It was also interesting to see how much metadata such as the Volume Label is visible through a simple strings command before even mounting the image. This "look but don't touch" approach is the cornerstone of maintaining evidence integrity.
