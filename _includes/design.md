- In smaller configurations, Mongos can be on the same instances as
your data. We chose to separate the routers from the data in order to
isolate application and data networks by configuring Mongos instances to have 
interfaces on both networks. Replica sets and Config Servers only
have interfaces on the data network.
- The cluster comes ready with one shard for databases to be deployed
and have sharding enabled by simply enabling sharding on your database in
the mongo shell.
- Backups can be taken via mongodump. A great backup strategy is to
attach a Cinder volume to the shard, dump the database to the Cinder volume, 
and detach it. Then you can utilize volume snapshots to retain point in time 
copies.
