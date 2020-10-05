# Description

This role configures an [Apache Cassandra](https://cassandra.apache.org/) cluster.

We run this as a storage backend for [Cortex](https://cortexmetrics.io/) which is a storage backend for [Prometheus](https://prometheus.io/).

# Configuration

The bare minimum would be to set cluster name and number of tokens.
```yml
cassandra_cluster_name: 'my-cassandra-cluster'
cassandra_num_tokens: 256
cassandra_storage_port: 7000
cassandra_native_port: 9042
```

# Management

The nodes run Cassandra as Systemd services:
```
 > sudo systemctl status cassandra
● cassandra.service - "Cassandra -  distributed, wide column store, NoSQL database"
     Loaded: loaded (/lib/systemd/system/cassandra.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2020-08-01 12:16:56 UTC; 1min 51s ago
       Docs: https://cassandra.apache.org/
   Main PID: 317116 (java)
      Tasks: 64 (limit: 9513)
     Memory: 2.2G
     CGroup: /system.slice/cassandra.service
             └─317116 java -Xloggc:/opt/cassandra/bin/../logs/gc.log -ea -XX:+UseThreadPriorities -XX:ThreadPriorityPolicy=42 -XX:+HeapDumpOnOutOfMemoryError -Xss256k -XX:StringTableSize=1000003 -XX:+AlwaysPreTouch -XX:-UseBiasedLocking -XX:+UseTLA>

Aug 01 12:17:06 store-01.do-ams3.metrics.hq cassandra[317116]: INFO  [GossipStage:1] 2020-08-01 12:17:06,504 Gossiper.java:1126 - Node /10.1.32.252 has restarted, now UP
Aug 01 12:17:06 store-01.do-ams3.metrics.hq cassandra[317116]: INFO  [GossipStage:1] 2020-08-01 12:17:06,515 StorageService.java:2394 - Node /10.1.32.252 state jump to NORMAL
Aug 01 12:17:06 store-01.do-ams3.metrics.hq cassandra[317116]: INFO  [GossipStage:1] 2020-08-01 12:17:06,528 TokenMetadata.java:497 - Updating topology for /10.1.32.252
Aug 01 12:17:06 store-01.do-ams3.metrics.hq cassandra[317116]: INFO  [GossipStage:1] 2020-08-01 12:17:06,529 TokenMetadata.java:497 - Updating topology for /10.1.32.252
```
And you can check the status of the cluster using:
```
 > nodetool status
Datacenter: do-ams3
===================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens       Owns (effective)  Host ID                               Rack
UN  10.1.32.253  283.88 KiB  256          69.4%             d625ecc1-37c5-4c77-9f48-6d21df6d7da4  main
UN  10.1.32.252  340.06 KiB  256          64.8%             8c3e38a1-7751-433a-82a1-a7f709378376  main
UN  10.1.32.254  334.51 KiB  256          65.8%             1c4ba389-52f8-4663-a5e2-27f9fc159f6d  main
```

# Usage

You can query the cluster using [CQL](https://cassandra.apache.org/doc/latest/cql/index.html):
```
 > cqlsh 10.1.32.253
Connected to status-im-metrics at 10.1.32.253:9042.
[cqlsh 5.0.1 | Cassandra 3.11.7 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
cqlsh> SHOW host
Connected to status-im-metrics at 10.1.32.253:9042.
```

# Links

* https://github.com/status-im/infra-hq/issues/18
* https://cassandra.apache.org/doc/latest/architecture/index.html
