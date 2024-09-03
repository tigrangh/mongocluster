# mongocluster

nice document to follow
https://medium.com/@nkhl143/mongodb-replica-set-deployment-on-ubuntu-18-04-lts-4c3ce3b85b73

a bit more verbose documents to follow
https://www.mongodb.com/docs/manual/tutorial/convert-standalone-to-replica-set/?_ga=2.186345770.2047433748.1716796207-147775273.1716796207  
https://www.mongodb.com/docs/manual/tutorial/deploy-replica-set/

when following the above docs, avoid calling `rs.initiate()`, since, by default it uses the ip address for the primary node. resetting it involves deleting mongodb data directory.

## 1. Hosts file
```
192.168.168.122 rs0mongo01  
192.168.168.123 rs0mongo02  
192.168.168.124 rs0mongo03  
```

*have this portion for each server. also those that are not part of the cluster, but are only clients.*

## 2. MongoDB config
```
net:
  port: 27017
  bindIp: "127.0.0.1,192.168.168.122"
replication:
 replSetName: rs0
```

*use the corresponding ip address for each instance*  
*after changing the config restart each mongod instance*  

## 3. Replica set initiation command
simply connect to mongo shell from the primary server, and call the following.  
it makes sure to communicate with other participants of the cluster, and make it to work together as one.

```
rs.initiate({_id : "rs0",members: [{ _id: 0, host: "rs0mongo01:27017" },{ _id: 1, host: "rs0mongo02:27017" },{ _id: 2, host: "rs0mongo03:27017" }]})
```

## 4. Command to connect from shell
mongosh --host rs0/rs0mongo01,rs0mongo02,rs0mongo03 testcollection
