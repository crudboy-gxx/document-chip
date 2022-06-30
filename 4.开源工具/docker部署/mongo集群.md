
docker run -idt --name mongo-node1 --net host --restart always -v /sdyy/mongodb/data:/data/db -v /sdyy/mongodb/config/mongo.conf:/etc/mongo.conf -v /sdyy/mongodb/log/:/var/log/mongodb/ mongo:4.2.8 --replSet rs0

docker run -idt --name mongo-node2 --net host --restart always -v /sdyy/mongodb/data:/data/db -v /sdyy/mongodb/config/mongo.conf:/etc/mongo.conf -v /sdyy/mongodb/log/:/var/log/mongodb/ mongo:4.2.8 --replSet rs0

docker run -idt --name mongo-node3 --net host --restart always -v /sdyy/mongodb/data:/data/db -v /sdyy/mongodb/config/mongo.conf:/etc/mongo.conf -v /sdyy/mongodb/log/:/var/log/mongodb/ mongo:4.2.8 --replSet rs0


执行以下命令初始化副本集
use admin
config = { "_id": "rs0", "members": [{ "_id": 0, "host": "midnode1:27017", "priority": 1 }, { "_id": 1, "host": "midnode2:27017", "priority": 1 }, { "_id": 2, "host": "midnode3:27017", "priority": 1 }] }
rs.initiate(config)

db.createUser({user:'root',pwd:'1qaz@WSX3edc',roles:[{ role: "userAdminAnyDatabase", db: "admin" }]})
use bdf
db.createUser({user:'root',pwd:'1qaz@WSX3edc',roles:[{role:"readWrite", db: "bdf"}]})
use bdf
db.auth('root','1qaz@WSX3edc')
