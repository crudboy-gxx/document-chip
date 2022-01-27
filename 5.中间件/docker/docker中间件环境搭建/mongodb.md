# mongodb单机版docker安装
## 拉取镜像
$ docker pull mongo:latest
## 启动镜像方式1（设置MONGO_INITDB_ROOT_USERNAME和MONGO_INITDB_ROOT_PASSWORD两个环境变量后，mongo服务将以--auth启动）
$ docker run -itd --name mongo -p 27017:27017 -v /sdyy/mongo/data:/data/db --restart always -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=password mongo:latest
## 启动镜像方式2
$ docker run -itd --name mongo -p 27017:27017 -v /sdyy/mongo/data:/data/db --restart always mongo:latest --auth
### 进入容器
$ docker exec -it mongo bash
### 执行命令连接mongo
mongo admin 或 mongo 127.0.0.1:27017/admin
### 创建一个名为 admin，密码为 123456 的用户。
> db.createUser({ user:'admin',pwd:'password',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});


## 创建普通用户
> db.createUser({user:'ecis', pwd:'ecis', roles:['readWrite']})