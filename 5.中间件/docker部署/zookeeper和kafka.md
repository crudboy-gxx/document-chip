<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 16:58:29
 * @LastEditors: guox
-->
# zookeeper和kafka

+ 拉取镜像
`docker pull wurstmeister/zookeeper`
`docker pull wurstmeister/kafka`

+ 启动zookeeper服务
`docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper`

+ 启动kafuka服务
 `docker run --name kafka -p 9092:9092  -e KAFKA_ZOOKEEPER_CONNECT=10.20.12.24:2181 -e ALLOW_PLAINTEXT_LISTENER=yes -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://10.20.12.24:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -d  wurstmeister/kafka`
docker容器部署必须指定以下环境变量：
  + KAFKA_ZOOKEEPER_CONNECT      指定zookeeper的地址和端口。
  + ALLOW_PLAINTEXT_LISTENER      允许使用PLAINTEXT侦听器。
  + KAFKA_ADVERTISED_LISTENERS      是指向Kafka代理的可用地址列表。 Kafka将在初次连接时将它们发送给客户。格式为PLAINTEXT://host:port，此处已将容器9092端口映射到宿主机9092端口，所以host指定为localhost，便可在宿主机执行测试程序连接kafka。
  + KAFKA_LISTENERS      是Kafka代理将侦听传入连接的地址列表。格式为PLAINTEXT://host:port，0.0.0.0代表接受所有地址。设置了上个变量就要设置此变量。

+ 测试kafka
  `docker exec kafka kafka-topics.sh --create --zookeeper 10.20.12.24:2181 --replication-factor 1 --partitions 1 --topic test`
  `docker exec kafka kafka-topics.sh --list --zookeeper 10.20.12.24:2181`


+ 集群部署kafak
~~~
  docker run -dit --name kafka-node1 --net host -e KAFKA_BROKER_ID=1 -e KAFKA_ZOOKEEPER_CONNECT=midnode1:2181,midnode2:2181,midnode3:2181  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://midnode1:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka:latest

  docker run -dit --name kafka-node2 --net host -e KAFKA_BROKER_ID=2 -e KAFKA_ZOOKEEPER_CONNECT=midnode1:2181,midnode2:2181,midnode3:2181  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://midnode2:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka:latest

  docker run -dit --name kafka-node3 --net host -e KAFKA_BROKER_ID=3 -e KAFKA_ZOOKEEPER_CONNECT=midnode1:2181,midnode2:2181,midnode3:2181  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://midnode3:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka:latest

  docker exec kafka-node1 kafka-topics.sh --create --zookeeper midnode1:2181,midnode2:2181,midnode3:2181 --replication-factor 2 --partitions 3 --topic ttp
~~~