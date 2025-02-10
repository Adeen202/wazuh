## WAZUH DASHBOARD

1.  **INSTALLING WAZUH DASHBOARD**

Install the Wazuh dashboard package.

\# apt-get install debhelper tar curl libcap2-bin #debhelper version 9 or later

\# apt-get -y install wazuh-dashboard

![image](https://github.com/user-attachments/assets/bd59b9df-e530-4023-854f-f164b52907ce)

![image](https://github.com/user-attachments/assets/2727bcb7-0d80-486a-8b0d-27e8fe1aaaf4)

2.  **CONFIGURING WAZUH DASHBOARD**

Configuring the Wazuh dashboard

Edit the /etc/wazuh-dashboard/opensearch\_dashboards.yml

![image](https://github.com/user-attachments/assets/4afb0dcf-1009-4748-9662-892905a65d03)

3.  **DEPLOYING CERTIFICATES**

\# NODE\_NAME=ubuntu0

\# mkdir /etc/wazuh-dashboard/certs

\# tar -xf ./wazuh-certificates.tar -C /etc/wazuh-dashboard/certs/ ./$NODE\_NAME.pem ./$NODE\_NAME-key.pem ./root-ca.pem

\# nano /etc/wazuh-dashboard/opensearch\_dashboards.yml

\# chmod 500 /etc/wazuh-dashboard/certs

\# chmod 400 /etc/wazuh-dashboard/certs/\*

\# chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs

![image](https://github.com/user-attachments/assets/826c13d7-0fce-42ac-a584-8b115b06f109)

4.  **STARTING THE SERVICE**

\# systemctl daemon-reload

\# systemctl enable wazuh-dashboard

\# systemctl start wazuh-dashboard

![image](https://github.com/user-attachments/assets/ada63709-0814-4256-a5d8-5129b57c9a4a)

Navigate to the wazuh dashboard in browser

![image](https://github.com/user-attachments/assets/9bddf2ba-1ddb-4cfa-919c-4404de334a78)

Login via admin:admin

![image](https://github.com/user-attachments/assets/f1967671-3e01-405e-9044-4bc4234f550b)


Use the Wazuh passwords tool to change all the internal users' passwords.

\# /usr/share/wazuh-indexer/plugins/opensearch-security/tools/wazuh-passwords-tool.sh --change-all --admin-user wazuh --admin-password wazuh

**WAZUH AGENT**

1.  **DEPLOTING NEW AGENT**

Go to wazuh dashboard >> agent and then deploy a new agent.

#### Linux Agent

![image](https://github.com/user-attachments/assets/b7c1c1b1-09ec-4432-a304-345f47ca0e3b)


2.  **RUN THE COMMAND ON AGENT**

\# wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent\_4.8.0-1\_amd64.deb && sudo WAZUH\_MANAGER='192.168.198.129' WAZUH\_AGENT\_NAME='kaliBB' dpkg -i ./wazuh-agent\_4.8.0-1\_amd64.deb

3.  **START SERVICE ON AGENT**

\# sudo systemctl daemon-reload

\# sudo systemctl enable wazuh-agent

\# sudo systemctl start wazuh-agent

![image](https://github.com/user-attachments/assets/ae98ffbc-2b2f-4058-a531-1c32617a4ca9)

![image](https://github.com/user-attachments/assets/ceaedd5a-ceec-4172-962f-e2334e87eabc)


Check manager ip in agent’s /var/ossec/etc/ossec.conf

![image](https://github.com/user-attachments/assets/16231705-23d3-473f-9d85-fdeabbfb6ce9)

#### Windows Agent

![image](https://github.com/user-attachments/assets/278b3114-a292-4af3-8f6d-349d6badb7a1)


