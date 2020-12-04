配置：
	两台数据库服务器：172.23.7.116/172.23.7.117 
	两台keepalive服务器：172.23.7.110/172.23.7.102
	VIP：172.23.7.115

中间件：keepalive，dbproxy，mycat，amoeba，mysql-proxy

安装mysql（此处使用二进制，还可以用 rpm包安装 或 mysql编译安装）
https://blog.csdn.net/u012562411/article/details/85220783

0.卸载原来版本mysql或mariadb
	rpm -e mariadb-libs  --nodeps
1.进入/usr/local，下载tar包
	wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz
2.解压并重命名
	tar -xvf mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz
	mv /usr/local/mysql-5.7.26-linux-glibc2.12-x86_64 /usr/local/mysql
3.在/usr/local/mysql新建data目录
	mkdir data
4.新建用户组
	groupadd mysql
5.为用户组添加新用户
	useradd mysql -g mysql
6.将mysql文件夹下所有文件/文件夹的所有者及所属组改为mysql
	chown -R mysql.mysql /usr/local/mysql
7.配置
	/usr/local/mysql/bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data 
	
8.继续安装
	/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize
	
9.编辑/etc/my.cnf文件，将一下代码添加到[mysqld]下。
	vi /etc/my.cnf
	[mysqld]
	datadir=/usr/local/mysql/data
	basedir=/usr/local/mysql
	socket=/tmp/mysql.sock
	user=mysql
	port=3306
	character-set-server=utf8
	#取消密码验证
	skip-grant-tables
10.将mysql加入服务，并设置开机自启
	cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
chkconfig mysql on
11.开启服务
	service mysql start
12.登录mysql（由于/etc/my.cnf中设置了取消密码验证，所以此处密码任意，或者直接回车）
	cp /usr/local/mysql/bin/mysql /usr/bin/mysql
	mysql -u root -p
13.使用mysql数据库
	>>use mysql;
14.修改密码
	>>update user set authentication_string=password('123456'),host='%' where user='root';
	>>flush privileges;
>>exit;
15.将/etc/my.cnf中的skip-grant-tables删除或注释掉
	vim /etc/my.cnf
16.登录再次设置密码（可以和刚刚的相同）
	mysql -u root -p
>>ALTER USER 'root'@'%' IDENTIFIED BY '123456';
>>exit;
17.开放端口或者关闭防火墙
	firewall-cmd --zone=public --add-port=3600/tcp --permanent
	firewall-cmd --reload 
	
双主配置
https://blog.csdn.net/yuefei169/article/details/81297668
https://www.cnblogs.com/kevingrace/p/6710136.html
1.主机添加从机账户
	-- 116配置117信息
	grant replication slave on *.* to 'replicate'@'172.23.7.117' identified by '123456';
	flush privileges;
	-- 116配置117信息
	grant replication slave on *.* to 'replicate'@'172.23.7.116' identified by '123456';
	flush privileges;
2.修改配置文件，重启
	复制配置文件
3.查看主从状态
	117
	> show master status;
	116
	> show master status;
4.配置同步信息
	116配置信息
		> stop slave;
		> change master to master_host='172.23.7.117',master_port=3306,master_user='replicate',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=150;
		> start slave;
		> show slave status\G;
		显示有如下状态则正常：
		Slave_IO_Running: Yes
		Slave_SQL_Running: Yes
	117配置信息
		> stop slave;
		> change master to master_host='172.23.7.116',master_port=3306,master_user='replicate',master_password='123456',master_log_file='mysql-bin.000003',master_log_pos=437;
		> start slave;
		> show slave status\G;
		显示有如下状态则正常：
		Slave_IO_Running: Yes
		Slave_SQL_Running: Yes
	
配置Mysql+Keepalived（高可用，mysql和keepalive部署在相同的机器上）
https://www.cnblogs.com/kevingrace/p/6710136.html
https://www.cnblogs.com/Steward-Xu/p/7275273.html
http://ylong.net.cn/mysql-ha.html
1.116和117上安装keepalived并将其配置成系统服务。110和102两台机器上同样进行如下操作
	[root@master1 ~]# yum install -y openssl-devel
	[root@master1 ~]# yum install gcc -y
	[root@master1 ~]# cd /usr/local/src/
	[root@master1 src]# wget http://www.keepalived.org/software/keepalived-1.3.5.tar.gz
	[root@master1 src]# tar -zvxf keepalived-1.3.5.tar.gz
	[root@master1 src]# cd keepalived-1.3.5
	[root@master1 keepalived-1.3.5]# ./configure --prefix=/usr/local/keepalived
	[root@master1 keepalived-1.3.5]# make && make install
	
	[root@master1 keepalived-1.3.5]# cp /usr/local/src/keepalived-1.3.5/keepalived/etc/init.d/keepalived /etc/rc.d/init.d/
	[root@master1 keepalived-1.3.5]# cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/
	[root@master1 keepalived-1.3.5]# mkdir /etc/keepalived/
	[root@master1 keepalived-1.3.5]# cp /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/
	[root@master1 keepalived-1.3.5]# cp /usr/local/keepalived/sbin/keepalived /usr/sbin/
	[root@master1 keepalived-1.3.5]# echo "/etc/init.d/keepalived start" >> /etc/rc.local
2-1.116机器上的keepalived.conf配置。（下面配置中没有使用lvs的负载均衡功能，所以不需要配置虚拟服务器virtual server）
	[root@master1 ~]# cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.bak
	[root@master1 ~]# vim /etc/keepalived/keepalived.conf       ＃清空默认内容，直接采用下面配置
	复制配置文件
2-2.编写切换脚本。KeepAlived做心跳检测，如果Master的MySQL服务挂了(3306端口挂了),那么它就会选择自杀。Slave的KeepAlived通过心跳检测发现这个情况，就会将VIP的请求接管
	[root@master1 ~]# vim /opt/chk_mysql.sh
	复制shell文件
	[root@master1 ~]# chmod 755 /opt/chk_mysql.sh
2-3.启动keepalived服务
	[root@master1 ~]# vi /lib/systemd/system/keepalived.service
		PIDFile=/var/run/keepalived.pid  
	[root@master1 ~]# systemctl daemon-reload
	[root@master1 ~]# /etc/init.d/keepalived start
	正在启动 keepalived：                                      [确定]
3.117机器上的做同样的配置
4.检测keepalive是否起作用

配置Mysql+Keepalived+lvs（负载均衡高可用，mysql和keepalive部署在不同的机器上）
https://www.cnblogs.com/tangyanbo/p/4305589.html
1.两台机器上安装keepalive
2.修改keepalived.conf配置文件
3.在mysql服务器上，编辑realserver脚本文件，开启realserver服务
4.检测keepalive是否起作用
