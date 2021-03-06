This ansible playbook is designed for repo based Quobyte installations on nodes with Internet access. The playbook install-quobyte-server will install and configure prerequisites before installing and configuring a Quobyte cluster that can immediately be used for writing data (batteries included).

The Quobyte client will be installed on the storage nodes, additional clients can be installed using the install-quobyte-client playbook by populating the client nodes in the [clients] section of the inventory file.

If you want to scale out your cluster by adding more data servers you can do so by running the playbook scaleout_dataservices.yaml.

Terminology:

The first node in the inventory file is used as a bootstrap node to install and configure the Quobyte cluster.
The first nodes (within the section [cluster]) in the inventory file are also used as registry nodes.
The registry nodes are used for service lookup, it can be compaired to a lightweight DNS server.
Registry nodes like all other nodes have metadata and data services installed and will play an active role in the cluster
You can have as many cluster nodes as you want!

Requirements:

Has CentOS 7.x or Ubuntu 20.x up and running with networking configured.
Requires an Ansible management node or the first node in the Quobyte cluster to have ansible installed.
Has root or sudoers access to all nodes and clients that should be part of the cluster.
Has passwordless key access to all cluster and client nodes.
The host key for all cluster and client nodes has been accepted or checking has been disabled.
Has a root or sudors account configured used to allow management via ansible.


Preparation:

1) All devices (ssd,nvme,hdd) that will be used for Quobyte should be completly wiped or new 
   Please use 'wipefs -av' to clean repurposed devices

2) Edit the inventory file

 a) In section [cluster] put the cluster nodes by name or ip (one per line)
 b) In section [clients] put all Client Nodes by name or ip (one per line) (Cluster nodes already have the client installed)
 b) In section [dataserver] put all additional Dataserver Nodes by name or ip (one per line) 
 

3) Edit ``` vars/quobyte.yaml ```

   Set the variables to a meaningful value for your environment
 a) registry: The DNS CNAME or A record (or IP-Addresses) for service discovery (example: registry.quobyte.local) for the first nodes of the cluster.
 b) metadatadevice / datadevice: Choose, which device will be a dedicated metadata device and which will serve as data device.
 c) mountpoint: Mountpoint name for the Quobyte client (example: /quobyte)
 d) network: Specify the preffered network for traffic, usually this is the physical or logical interface with the most bandwidth
 e) s3_endpoint: dns name for s3 usage
 f) clustername: cluster name
 g) clustersize (optional): Determines the number of nodes within your core-cluster to run registry and metadata services. Counts from 0 -n, can be used if your cluster is smaller than the recommended four nodes.

4) Start Quobyte server installation.

   Within the quobyte-ansible directory run the following command on the Ansible management node or the Bootstrap node of the Quobyte cluster
   ```
   $ ansible-playbook -i inventory install-quobyte-coreservices.yaml
   ```

5) (Optional) Start Quobyte client installation

   Within the quobyte-ansible directory run the following command on the Ansible management node or the Bootstrap node of the Quobyte cluster
   ```
   $ ansible-playbook -i inventory install-quobyte-client.yaml
   ```

5) (Optional) Scale out number of dataservers

   Within the quobyte-ansible directory run the following command on the Ansible management node or the Bootstrap node of the Quobyte cluster
   ```
   $ ansible-playbook -i scaleout_dataservices.yaml
   ```

6) (Optional) Cleanup Quobyte server installation

   Within the quobyte-ansible directory run the following command on the Ansible management node or the Bootstrap node of the Quobyte cluster
   ansible-playbook -i inventory clean-quobyte-server.yaml

7) (Optional) Cleanup Quobyte client installation

   Within the quobyte-ansible directory run the following command on the Ansible management node or the Bootstrap node of the Quobyte cluster
   ansible-playbook -i inventory clean-quobyte-client.yaml
