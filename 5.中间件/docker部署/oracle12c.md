#拉取镜像
docker pull absolutapps/oracle-12c-ee
#运行容器
docker run -d -p 8080:8080 -p 1521:1521 --name oracle-12cR1-ee --privileged absolutapps/oracle-12c-ee
#容器开启时间很长，需要打开日志查看安装情况，此条命令会卡很久，看个人机器配置，我卡了30分钟。
docker logs -f oracle-12cR1-ee
#进入容器内部
docker exec -it oracle-12cR1-ee /bin/bash
echo $ORACLE_SID
#登录
sqlplus system/oracle@//localhost:1521/orcl
#创建表空间
create tablespace test datafile '/u01/app/oracle/test.dbf' size 200M;
#创建用户
create user steveyu identified by "Root123.." default tablespace test;
#给用户授予权限
grant create session to steveyu;
grant connect,resource to steveyu;
grant dba to steveyu;
grant resource to steveyu;
# enjoy it