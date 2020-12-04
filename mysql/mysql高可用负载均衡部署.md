110 keepalive配置文件
global_defs {            
     router_id LVS_1
}
vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 55
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        172.23.7.115
    }
}

virtual_server 172.23.7.115 3306 {
    delay_loop 2
    lb_algo wlc
    lb_kind DR
    #nat_mask 255.255.255.0
    #persistence_timeout 5
    protocol TCP
    real_server 172.23.7.117 3306 {
         weight 3
         TCP_CHECK {
             connect_timeout 3
             #nb_get_retry 3
             delay_before_retry 3
             connect_port 3306
         } 
        }
    real_server  172.23.7.116 3306 {
         weight 3
         TCP_CHECK {
             connect_timeout 3
             #nb_get_retry 3
             delay_before_retry 3
             connect_port 3306                                                                                                                              
         } 
        }
}
102 keepalive配置文件
global_defs {            
     router_id LVS_1
}
vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 55
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        172.23.7.115
    }
}

virtual_server 172.23.7.115 3306 {
    delay_loop 2
    lb_algo wlc
    lb_kind DR
    #nat_mask 255.255.255.0
    #persistence_timeout 5
    protocol TCP
    real_server 172.23.7.117 3306 {
         weight 3
         TCP_CHECK {
             connect_timeout 3
             #nb_get_retry 3
             delay_before_retry 3
             connect_port 3306
         } 
        }
    real_server  172.23.7.116 3306 {
         weight 3
         TCP_CHECK {
             connect_timeout 3
             #nb_get_retry 3
             delay_before_retry 3
             connect_port 3306                                                                                                                              
         } 
        }
}

在mysql服务器上
编辑realserver脚本文件
①进入指定文件夹：cd /etc/init.d/
②创建脚本文件：vi realserver
复制代码，这里我们设置虚拟IP为：192.168.5.200

SNS_VIP=192.168.5.200
/etc/rc.d/init.d/functions
case "$1" in
start)
       ifconfig lo:0 $SNS_VIP netmask 255.255.255.255 broadcast $SNS_VIP
       /sbin/route add -host $SNS_VIP dev lo:0
       echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
       echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
       echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
       echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
       sysctl -p >/dev/null 2>&1
       echo "RealServer Start OK"
       ;;
stop)
       ifconfig lo:0 down
       route del $SNS_VIP >/dev/null 2>&1
       echo "0" >/proc/sys/net/ipv4/conf/lo/arp_ignore
       echo "0" >/proc/sys/net/ipv4/conf/lo/arp_announce
       echo "0" >/proc/sys/net/ipv4/conf/all/arp_ignore
       echo "0" >/proc/sys/net/ipv4/conf/all/arp_announce
       echo "RealServer Stoped"
       ;;
*)
       echo "Usage: $0 {start|stop}"
       exit 1
esac
exit 0
③保存脚本文件后更改该文件权限：chmod 755 realserver
④开启realserver服务：service realserver start