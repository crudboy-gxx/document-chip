## systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。
启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed

## firewalld的基本使用
启动： systemctl start firewalld
关闭： systemctl stop firewalld
查看状态： systemctl status firewalld 
开机禁用  ： systemctl disable firewalld
开机启用  ： systemctl enable firewalld

## 配置firewalld-cmd
查看开放端口： firewall-cmd --zone=public --list-ports
查看区域信息:  firewall-cmd --get-active-zones
查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
拒绝所有包：firewall-cmd --panic-on
取消拒绝状态： firewall-cmd --panic-off
查看是否拒绝： firewall-cmd --query-panic

## 开启一个端口
添加：firewall-cmd --zone=public --add-port=80/tcp --permanent
重新载入：firewall-cmd --reload
查看：firewall-cmd --zone= public --query-port=80/tcp
删除：firewall-cmd --zone= public --remove-port=80/tcp --permanent



# iptables 限制ip访问
## 通过iptables限制9889端口的访问，其他ip都禁止访问
iptables -I INPUT -p tcp --dport 9200 -j DROP
iptables -I INPUT -p tcp --dport 9300 -j DROP
iptables -I INPUT -s 10.80.13.157 -p tcp --dport 9200 -j ACCEPT
iptables -I INPUT -s 10.80.13.154 -p tcp --dport 9200 -j ACCEPT
iptables -I INPUT -s 10.80.13.157 -p tcp --dport 9300 -j ACCEPT
iptables -I INPUT -s 10.80.13.154 -p tcp --dport 9300 -j ACCEPT
iptables -L -n

## docker部署限定
iptables -I DOCKER -p tcp --dport 9200 -j DROP
iptables -I DOCKER -p tcp --dport 9300 -j DROP
iptables -I DOCKER -s 127.0.0.1 -p tcp --dport 6379 -j ACCEPT
iptables -L -n

## 删除
iptables -L -n --line-num
iptables -D INPUT 1