
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
+ 查看某个文件夹下目录 `du -sh * | sort -n`
+ 查询磁盘文件大小 `du -sh /opt/* | sort -nr`
+ 查看进程详细情况
+ 后台运行jar且不输入nohup日志 `nohup java -jar yourProject.jar >/dev/null 2>&1 & `
+ 从本地复制到远程 `scp -r /root/lk root@43.224.34.73:/home/lk/cpfile`
+ 从远程复制到本地 `scp -r root@43.224.34.73:/home/lk /root`
+ 测试磁盘读写 `fio -direct=1 -iodepth=128 -rw=randwrite -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=iotest -name=Rand_Write_Testing`