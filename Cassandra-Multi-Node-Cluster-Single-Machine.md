Prerequisites:
- download Cassandra in local directory (follow the steps in Cassandra-Local-Installation-Mac)

--------------------------------------------------------------------------------------------------------------------------------------------------------

Cassandra-Multi-Node-Cluster-Single-Machine : Process 1 : Using Manual Config Change

Now, go inside the extracted Cassandra folder and make two copies of the conf folder- conf2 and conf3.
Now, we will go inside the conf, conf2 and conf3 folder and make changes to the cassandra.yml file to make all the nodes of our cluster up and working.
Cassandra.yaml is the main configuration file for Cassandra.
Inside cassandra.yaml file we have to make the following changes:

1. Name of the cluster (cluster_name) – All the three nodes must have same cluster name to be part of the same cluster.
    cluster_name: 'Local Cluster'

2. Data file directories (data_file_directories) – Give different paths to all three nodes so that so that all nodes can save data on different directories.
I have given below the snapshots of the paths that I have given for the nodes:
    data_file_directories: - /Users/souravdhar/Documents/Cassandra/node1/data
    data_file_directories: - /Users/souravdhar/Documents/Cassandra/node2/data
    data_file_directories: - /Users/souravdhar/Documents/Cassandra/node3/data

3. Commit log directory(commitlog_directory) – Give different paths to all three nodes for commit_direstory as well below are the snapshots
of the paths that I have given:
    commitlog_directory: /Users/souravdhar/Documents/Cassandra/node1/commit_log
    commitlog_directory: /Users/souravdhar/Documents/Cassandra/node2/commit_log
    commitlog_directory: /Users/souravdhar/Documents/Cassandra/node3/commit_log

4. Saved cache directory(change path):
    saved_caches_directory: /Users/souravdhar/Documents/Cassandra/node1/saved_caches
    saved_caches_directory: /Users/souravdhar/Documents/Cassandra/node2/saved_caches
    saved_caches_directory: /Users/souravdhar/Documents/Cassandra/node3/saved_caches

5. Listening address:
    listen_address: 127.0.0.1
    listen_address: 127.0.0.2
    listen_address: 127.0.0.3

6. rpc-address:
    rpc_address: 127.0.0.1
    rpc_address: 127.0.0.2
    rpc_address: 127.0.0.3


Next, we will change the JMX_Port under cassandra.env.sh file for conf2 and conf3 folders.
JMX_Port specifies the default port over which Cassandra will be available for JMX connections.
Sample JMX_ Port for the three nodes:
JMX_PORT="7177"
JMX_PORT="7188"
JMX_PORT="7199"


Now, in bin folder there is a cassandra.in.sh file, make two copies of it naming them cassandra2.in.sh and cassandra3.in.sh.
Now, open cassandra2.in.sh and change cassandra_conf property. Similarly, change cassandra3in.sh.
CASSANDRA_CONF="$CASSANDRA_HOME/conf2"
CASSANDRA_CONF="$CASSANDRA_HOME/conf3"


Finally, in bin folder there is a Cassandra file, make two copies of it naming them cassandra2 and specify which config folder it has to use.
    if [ "x$CASSANDRA_INCLUDE" = "x" ]; then
        # Locations (in order) to use when searching for an include file.
          for include in "`dirname "$0"`/cassandra2.in.sh" \
                         "$HOME/.cassandra2.in.sh" \
                         /usr/share/cassandra/cassandra2.in.sh \
                         /usr/local/share/cassandra/cassandra2.in.sh \
                         /opt/cassandra/cassandra2.in.sh; do
              if [ -r "$include" ]; then
                . "$include"
                break
              fi
          done
    # ...otherwise, source the specified include.
    elif [ -r "$CASSANDRA_INCLUDE" ]; then
          . "$CASSANDRA_INCLUDE"
    fi
Similarly do it for cassandra3


After making all the changes now run all the instances of Cassandra on different terminals. Using commands:
./cassandra -f
./cassandra2 -f
./cassandra3 -f

./nodetool -h localhost -p 7177 status


--------------------------------------------------------------------------------------------------------------------------------------------------------


Cassandra-Multi-Node-Cluster-Single-Machine : Process 2 : Using CCM - Cassandra Cluster Manager

https://github.com/riptano/ccm


--------------------------------------------------------------------------------------------------------------------------------------------------------

Working with Cassandra CQLSH

In a separate terminal execute following:
cqlsh
By default it will start the client with 127.0.0.1:9042


Create KEYSPACE with following command in CQL shell:
CREATE KEYSPACE test01
    WITH REPLICATION = {
        'class': 'SimpleStrategy',
        'replication_factor': 3
    };


To view all keyspaces use:
DESCRIBE KEYSPACES


Switch keyspace:
USE test01;


Create a table:
CREATE TABLE countries (
    id INT PRIMARY KEY,
    official_name TEXT,
    capital_city TEXT
);


Insert data into the partition with a single row:
INSERT INTO countries (id, official_name, capital_city) VALUES (1, 'India', 'New Delhi');


Query the data you just entered:
SELECT * FROM countries;


Check in all data folder and you can see the KEYSPACE.
I will now stop the first node of the Cassandra cluster. I will not get data via CQLSH as 127.0.0.1 is stoped.


If we need to connect to different url have to specify like following:
cqlsh 127.0.0.2
Now if we run SELECT * FROM countries; then I can see the records.
