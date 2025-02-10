## ENDPOINT SECURITY

![image](https://github.com/user-attachments/assets/86e067cb-c3bf-4bcb-b5c3-2892bd183cf8)

### 1.  FILE INTEGRITY MONITORING

File Integrity Monitoring (FIM) is a security measure that checks if important files on your computer or network have been changed without permission.
It is a security process that validates the integrity of operating system and application files by constantly monitoring and verifying their current state against a known, trusted baseline.
The _primary goal_ of FIM is to **detect unauthorized changes, such as modifications, deletions, or additions to files,** which may indicate potential security breaches, malware infections, or operational errors.
It keeps an eye on these files to make sure they stay the way they should, which helps catch problems like viruses or hacking attempts early.

#### Linux Agent

Edit the Wazuh agent /var/ossec/etc/ossec.conf

\# nano /var/ossec/etc/ossec.conf

Enable the syscheck and add the _directories f_or monitoring within the **_<syscheck>_** block.

Let’s check root directories for now:

<directories check\_all="yes" report\_changes="yes" realtime="yes">/root</directories>

![image](https://github.com/user-attachments/assets/fb476797-24c7-4ac4-a08e-eb4ce6a5e874)

Restart the agent so that changes can take effect

## 2.  Testing The Configuration

Let’s check the alerts of the file integrity monitoring. First generate a file in the directory that has been added to the <syscheck> block i.e root in our case and then alter the file.

Create a file:

![image](https://github.com/user-attachments/assets/f549435d-a841-454f-ba73-5aac035e57ab)

Edit the file:

![image](https://github.com/user-attachments/assets/8b6ff2d0-1561-4f36-81b2-05647ab3c64f)

![image](https://github.com/user-attachments/assets/c16c254e-21b4-4079-9e0e-e423e3a908cb)

Delete the file:

![image](https://github.com/user-attachments/assets/da0a6f70-5f06-4223-843c-983e10e549ac)

Check the alerts in wazuh:

![image](https://github.com/user-attachments/assets/0204abb5-5551-43e5-a973-0e8b090b6d95)

![image](https://github.com/user-attachments/assets/73cc2ec7-0340-496b-96ed-05064ff0879a)

#### Windows Agent

Edit the Wazuh agent C:\\Program Files (x86)\\ossec-agent\\osse.conf file. Enable the syscheck and add the _directories f_or monitoring within the **_<syscheck>_** block. Let’s check desktop directory of user adeen for now:

<directories check\_all="yes" report\_changes="yes" realtime="yes"> C:\\Users\\Adeen\\Desktop </directories>

![image](https://github.com/user-attachments/assets/3f146770-53f4-41ca-99ce-263a2a91a549)

Restart the agent so that changes can take effect.
Let’s check the alerts of the file integrity monitoring. First generate a file in the directory that has been added to the <syscheck> block i.e root in our case and then alter the file.
Create a file on desktop:

![image](https://github.com/user-attachments/assets/bf006523-edac-4ec0-9106-0d581717dcce)

Edit the file:

![image](https://github.com/user-attachments/assets/5071d8b8-a9b4-4051-b2d3-0cf8d74bc141)

Delete the file and check the alerts in wazuh

![image](https://github.com/user-attachments/assets/5ff23d53-6cc6-4893-af2f-93a9703dd5bd)

Wazuh Events:

![image](https://github.com/user-attachments/assets/63e8da31-e332-4523-84fd-c64274eac898)
