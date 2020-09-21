<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-09-21 16:18:12
 * @LastEditors: guox
-->
# oracle11g
+ 拉取镜像：
`docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g`
+ 创建容器：
`docker run -d –p 1521:1521 --name oracle11g registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g`
+ 启动容器
`docker start oracle11g`
+ 进入镜像进行配置
	+ 进入oracle的命令环境中
	  `docker exec -it oracle11g bash`
	
	+ 切换到oracle数据库的root 用户下
	  `su root`
	  密码：helowin
	  
	+ 编辑profile文件配置ORACLE环境变量
	  `vi /etc/profile`
        ~~~
        export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
        export ORACLE_SID=helowin
        export PATH=$ORACLE_HOME/bin:$PATH
        ~~~ 
	  `source /etc/profile`
	
	+ 创建软连接
	  `ln -s /home/oracle/app/oracle/product/11.2.0/dbhome_2/bin/sqlplus /usr/bin`
	
	+ 切换到oracle 用户
	  `su - oracle`
	
	+ 登录sqlplus–修改sys、system用户密码–创建用户
		以管理员连接：
		`sqlplus /nolog`
		`conn /as sysdba`
		修改密码：
        ~~~sql
		alter user system identified by system;
		alter user sys identified by sys;
		ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
        ~~~
		创建用户：
		`create user test identified by test;`
		并给用户赋予权限：
	      `grant connect,resource,dba to test;`