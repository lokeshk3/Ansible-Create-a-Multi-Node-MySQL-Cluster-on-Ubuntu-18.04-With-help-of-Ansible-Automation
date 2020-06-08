# Ansible-Create-a-Multi-Node-MySQL-Cluster-on-Ubuntu-18.04-With-help-of-Ansible-Automation
Create a Multi-Node MySQL Cluster on Ubuntu 18.04 With help of Ansible-Automation

-------------------------------------------------------------------------------------
  Create a Multi-Node MySQL Cluster on Ubuntu 18.04 With help of Ansible-Automation
--------------------------------------------------------------------------------------

--------------
 Introduction
--------------

-The MySQL Cluster distributed database provides high availability and throughput for your MySQL database management system. 

-A MySQL Cluster consists of one or more management nodes (ndb_mgmd) that store the cluster’s configuration and control the data nodes (ndbd), where cluster data is stored. 

-After communicating with the management node, clients (MySQL clients, servers, or native APIs) connect directly to these data nodes.

-With MySQL Cluster there is typically no replication of data, but instead data node synchronization. 

-For this purpose a special data engine must be used — NDBCluster (NDB). It’s helpful to think of the cluster as a single logical MySQL environment with redundant components. Thus, a MySQL Cluster can participate in replication with other MySQL Clusters.

-MySQL Cluster works best in a shared-nothing environment. Ideally, no two components should share the same hardware. For simplicity and demonstration purposes, we’ll limit ourselves to using only three servers. 

-We will set up two servers as data nodes which sync data between themselves. The third server will be used for the Cluster Manager and also for the MySQL server/client. 

-If you spin up additional servers, you can add more data nodes to the cluster, decouple the cluster manager from the MySQL server/client, and configure more servers as Cluster Managers and MySQL servers/clients.


Prerequisites
To complete this tutorial, you will need a total of three servers: two servers for the redundant MySQL data nodes (ndbd), and one server for the Cluster Manager (ndb_mgmd) and MySQL server/client (mysqld and mysql).


Three Ubuntu 18.04 Droplets with private networking enabled

Be sure to note down the private IP addresses of your three Droplets. In this tutorial our cluster nodes have the following private IP addresses:

198.51.100.2 will be the Cluster Manager & MySQL server node
198.51.100.0 will be the first MySQL Cluster data node
198.51.100.1 will be the second data node



Install Ansible on Cluster Manager & MySQL server node

---------------------------------------------
sudo apt-add-repository ppa:ansible/ansible
--------------------------------------------

Press ENTER when prompted to accept the PPA addition.


Next, refresh your system’s package index so that it is aware of the packages available in the newly included PPA:

-------------------
 sudo apt update
------------------

Following this update, you can install the Ansible software with:

---------------------------
 sudo apt install ansible
--------------------------

Your Ansible control node now has all of the software required to administer your hosts. Next, we will go over how to add your hosts to the control node’s inventory file so that it can control them.


-------------------------------------
Ansible Playbook for Mysql Cluster
------------------------------------

Ansible playbook that install a Mysql-Cluster 7.6 for Linux x86_64, with support for multiple clusters and a differant number of sql, data or management nodes.

-----------------
  Adding hosts
-----------------

Just add your new host to the corresponding section and specify the cluster it belongs to in the mysql_cluster variable.

[mysql_cluster_data]
ndb0 mysql_cluster=test
ndb1 mysql_cluster=test

[mysql_cluster_sql]
ndb0 mysql_cluster=test
ndb1 mysql_cluster=test

[mysql_cluster_mgm]
ndb-mgm mysql_cluster=test

----------------
  Installation
----------------

----------------------------------------------------------------------------------------
ansible-playbook -i hosts.ini   mysql-cluster.yaml --private-key='sampleprivatekey.pem'
----------------------------------------------------------------------------------------

___________________________________________________________________________________________________________________
Note: Use the private key which you used to access these server on Aws and replace it with k.pem in the command 
____________________________________________________________________________________________________________________

-----------------------------------------------------------------------------
 Once inside the MySQL client, run the following command for verification:
----------------------------------------------------------------------------

------------------------------------
mysql> SHOW ENGINE NDB STATUS \G
-----------------------------------

Output

*************************** 1. row ***************************
  Type: ndbcluster
  Name: connection
Status: cluster_node_id=4, connected_host=198.51.100.2, connected_port=1186, number_of_data_nodes=2, number_of_ready_data_nodes=2, connect_count=0
. . .


Open the Cluster management console, ndb_mgm using the command:

$ ndb_mgm

Open the Cluster management console, ndb_mgm using the command:

$ ndb_mgm

$ ndm_mgm> SHOW

You should see the following output:

Output
Connected to Management Server at: 198.51.100.2:1186
Cluster Configuration
---------------------
[ndbd(NDB)] 2 node(s)
id=2    @198.51.100.0  (mysql-5.7.22 ndb-7.6.6, Nodegroup: 0, *)
id=3    @198.51.100.1  (mysql-5.7.22 ndb-7.6.6, Nodegroup: 0)

[ndb_mgmd(MGM)] 1 node(s)
id=1    @198.51.100.2  (mysql-5.7.22 ndb-7.6.6)

[mysqld(API)]   1 node(s)
id=4    @198.51.100.2  (mysql-5.7.22 ndb-7.6.6)


Thank You .......
