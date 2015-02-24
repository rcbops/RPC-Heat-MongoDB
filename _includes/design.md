- Uses MongoDB salt-formulas to configure the servers. Three instances will be Mongo configuration servers, at least two Mongo routing (mongos) servers, and at least three MongoDB shards in a replica set. Each replica set created becomes a shard for databases to spread across.

- For access to nodes in the MongoDB cluster, either a floating IP address is assigned to the salt-master, or a new server is created in the same network as the MongoDB network. Additionally, mongos are also on a separate network so that applications can access your database cluster without being on the same network.

- Changes to the MongoDB configuration can be done using Salt pillars on the Salt master. Adding additional mongos nodes can also be done using Salt.
