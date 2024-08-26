




**WAZUH INDEXER** 

**By: Adeen Mumtaz**
**


**Table of Contents**

|**1.	OVERVIEW OF TERMS .........................................................................................................**|3|
| :- | -: |
|**2.	CERTIFICATES CREATION..................................................................................................**|**3** |
|**3.	NODES INSTALLATION......................................................................................................**|**4**|
|**4.	INSTALLING THE WAZUH INDEXER...................................................................................**|**12**|
|**5. 	CONFIGURING THE WAZUH INDEXER……………………...........................................................**|**16**|
|**6.	DEPLOYING CERTIFICATES.................................................................................................**|**23**|
|**7. 	MEMORY LOCKING .............................................................................................................**|**35**|
|**8.           STARTING THE SERVICE .....................................................................................................**||
|**9.           CLUSTER INITIALIZATION....................................................................................................**||

1. **OVERVIEW OF TERMS**

Wazuh Indexer is the backend where all the logs are stored.

The graylog gets the logs from wazuh indexer to check them.

All logs from PC, Router, firewall will be sent to wazuh dashboard from the graylog to indexer.

**Master Node**: the server or pc having wazuh server  is considered the master node. Master node looks after other nodes, assigns new nodes and perform any other functions. If master node is missing the indexer won’t be able to work properly.

Other nodes are configured to take up the place of master node incase master node fails, so it is better to assign multiple nodes the role of master node so they can take up the role when needed

**High Availability:** Besides its performance capabilities, wazuh-indexer nodes can be clustered together to provide high availability.

Nodes Clustering >> For high availability >> using multiplt nodes

Nodes will have the logs.

**Indices And Shards:** Data is organized in indexer in form of indices and shards.** Indexes are unique which allows us to refer to the data when we make some request , it is used for searching data, these are **unique names.**

Its always better to have a new index name for each day so we can refer to it easily instead of one index that stores 5 months long data, it’ll help indedxer search data more quickly

**Shards:** one index may have many shards

1. **CERTIFICATES CREATION**

Download the Wazuh installation assistant and the configuration file.

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.001.png)

Edit **./config.yml** and replace the node names and IP values with the corresponding names and IP addresses.

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.002.png)

Create certificate that will encrypt our logs between clients and wazuh indexer by using command

\# bash ./wazuh-certs-tool.sh –A

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.003.png)

(If we get an error check the ip for server is properly configured in config.yml file) 

The certificates are created. For a multi-node cluster, these certificates need to be later deployed to all Wazuh instances in your cluster. Compress these files.

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.004.png)

Remove the other files and keep only the tar file

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.005.png)

Copy the wazuh-certificates.tar file to all the nodes, including the Wazuh indexer, Wazuh server, and Wazuh dashboard nodes.

1. **NODES INSTALLATION**

Installaing package

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.006.png)

- **Adding the Wazuh repository**

Installing the following packages if missing.

` `# apt-get install gnupg apt-transport-https

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.007.png)

Installing the GPG key.

\# curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.008.png)

Adding the repository.

\# echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.009.png)

Updating the packages information.

\# apt-get update

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.010.png)

1. **INSTALLING THE WAZUH INDEXER**

Install the Wazuh indexer package.

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.011.png)

1. **CONFIGURING THE WAZUH INDEXER**

Navigate to /etc/wazuh-indexer/

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.012.png)

Edit the /etc/wazuh-indexer/opensearch.yml configuration file

***network.host:*** Sets the address of this node for both HTTP and transport traffic. The node will bind to this address and use it as its publish address. Accepts an IP address or a hostname.

The interface the indexer service will be listening on

` `![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.013.png)

Use the same node address set in config.yml to create the SSL certificates.

***node.name:*** Name of the Wazuh indexer node as defined in the ***config.yml*** file.

It is *ubuntu0* for us.

***cluster.initial\_master\_nodes:*** which will be our initial master node.

List of the names of the master-eligible nodes. These names are defined in the ***config.yml*** file. 

It will be *ubunt0* for our case as we are only using single node right now.

***discovery.seed\_hosts:*** List of the addresses of the master-eligible nodes. Each element can be either an IP address or a hostname. You may leave this setting commented if you are configuring the Wazuh indexer as a single node. For multi-node configurations, uncomment this setting and set the IP addresses of each master-eligible node.

Which other servers the wazuh indexer should look for to join the cluster

![ref1]

Changing max local storage to 1 as there is single node we are using.

![ref2]

Save and exit.

1. **DEPLOYING CERTIFICATES**

Run the following commands replacing <indexer-node-name> with the name of the Wazuh indexer node you are configuring as defined in config.yml. *Ubuntu0* for our case. Make a certs directory using command

\# mkdir /etc/wazuh-indexer/certs

move the certs we have created into this directory

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.016.png)

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.017.png)

Make changes to either the name of certs or change the name in opensearch.yml file. I’ll change the name in opensearch.yml file to uniquely identify the certs.

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.018.png)![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.019.png)![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.020.png)

Changing permissions of directories and certs

\# chmod 500 /etc/wazuh-indexer/certs

\# chmod 400 /etc/wazuh-indexer/certs/\*

\# chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.021.png)

Recommended action: If no other Wazuh components are going to be installed on this node, remove the **wazuh-certificates.tar** file by running **rm -f ./wazuh-certificates.tar** to increase security.

1. **MEMORY LOCKING**

A good practice is that to allocate half the memory to the indexer

Lock the memory for explicit use of indexer

\# htop

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.022.png)![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.023.png)

Almost 1.9 G should be allocated to it. We will enter the settings for memory lock in opensearch.yml.

**Set bootstrap.memory\_lock:**

Uncomment or add this line to the /etc/wazuh-indexer/opensearch.yml file:

\# bootstrap.memory\_lock: true

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.024.png)![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.025.png)

**Edit The Limit Of System Resources**

We now need to edit our service file and set LimitMEMLOCK to infinity otherwise the service will not s be able to start

\# nano /usr/lib/systemd/system/wazuh-indexer.service

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.026.png)

Add **LimitMEMLOCK=infinity** in the service file

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.027.png)

Lets edit the jvm.options file and allocate 2 gigs (1.9 was what I decided earlier) of memory to wazuh indexer instead of 1.

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.028.png)

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.029.png)

1. **STARTING THE SERVICE**

Enable and start the Wazuh indexer service.

\# systemctl daemon-reload

\# systemctl enable wazuh-indexer

\# systemctl start wazuh-indexer

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.030.png)

1. **CLUSTER INITIALIZATION**

Run the Wazuh indexer indexer-security-init.sh script on any Wazuh indexer node to load the new certificates information and start the single-node or multi-node cluster.

/usr/share/wazuh-indexer/bin/indexer-security-init.sh

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.031.png)

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.032.png)![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.033.png)

You only have to initialize the **cluster once**, there is no need to run this command on every node.

Checking log file again

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.034.png)![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.035.png)

` `Check if the single-node or multi-node cluster is working correctly.

\# curl –k –u admin:admin https://192.168.198.129:9200/\_cat/nodes?v

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.036.png)

Run the following command to check if confirm that the installation is successful	

\# curl –k –u admin:admin https://192.168.198.129:9200

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.037.png)

![](Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.038.png)

The wazuh indexer installation was a success.

[ref1]: Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.014.png
[ref2]: Aspose.Words.c2d542f7-264c-478c-81f6-ce617acbf394.015.png
