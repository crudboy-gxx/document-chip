执行sql 在服务器上执行
117
	show master status;
	stop slave;
	change master to master_host='172.23.7.116',master_port=3306,master_user='replicate',master_password='123456',master_log_file='mysql-bin.000002',master_log_pos=851;
	start slave;
	show slave status\G;
116
	show master status;
	stop slave;
	change master to master_host='172.23.7.117',master_port=3306,master_user='replicate',master_password='123456',master_log_file='mysql-bin.000002',master_log_pos=851;
	start slave;
	show slave status\G;


配置文件
116服务器
	[mysqld]
	datadir=/usr/local/mysql/data
	basedir=/usr/local/mysql
	socket=/tmp/mysql.sock
	user=mysql
	port=3306
	character-set-server=utf8
	# 主从配置
	server-id=1         
	log-bin=mysql-bin     
	# 跳过复制错误    
	slave-skip-errors=all  
	# 将复制事件写入binlog,一台服务器既做主库又做从库此选项必须要开启
	log-slave-updates=true
	# 自增
	auto-increment-increment=2     
	auto-increment-offset=1
	# 忽略的数据库
	binlog-ignore-db=mysql  
	binlog-ignore-db=performance_schema
	binlog-ignore-db=information_schema
117服务器
	[mysqld]
	datadir=/usr/local/mysql/data
	basedir=/usr/local/mysql
	socket=/tmp/mysql.sock
	user=mysql
	port=3306
	character-set-server=utf8
	# 主从配置
	server-id=2         
	log-bin=mysql-bin 
	# 跳过复制错误   
	slave-skip-errors=all 
	# 将复制事件写入binlog,一台服务器既做主库又做从库此选项必须要开启 
	log-slave-updates=true 
	# 自增
	auto-increment-increment=2     
	auto-increment-offset=2    
	# 忽略的数据库
	binlog-ignore-db=mysql  
	binlog-ignore-db=performance_schema
	binlog-ignore-db=information_schema


采用脚本执行杀死的方案不好，脚本执行时间和脚本执行所需要的时间不能一样
116 keepalive配置文件  

global_defs {
        router_id MYSQL-1
}     
vrrp_script chk_mysql_port {     #检测mysql服务是否在运行。有很多方式，比如进程，用脚本检测等等
    script "/opt/chk_mysql.sh"   #这里通过脚本监测
    interval 3                   #脚本执行间隔，每3s检测一次
    weight -5                 
    fall 2                 
    rise 1  

}  
vrrp_instance VI_1 {
    state MASTER
    interface ens33              #指定虚拟ip的网卡接口 ip addr查询
    virtual_router_id 51         #路由器标识，MASTER和BACKUP必须是一致的
    priority 100                  #定义优先级，数字越大，优先级越高，在同一个vrrp_instance下，MASTER的优先级必须大于BACKUP的优先级。这样MASTER故障恢复后，就可以将VIP资源再次抢回来 
    advert_int 1         
    authentication {   
        auth_type PASS 
        auth_pass 1111     
    }
    virtual_ipaddress {    
        172.23.7.115
    }
      
        track_script {               
           chk_mysql_port             
        }
}


117 keepalive配置文件

global_defs {
        router_id MYSQL-2
}     
vrrp_script chk_mysql_port {     #检测mysql服务是否在运行。有很多方式，比如进程，用脚本检测等等
    script "/opt/chk_mysql.sh"   #这里通过脚本监测
    interval 3                   #脚本执行间隔，每3s检测一次
	weight -5                 
    fall 2                 
    rise 1  
}  
vrrp_instance VI_1 {
    state BACKUP
    interface ens33              #指定虚拟ip的网卡接口 ip addr查询
    virtual_router_id 51         #路由器标识，MASTER和BACKUP必须是一致的
    priority 90                  #定义优先级，数字越大，优先级越高，在同一个vrrp_instance下，MASTER的优先级必须大于BACKUP的优先级。这样MASTER故障恢复后，就可以将VIP资源再次抢回来 
    advert_int 1         
    authentication {   
        auth_type PASS 
        auth_pass 1111     
    }
    virtual_ipaddress {    
        172.23.7.115
    }
      
        track_script {               
           chk_mysql_port             
        }
}


keepalive检测mysql脚本

#!/bin/bash
MYSQL=/usr/local/mysql/bin/mysql
MYSQL_HOST=127.0.0.1
MYSQL_USER=root
MYSQL_PASSWORD=123456
CHECK_TIME=3
  
#mysql  is working MYSQL_OK is 1 , mysql down MYSQL_OK is 0
  
MYSQL_OK=1
function check_mysql_helth (){
    $MYSQL -h $MYSQL_HOST -u $MYSQL_USER -p${MYSQL_PASSWORD} -e "show status;" >/dev/null 2>&1
    if [ $? = 0 ] 
        then
            MYSQL_OK=1
        else
            MYSQL_OK=0
    fi
    return $MYSQL_OK
}
while [ $CHECK_TIME -ne 0 ]
do
    let "CHECK_TIME -= 1"
    echo "检查次数"+$CHECK_TIME
    check_mysql_helth
if [ $MYSQL_OK = 1 ] 
    then
        CHECK_TIME=0
        exit 0
fi
if [ $MYSQL_OK -eq 0 ] &&  [ $CHECK_TIME -eq 0 ]
    then
        echo "开始退出"
        /etc/init.d/keepalived stop
        exit 1
fi
done
