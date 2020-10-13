
1. 查看磁盘使用情况
   + 查看分区使用情况 `df -h`
   + 查看目录下所有文件大小 `du -sh *`

2. 变更文件所有者 `chown -R runoob:runoobgroup *`

3. 文件权限(r 读权限read  4，w 写权限write 2，x 操作权限execute)
   + 增加执行权限 `chmod +x *.sh`
   + 增加全部权限 `chmod 777 test.txt`
   + 增加文件夹权限 `chmod -R 777 /test`

1. 杀死进程 `kill -9 PID(第一个)`

2. 解压缩和压缩
   + 