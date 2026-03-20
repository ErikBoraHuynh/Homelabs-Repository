# $UsnJrnl: Replaying the Security Tape

## Objective

- Utilize the $UsnJrnl within a SANS SIFT environment to track the lifecycle of a file, identifying that a sensitive image "surveil-sourhside06.JPG" was renamed to a benign spreadsheet called "billing1.ods" to evade detection.

- Prove file continuity by matching the immutable File Reference Number (File ID), demonstrating how low-level NTFS artifacts serve as a forensic "barcode" that remains unchanged regardless of user-facing filename modifications.


### Skills Learned

- **NTFS Artifact Analysis:** Gained hands-on experience parsing the $UsnJrnl (Change Journal) to track file system modifications and reconstruct the lifecycle of specific files.

- **Metadata Correlation:** Developed the ability to match File Reference Numbers (File IDs) to identify renamed or "hidden" files, bypassing surface-level user deception to verify data identity.

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM
- 

***

**Phase 0: Environment & Evidence Setup**

text

img

***

**Phase 1: Autopsy Case Initialization**

text

img

***

**Phase 2: Extracting the $J Stream**

text

img

***

**Phase 3: Replaying the Tape**

text

img

***

**Phase 4: Connecting the Barcodes**

text

img

***
