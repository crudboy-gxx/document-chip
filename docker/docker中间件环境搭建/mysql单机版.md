# mysql单机版搭建
+ 拉取镜像
`docker pull mysql:5.7`
+ 宿主机新建文件夹
    ~~~shell
    mkdir /home/mysql
    mkdir /home/mysql/conf
    mkdir /home/mysql/data
    mkdir /home/mysql/logs
    ~~~
+ 获取配置文件
    ~~~shell
    docker run -d -p 3306:3306 --name myMysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7
    docker cp myMysql:/etc/mysql  /home/mysql/conf
    docker stop myMysql
    docker rm myMysql
    ~~~
+ 配置mysqld.cnf文件
`vi /home/mysql/conf/mysql/mysql.conf.d/mysqld.cnf`
增加如下参数
    ~~~conf
    lower_case_table_names=1
    sql_mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
    character-set-server=utf8
    group_concat_max_len=1024000
    ~~~
+ 创建容器
    ~~~shell
    docker run -d --name mysql5.7 -p 33060:3306 --restart always --privileged=true -v /home/mysql/conf/mysql:/etc/mysql -v /home/mysql/data:/var/lib/mysql -v /home/mysql/logs:/logs -e MYSQL_ROOT_PASSWORD="password" mysql:5.7
        --restart always                                -> 开机启动
        --privileged=true                               -> 提升容器内权限
        -v /root/docker/mysqletc/mysql:/etc/mysql       -> 映射配置文件
        -v /root/docker/mysqletc/data:/var/lib/mysql    -> 映射数据目录
        -e MYSQL_ROOT_PASSWORD="123456"                 -> 设置root的密码为123456
    ~~~
+ 配置ip可访问
	连接docker容器：`docker exec -it mysql5.7 /bin/bash`
		进入mysql：`mysql -u root -p`
		授权：`GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "password";flush privileges;`
