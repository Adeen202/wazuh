## WAZUH MANAGER

![image](https://github.com/user-attachments/assets/859b0e6c-a154-405f-b846-072f84221db9)


1.  **INSTALLING WAZUH MANAGER**

\# apt-get -y install wazuh-manage

![image](https://github.com/user-attachments/assets/d86981ae-d9f2-4ec3-8b75-72d4b9a4317f)

2.  **INSTALLING FILEBEAT**

#### FileBeat
Filebeat is a lightweight, open-source shipper for forwarding and centralizing log data. It's part of the Elastic Stack (formerly known as the ELK Stack), which also includes Elasticsearch, Logstash, and Kibana. Filebeat is designed to monitor and collect log files from various sources and forward them to a destination.
Here Filebeat will be integrated with Wazuh for log collection and forwarding, enabling efficient monitoring and analysis of security events.

\# apt-get -y install filebeat

![image](https://github.com/user-attachments/assets/4bd16303-309c-4083-bc6c-642a62dec887)

3.  **CONFIGURING FILEBEAT**

Download the preconfigured Filebeat configuration file.

\# curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.8/tpl/wazuh/filebeat/filebeat.yml

Edit the **/etc/filebeat/filebeat.yml** configuration file

![image](https://github.com/user-attachments/assets/198c6691-da39-4a0e-bf44-481acdfd11c0)

Create a Filebeat keystore to securely store authentication credentials.

\# filebeat keystore create

Add the default username and password admin:admin to the secrets keystore.

\# echo admin | filebeat keystore add username --stdin –force

\# echo admin | filebeat keystore add password --stdin –force

![image](https://github.com/user-attachments/assets/f184ce02-92d5-4d80-bbf9-6d3109e1b514)

Download the alerts template for the Wazuh indexer.

\# curl -so /etc/filebeat/wazuh-template.json [https://raw.githubusercontent.com/wazuh/wazuh/v4.8.0/extensions/elasticsearch/7.x/wazuh-template.json](https://raw.githubusercontent.com/wazuh/wazuh/v4.8.0/extensions/elasticsearch/7.x/wazuh-template.json)

\# chmod go+r /etc/filebeat/wazuh-template.json

Install the Wazuh module for Filebeat.

\# curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.4.tar.gz | tar -xvz -C /usr/share/filebeat/module

![image](https://github.com/user-attachments/assets/4d907814-5694-4030-8f50-001b3d19bc90)

4.  **DEPLOYING CERTIFICATES**

Replace <SERVER\_NODE\_NAME> with your Wazuh server node certificate name, the same one used in config.yml when creating the certificates. Then, move the certificates to their corresponding location.

\# NODE\_NAME=ubuntu0

\# mkdir /etc/filebeat/certs

\# tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./$NODE\_NAME.pem ./$NODE\_NAME-key.pem ./root-ca.pem

![image](https://github.com/user-attachments/assets/05363ead-402a-4665-934c-0b455f67c31d)

Edit filebeat.yml file

![image](https://github.com/user-attachments/assets/4ec399a6-34dc-4cf0-bd6c-ab2242879d4c)

Ensure the Filebeat certificate and key name match the certificate files in /etc/filebeat/certs.

\# chmod 500 /etc/filebeat/certs

\# chmod 400 /etc/filebeat/certs/\*

\# chown -R root:root /etc/filebeat/certs

5.  **CONFIGURING**

Edit /var/ossec/etc/ossec.conf to configure the indexer connection.

Change the ip and the certs name as well

![image](https://github.com/user-attachments/assets/61764468-fd27-4530-ba4f-b23365ace4bb)

6.  **STARTING THE MANAGER AND FILEBEAT**

\# systemctl daemon-reload

\# systemctl enable wazuh-manager

\# systemctl start wazuh-manager

![image](https://github.com/user-attachments/assets/689e54cc-3da0-4ef3-9f11-c2af78eed4e8)

\# systemctl daemon-reload

\# systemctl enable filebeat

\# systemctl start filebeat

Filebeat test output:

![image](https://github.com/user-attachments/assets/5c181ce1-952b-4a5e-bd1f-c2714d93e2e6)
