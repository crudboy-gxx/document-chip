DDL（Data Definition Language）数据库定义语言，用于定义数据库的三级结构，包括外模式、概念模式、内模式及其相互之间的映像，定义数据的完整性、安全控制等约束，
DDL不需要commit。
CREATE、ALTER、DROP、TRUNCATE、COMMENT、RENAME
DML（Data Manipulation Language）数据操纵语言，由DBMS提供，用于让用户或程序员使用，实现对数据库中数据的操作。DML分成交互型DML和嵌入型DML两类。依据语言的级别，DML又可分成过程性DML和非过程性DML两种。需要commit。
SELECT、INSERT、UPDATE、DELETE、MERGE（有则更新，无则插入）、CALL、EXPLAIN PLAN、LOCK TABLE、
DCL（Data Control Language）数据库控制语言  授权，角色控制等
GRANT 授权
REVOKE 取消授权
TCL（Transaction Control Language）事务控制语言
SAVEPOINT 设置保存点
ROLLBACK  回滚
SET TRANSACTION

![image-20201204111321466](https://raw.githubusercontent.com/guoxx-crudboy/picture/master/image-20201204111321466.png)