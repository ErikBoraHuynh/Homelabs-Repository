# Wireshark: The Invisible Bully

### Objective

- Perform network-based attribution by reconstructing an anonymous threat sent to Lily Tuckrige via WillSelfDestruct.com from a PCAP file and correlate session artifacts such as cookies and personal logins, to link the prime suspect Johnny Coach's identity to the malicious network transaction.

### Skills Learned

- **Packet Triage & Filtering:** Mastering Wireshark display filters (e.g., http.host and ip.addr) to isolate malicious traffic within large network captures.

- **Identity Correlation (The "Digital Leak"):** Linking non-persistent IP addresses to physical users by identifying personal identifiers like session cookies, Gmail addresses, or account IDs leaked in unencrypted traffic.

### Tools Used

- SANS Investigative Forensics Toolkit (SIFT) Workstation VM
- Wireshark

### Findings

- **Isolating the Transaction:** Successfully identified the suspect's computer by filtering for traffic to willselfdestruct.com, confirming the exact IP address used to send the malicious message.

- **Content Recovery:** Utilized TCP stream following on the POST /secure/submit request to recover the "Smoking Gun"—the full text, subject line, and target email address of the anonymous death threat.

- **Closing the Attribution Gap:** Successfully countered the "it wasn't me" defense by identifying a Digital Leak (a personal account login or cookie) associated with Johnny Coach that originated from the same IP address during the timeframe of the threat.

***

placeholder

img

***
