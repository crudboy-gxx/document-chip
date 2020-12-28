
+ 查看磁盘使用情况
   + 查看分区使用情况 `df -h`
   + 查看目录下所有文件大小 `du -sh *`
+ 变更文件所有者 `chown -R runoob:runoobgroup *`
+ 文件权限(r 读权限read  4，w 写权限write 2，x 操作权限execute)
   + 增加执行权限 `chmod +x *.sh`
   + 增加全部权限 `chmod 777 test.txt`
   + 增加文件夹权限 `chmod -R 777 /test`
+ 杀死进程 `kill -9 PID(第一个)`
+ 清理缓存 `echo 1 > /proc/sys/vm/drop_caches`
+ 查看端口占用 `netstat -ntlp`
+ 查看cpu核心数 
  + 1、查看 CPU 物理个数 `grep 'physical id' /proc/cpuinfo | sort -u | wc -l`
  + 2、查看 CPU 核心数量 `grep 'core id' /proc/cpuinfo | sort -u | wc -l`
  + 3、查看 CPU 线程数 `grep 'processor' /proc/cpuinfo | sort -u | wc -l`
+ 查看cpu使用情况 `vmstat 5`
