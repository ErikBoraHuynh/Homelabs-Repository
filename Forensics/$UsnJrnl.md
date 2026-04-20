# $UsnJrnl: Replaying the Security Tape

### Objective

- Utilize the $UsnJrnl within a SANS SIFT environment to track the lifecycle of a file, identifying that a sensitive image "surveil-sourhside06.JPG" was renamed to a benign spreadsheet called "billing1.ods" to evade detection.

- Prove file continuity by matching the immutable File Reference Number (File ID), demonstrating how low-level NTFS artifacts serve as a forensic "barcode" that remains unchanged regardless of user-facing filename modifications.

### Skills Learned

- **NTFS Artifact Analysis:** Gained hands-on experience parsing the $UsnJrnl (Change Journal) to track file system modifications and reconstruct the lifecycle of specific files.

- **Metadata Correlation:** Developed the ability to match File Reference Numbers (File IDs) to identify renamed or "hidden" files, bypassing surface-level user deception to verify data identity.

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM
- New Technology File System (NTFS) $UsnJrnl

### Findings

- **Immutable File Identity:** Confirmed that the File Reference Number (File ID 6017) acts as a permanent "barcode" for data; while the filename was altered to evade detection, the underlying NTFS metadata remained constant, proving file continuity.

- **Anti-Forensic Detection:** Successfully tracked the transition of a sensitive image file (surveil-sourhside06.JPG) being renamed to a benign document (billing1.ods), a common tactic used by bad actors to hide illicit data in plain sight.

- **Temporal Precision:** Leveraged the $UsnJrnl (Change Journal) to pinpoint the exact moment of the filename change at Unix Epoch 1665596185, which correlates to Wed Oct 12 17:36:25 UTC 2022.

- **Artifact Reconstruction:** Demonstrated the ability to transform raw, binary $J stream data into a human-readable chronological report using usnj.pl, effectively "replaying" the file system's history.

- **The "Honest Witness" Principle:** This lab reinforced that user-facing changes (renaming, moving) are superficial; the $UsnJrnl serves as an objective ledger that captures intent and action, making it an essential artifact for incident response and digital forensics.

***

**Phase 0: Environment & Evidence Setup**

Entered `mkdir -p evidencevault && cd evidencevault` to create and enter the vault directory for this home lab.

Entered `wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=1wYYKasdmBShQ4TtYahZWmHLdByyJyDDu' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1wYYKasdmBShQ4TtYahZWmHLdByyJyDDu" -O evidence.E01` to download the evidence.E01 file into the vault.

Entered `md5sum evidence.E01` to verify the integrity of our downloaded evidence.E01 file.

<img width="1920" height="918" alt="USN 1" src="https://github.com/user-attachments/assets/04ec15da-6439-47ad-864d-ea15c29d71d3" />

***

**Phase 1: Autopsy Case Initialization**

Entered `sudo autopsy &` to launch the forensic browser Autospy and load our case.

We then:
1. **Terminal:** Typed sudo autopsy & and pressed Enter.
2. **Browser:** Opened Firefox and navigated to the address shown in the terminal (usually http://localhost:9999/autopsy).
3. **New Case:** Clicked New Case. Named it Lab06_Audit. Clicked New Case.
4. **Add Host:** Clicked Add Host. Named it Workstation_01. Clicked Add Host.
5. **Add Image:** Clicked Add Image File. Choose Path: /home/sansforensics/evidencevault/evidence.E01. Choose Type: Disk. Import Method: Symlink.
6. **The Volume Trap:** Clicked NEXT. When prompted, selected Volume Image and ntfs from the dropdown. Clicked Analyze.

<img width="1920" height="918" alt="USN 2" src="https://github.com/user-attachments/assets/46d51d06-8034-4889-b629-f2187a7bf53b" />
<img width="1920" height="919" alt="USN 3" src="https://github.com/user-attachments/assets/49b3044c-c641-49e8-ab3c-05c66e6d8fc6" />
<img width="1920" height="919" alt="USN 4" src="https://github.com/user-attachments/assets/6d290f29-c28d-4b3d-9179-2c60f16e843c" />
<img width="1920" height="919" alt="USN 5" src="https://github.com/user-attachments/assets/bf8ca8e1-d94b-447e-bb7d-585d5fd362f5" />
<img width="1920" height="917" alt="USN 6" src="https://github.com/user-attachments/assets/35867d21-b497-4edf-a5cd-231a7a6e650e" />
<img width="1920" height="919" alt="USN 7" src="https://github.com/user-attachments/assets/328be7a3-cec0-4755-8fea-24e5b890aa20" />
<img width="1920" height="918" alt="USN 8" src="https://github.com/user-attachments/assets/42300edf-6e9b-4dff-959c-672cfb27c831" />
<img width="1920" height="918" alt="USN 9" src="https://github.com/user-attachments/assets/c49ddf61-b376-43c8-b13c-40acc0ec210e" />
<img width="1920" height="919" alt="USN 10" src="https://github.com/user-attachments/assets/4443276e-c858-4fd0-b0f9-ed4efbb6fb96" />

***

**Phase 2: Extracting the $J Stream**

After clicking Analyze, I navigated to the $Extend directory and found $UsnJrnl. I then located the $J attribute (the primary data stream), exported, and saved it as usn_tape.bin on my SANS Workstation VM Desktop.

<img width="1920" height="920" alt="USN 11" src="https://github.com/user-attachments/assets/2ef30093-437a-4d4e-8bf0-8c622e29eac2" />
<img width="1920" height="919" alt="USN 12" src="https://github.com/user-attachments/assets/e0d62ee3-1f27-4186-a9d4-de5ad1913570" />
<img width="1920" height="920" alt="USN 13" src="https://github.com/user-attachments/assets/93f13573-b7e5-4045-b46e-7316a83fdb5d" />
<img width="1920" height="920" alt="USN 14" src="https://github.com/user-attachments/assets/3d2f08f7-da62-4b83-982d-cada4ff8abb8" />

***

**Phase 3: Replaying the Tape**

Entered `usnj.pl -f usn_tape.bin -t > usn_report.txt` to transform the raw, binary file system data into a human-readable format that we can actually analyze. The command runs a Perl script (usnj.pl) commonly found in the SANS SIFT toolkit to parse the usn_tape.bin (Change Journal) we extracted.

<img width="1920" height="919" alt="USN 15" src="https://github.com/user-attachments/assets/ea93152d-423c-487c-8e01-00f2dbc79d4e" />


***

**Phase 4: Connecting the Barcodes**

After opening usn_report.txt, we Ctrl-F to find the "surveil-sourhside06.JPG" file that a suspect claimed they had never seen before. We find it's File Reference Number 6017 and we Ctrl-F it as well to see all it's logs. We find that at the Unix Epoch Timestamp of 1665596185, it was renamed "billing1.ods". We enter `date -d @1665596185` to get the exact timestamp (UTC) of when it was renamed, which is Wed Oct 12 17:36:25 UTC 2022.

<img width="1920" height="918" alt="USN 16" src="https://github.com/user-attachments/assets/5593f3ee-5360-4fd4-9abb-27ef241a5c0b" />
<img width="1920" height="920" alt="USN 17" src="https://github.com/user-attachments/assets/f470f520-f900-494b-9502-6622dbd2c9fb" />
<img width="1920" height="920" alt="USN 18" src="https://github.com/user-attachments/assets/c15105d3-a4dd-4f3a-9848-0ff6641ab7da" />
