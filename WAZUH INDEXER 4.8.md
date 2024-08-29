# WAZUH INDEXER

## 1.  OVERVIEW OF TERMS

Wazuh Indexer is the backend where all the logs are stored.The graylog gets the logs from wazuh indexer to check them.All logs from PC, Router, firewall will be sent to wazuh dashboard from the graylog to indexer.

**Master Node**: the server or pc having wazuh server  is considered the master node. Master node looks after other nodes, assigns new nodes and perform any other functions. If master node is missing the indexer won’t be able to work properly.

Other nodes are configured to take up the place of master node incase master node fails, so it is better to assign multiple nodes the role of master node so they can take up the role when needed

**High Availability:** Besides its performance capabilities, wazuh-indexer nodes can be clustered together to provide high availability.

Nodes Clustering >> For high availability >> using multiplt nodes

Nodes will have the logs.

**Indices And Shards:** Data is organized in indexer in form of indices and shards.** Indexes are unique which allows us to refer to the data when we make some request , it is used for searching data, these are **unique names.**

Its always better to have a new index name for each day so we can refer to it easily instead of one index that stores 5 months long data, it’ll help indedxer search data more quickly

**Shards:** one index may have many shards

## 2. CERTIFICATES CREATION

Download the Wazuh installation assistant and the configuration file.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 001](https://github.com/user-attachments/assets/38d76648-0d9e-4bd2-8c18-4adeb663603d)


Edit **./config.yml** and replace the node names and IP values with the corresponding names and IP addresses.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 002](https://github.com/user-attachments/assets/9ca01cd1-32c1-4f84-b3ac-025151396d65)


Create certificate that will encrypt our logs between clients and wazuh indexer by using command

\# bash ./wazuh-certs-tool.sh –A

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 003](https://github.com/user-attachments/assets/f15c9dd4-f2cf-4c2f-ad69-8a85e356308d)


(If we get an error check the ip for server is properly configured in config.yml file) 

The certificates are created. For a multi-node cluster, these certificates need to be later deployed to all Wazuh instances in your cluster. Compress these files.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 004](https://github.com/user-attachments/assets/56a94ba3-fa95-4e23-9511-efaffdcde9a1)


Remove the other files and keep only the tar file

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 005](https://github.com/user-attachments/assets/ce5c972b-2854-41d4-abfb-135393ef2f2c)


Copy the wazuh-certificates.tar file to all the nodes, including the Wazuh indexer, Wazuh server, and Wazuh dashboard nodes.

## 3. NODES INSTALLATION

Installaing package

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 006](https://github.com/user-attachments/assets/78b8dff0-fce5-4abf-a873-c232b56d3e77)


- **Adding the Wazuh repository**

Installing the following packages if missing.

` `# apt-get install gnupg apt-transport-https

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 007](https://github.com/user-attachments/assets/31994c51-af07-4be5-9b92-f4850a4dd93d)


Installing the GPG key.

\# curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 008](https://github.com/user-attachments/assets/32b6f1ea-8577-4a68-8049-f7513b49b846)


Adding the repository.

\# echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 009](https://github.com/user-attachments/assets/6b0f0622-2282-4f7e-ac5e-1798344cbd46)


Updating the packages information.

\# apt-get update

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 010](https://github.com/user-attachments/assets/5ea113bf-9a3d-49c6-bbad-b349c1e145a2)


## 4. INSTALLING THE WAZUH INDEXER

Install the Wazuh indexer package.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 011](https://github.com/user-attachments/assets/37e1ff5f-4495-4982-9fec-75c2bda516b9)


## 5. CONFIGURING THE WAZUH INDEXER

Navigate to /etc/wazuh-indexer/

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 012](https://github.com/user-attachments/assets/813d5dfa-9a3c-4526-8402-ec94f98d9d06)


Edit the /etc/wazuh-indexer/opensearch.yml configuration file

***network.host:*** Sets the address of this node for both HTTP and transport traffic. The node will bind to this address and use it as its publish address. Accepts an IP address or a hostname.

The interface the indexer service will be listening on

`![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 013](https://github.com/user-attachments/assets/157b3d20-3d76-4367-b85d-776a57f2f794)


Use the same node address set in config.yml to create the SSL certificates.

***node.name:*** Name of the Wazuh indexer node as defined in the ***config.yml*** file.

It is *ubuntu0* for us.

***cluster.initial\_master\_nodes:*** which will be our initial master node.

List of the names of the master-eligible nodes. These names are defined in the ***config.yml*** file. 

It will be *ubunt0* for our case as we are only using single node right now.

***discovery.seed\_hosts:*** List of the addresses of the master-eligible nodes. Each element can be either an IP address or a hostname. You may leave this setting commented if you are configuring the Wazuh indexer as a single node. For multi-node configurations, uncomment this setting and set the IP addresses of each master-eligible node.

Which other servers the wazuh indexer should look for to join the cluster

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 014](https://github.com/user-attachments/assets/5fdf4652-78f9-477e-aa28-773ee5c06b94)

Changing max local storage to 1 as there is single node we are using.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 015](https://github.com/user-attachments/assets/fb2cfd42-e537-4287-8734-fe70f6752f74)


Save and exit.

1. **DEPLOYING CERTIFICATES**

Run the following commands replacing <indexer-node-name> with the name of the Wazuh indexer node you are configuring as defined in config.yml. *Ubuntu0* for our case. Make a certs directory using command

\# mkdir /etc/wazuh-indexer/certs

move the certs we have created into this directory

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 016](https://github.com/user-attachments/assets/6dc99491-c6a5-4fe0-bd76-74c67fa7acb0)


![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 017](https://github.com/user-attachments/assets/3b3befea-1a54-4dff-82a7-c4b91024ae31)


Make changes to either the name of certs or change the name in opensearch.yml file. I’ll change the name in opensearch.yml file to uniquely identify the certs.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 020](https://github.com/user-attachments/assets/2780137e-6ce0-4dfc-bee6-0fe705128b5c)


Changing permissions of directories and certs

\# chmod 500 /etc/wazuh-indexer/certs

\# chmod 400 /etc/wazuh-indexer/certs/\*

\# chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 021](https://github.com/user-attachments/assets/0ccacda3-ee68-4c56-9e58-45a54473f81c)


Recommended action: If no other Wazuh components are going to be installed on this node, remove the **wazuh-certificates.tar** file by running **rm -f ./wazuh-certificates.tar** to increase security.

1. **MEMORY LOCKING**

A good practice is that to allocate half the memory to the indexer

Lock the memory for explicit use of indexer

\# htop

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 023](https://github.com/user-attachments/assets/50806b8e-4fec-42c4-9d4d-e80d26a06508)


Almost 1.9 G should be allocated to it. We will enter the settings for memory lock in opensearch.yml.

**Set bootstrap.memory\_lock:**

Uncomment or add this line to the /etc/wazuh-indexer/opensearch.yml file:

\# bootstrap.memory\_lock: true

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 025](https://github.com/user-attachments/assets/f11722f3-b5a5-4a11-a56f-8afdefb4e0d3)


**Edit The Limit Of System Resources**

We now need to edit our service file and set LimitMEMLOCK to infinity otherwise the service will not s be able to start

\# nano /usr/lib/systemd/system/wazuh-indexer.service

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 026](https://github.com/user-attachments/assets/7c40b2b4-8730-456b-8d31-f8ec907dc0da)


Add **LimitMEMLOCK=infinity** in the service file

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 027](https://github.com/user-attachments/assets/307a3f88-9105-47fb-9ae4-2b32d2a0bdda)


Lets edit the jvm.options file and allocate 2 gigs (1.9 was what I decided earlier) of memory to wazuh indexer instead of 1.

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 028](https://github.com/user-attachments/assets/7a1d28f4-0665-4d5b-afb5-3b1fa9c0db11)


![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 029](https://github.com/user-attachments/assets/c0facae6-84f2-462e-92ff-3b9730cdb188)


1. **STARTING THE SERVICE**

Enable and start the Wazuh indexer service.

\# systemctl daemon-reload

\# systemctl enable wazuh-indexer

\# systemctl start wazuh-indexer

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 030](https://github.com/user-attachments/assets/23cc8c5a-7d24-49da-9ab0-fa7142b846c1)


1. **CLUSTER INITIALIZATION**

Run the Wazuh indexer indexer-security-init.sh script on any Wazuh indexer node to load the new certificates information and start the single-node or multi-node cluster.

/usr/share/wazuh-indexer/bin/indexer-security-init.sh

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 031](https://github.com/user-attachments/assets/3ca2f9bc-8be4-400a-ab73-1a9d57a0b5e8)


![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 033](https://github.com/user-attachments/assets/1fdc7f07-debe-43a6-a774-6840eedc84bb)

You only have to initialize the **cluster once**, there is no need to run this command on every node.

Checking log file again

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 035](https://github.com/user-attachments/assets/4eaf30ec-871b-4989-8b84-f5babf8927e4)


` `Check if the single-node or multi-node cluster is working correctly.

\# curl –k –u admin:admin https://192.168.198.129:9200/\_cat/nodes?v

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 036](https://github.com/user-attachments/assets/c3fede22-f104-4bd3-b61f-cae307d8d137)


Run the following command to check if confirm that the installation is successful	

\# curl –k –u admin:admin https://192.168.198.129:9200

![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 037](https://github.com/user-attachments/assets/567c6716-ae30-42eb-a686-4e24e02af5a2)


![Aspose Words c2d542f7-264c-478c-81f6-ce617acbf394 038](https://github.com/user-attachments/assets/3a01781b-e2c0-4bb1-94b5-61156765905b)


The wazuh indexer installation was a success.


