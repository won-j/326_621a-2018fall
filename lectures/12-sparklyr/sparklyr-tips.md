Download the flights data
-------------------------

If you downloaded the flights data etc. and copied them to hdfs as super-user other than rstudio-user, then it is more time-saving to delete the DataProc instance and fresh-start.

Creating Cluster
----------------

-   When you create a new cluster freshly, choose `asia-east1` region (Taiwan) and `asia-east1-a` through `asia-east1-c` zone. This is cheaper than `asia-northeast` (Tokyo).

-   Current [lecture notes](../sparklyr-flights.html) are generated using 4vCPUs (15GB ram)+500GB (persistent disk) master with two 4vCPUs (15GB ram)+500GB (persistent disk) worker nodes, yielding 8 yarn cores + 24GB yarn memory.

-   You may also try 2vCPUs (7.5GB ram)+500GB (persistent disk) master with two 2vCPUs (7.5GB ram)+500GB (persistent disk) worker nodes, yielding 4 yarn cores + 12GB yarn memory to save money.

Create Hive tables
------------------

When creating Hive tables, the original data in `/user/rstudio-user/flights` are *moved* to `/user/hive/warehouse/flights`, etc. So don't panic. The hdfs files are stored as data buckets, and they are maintained even if the cluster is shutdown.

If your `spark_connect()` call takes forever...
-----------------------------------------------

Check out the Hadoop Namenode Manager at `http://$External_IP:8088` (e.g., <http://35.187.203.86:8088>). If you see hundreds of jobs created by `dr.who`, then you are likely to be contaminated by the CrytalMiner Virus! The current best practice is to shutdown the cluster immediately, update your firewall setup, and restart the cluster.

Read the following posts about the CrystalMiner Virus: - <https://stackoverflow.com/questions/51283049/google-cloud-dataproc-virus-crytalminer-dr-who> - <https://community.hortonworks.com/questions/191898/hdp-261-virus-crytalminer-drwho.html>

More secure firewall setup
--------------------------

To avoid potential attack from the virus, update your firewall configuration as follows.

1.  In the GCP console, go to VPC Networks &gt; Firewall rules.
2.  You there is no firewall rules regarding the Hadoop managers (involving port 8088), click `CREATE FIREWALL RULE` tab. Otherwise, click the corresponding rule and then click `EDIT` tab.
3.  In `Source filter` setting, select "IP ranges".
4.  In `Source IP ranges` setting, input `147.46.0.0/16` and `147.47.0.0/16`. This allows connections from the SNU network only.
5.  In `Protocols and ports` setting, select "Tcp" and port "8080".
6.  Repeat editing firewall rules for other ports with IP ranges `0.0.0.0/0`, e.g. Rstudio (port 8787) and SSH (port 22).

Clean Up
--------

After finising your project, delete the cluster. Remember that deleting alone is not enough to prevent further billing.

1.  If you used a static IP, then go to Networking &gt; Network Service Tiers &gt; Static external IP address to release the IP. A static IP address is free if it is in use, but charged if not VM is using it.

2.  The data buckets associated with the yarn cluster still reside. Go to Storage &gt; Browser &gt; Buckets to delte the buckets.
