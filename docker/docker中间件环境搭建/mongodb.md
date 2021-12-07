https://www.runoob.com/docker/docker-install-mongodb.html
docker pull mongo:4.4.0
docker run -itd --name mongo -p 27017:27017 mongo:4.4.0 --auth
$ docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'password',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', 'password')

# 创建普通用户
db.createUser({user:'ecis', pwd:'ecisn', roles:['readWrite']})