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

- **Isolating the Transaction:** Successfully identified the suspect's computer by filtering for traffic to willselfdestruct.com, confirming the exact IP address used to send the malicious message was 192.168.15.4.

- **Content Recovery:** Utilized TCP stream following on the POST /secure/submit request to recover the "Smoking Gun"—the full text and subject line of the anonymous death threat. The
Subject line the attacker chose was "you can’t find us" and the threat message was "and you can't hide from us. Stop teaching. Start running."

- **Closing the Attribution Gap:** Successfully countered the "it wasn't me" defense by identifying the Digital Leak (cookie TMog=87M63Y06OkWA0556; Mint=87M63Y06OkWA0556; zBT=1 and http://email.about.com/od/anonymousemail/Send_Anonymous_Email_Messages.htm) associated with Johnny Coach that originated from the same IP address during the timeframe of the threat.

***

**Phase 1: Triage (Finding the Transaction)**

I isolate the traffic to the specific "anonymous" website used for the threat.

Entered `wireshark nitroba.pcap &` to launch the wireshark GUI.

Entered `http.host contains "willselfdestruct"` in the wireshark display filter and looked at the Source IP address of the request.

<img width="1920" height="920" alt="Forensics08 1" src="https://github.com/user-attachments/assets/236e2039-5fdc-4ff7-80dc-dfb0b8289894" />

<img width="1920" height="919" alt="Forensics08 1 1" src="https://github.com/user-attachments/assets/39b6e0a9-3f45-4e62-a6f7-3e2cdcad724e" />

***

**Phase 2: The "Surgical" Reconstruction**

Now I will reassemble the fragmented packets to see exactly what was typed into the web form. I found the packet with the info: POST /secure/submit HTTP/1.1. then right clicked and selected Follow > TCP Stream. Red text is data sent by the suspect; Blue text is the server's response. We see the the message content and the suspect's target email address.

<img width="1920" height="920" alt="Forensics08 2" src="https://github.com/user-attachments/assets/43c49512-7abc-4847-9e66-5d44153f2050" />

***

**Phase 3: Closing the Attribution Gap**

We have a computer IP, but Johnny Coach claims someone else used his seat. I need to find a "Digital Leak"—a personal account login from that same IP address during the same session.

Entered `ip.addr == 192.168.15.4 and http.cookie` in the wireshark display filter to look for cookies or login strings associated with the suspect's IP address

<img width="1920" height="920" alt="Forensics08 3" src="https://github.com/user-attachments/assets/4f925a55-e402-4765-8a32-aee6dfcca371" />
