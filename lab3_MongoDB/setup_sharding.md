# MongoDb sharding setup in Docker

In this project there are:
    - config server containing 3 nodes - one primary and two secondary ones 
    - 4 shards containing 3 nodes each
    - mongos query router

## To launch this project:
1) Start the containers
```
docker-compose up -d
```

2) Configure config servers
Go to
```
mongo mongodb://localhost:40001
```
Then initiate the replica set
```
>rs.initiate(
  {
    _id: "config_serv_repl_set",
    configsvr: true,
    members: [
      { _id : 0, host : "conf_node1:27017" },
      { _id : 1, host : "conf_node2:27017" },
      { _id : 2, host : "conf_node3:27017" }
    ]
  }
)
```
To check the status use
```
rs.status()
```

2) Configure the shards
```
mongo mongodb://localhost:50001

> rs.initiate(
  {
    _id: "shard_repl_set",
    members: [
      { _id : 0, host : "shard1_node1:27017" },
      { _id : 1, host : "shard1_node2:27017" },
      { _id : 2, host : "shard1_node3:27017" }
    ]
  }
)
```
```
mongo mongodb://localhost:50004

> rs.initiate(
  {
    _id: "shard_repl_set2",
    members: [
      { _id : 0, host : "shard2_node1:27017" },
      { _id : 1, host : "shard2_node2:27017" },
      { _id : 2, host : "shard2_node3:27017" }
    ]
  }
)
```
```
mongo mongodb://localhost:50007

> rs.initiate(
  {
    _id: "shard_repl_set3",
    members: [
      { _id : 0, host : "shard3_node1:27017" },
      { _id : 1, host : "shard3_node2:27017" },
      { _id : 2, host : "shard3_node3:27017" }
    ]
  }
)
```
```
mongo mongodb://localhost:50010

> rs.initiate(
  {
    _id: "shard_repl_set4",
    members: [
      { _id : 0, host : "shard4_node1:27017" },
      { _id : 1, host : "shard4_node2:27017" },
      { _id : 2, host : "shard4_node3:27017" }
    ]
  }
)
```

3) Add the shards to mongos

Connect to mongos
```
mongo mongodb://localhost:60000
```
Add all shards
```
mongos> sh.addShard("shard_repl_set/shard1_node1:27017,shard1_node2:27017,shard1_node3:27017")
mongos> sh.addShard("shard_repl_set2/shard2_node1:27017,shard2_node2:27017,shard2_node3:27017")
mongos> sh.addShard("shard_repl_set3/shard3_node1:27017,shard3_node2:27017,shard3_node3:27017")
mongos> sh.addShard("shard_repl_set4/shard4_node1:27017,shard4_node2:27017,shard4_node3:27017")
```
```
mongos> sh.status()
```