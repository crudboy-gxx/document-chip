	数据操作语言语句[Data manipulation language，DML]
    　　从一个或多个表或视图中查询数据(SELECT);获取操作[fetch]是可滚动 的[scrollable](见“可滚动游标”)
    　　向表或视图中加入新数据行(INSERT)
    　　修改表或视图中已有数据行的列值(UPDATE)
    　　根据判断条件为表及视图插入或更新数据行(MERGE)
    　　从表或视图中删除数据行(DELETE)
    　　查询 SQL 语句的执行计划[execution plan](EXPLAIN PLAN)
    　　对表或视图加锁[lock]，临时地限制其他用户访问此对象(LOCK TABLE)

　　数据定义语言语句[Data definition language，DDL]
    　　创建，修改，移除方案对象及其他数据库结构，包括数据库自身及数据库用户(CREATE，ALTER，DROP)
    　　修改方案对象名称(RENAME)
    　　删除方案对象的所有数据，但不移除对象结构(TRUNCATE)
    　　授予或收回权限及角色(GRANT，REVOKE)
    　　打开或关闭审计选项(AUDIT，NOAUDIT)
    　　向数据字典中添加注释(COMMENT)

　　事务控制语句[transaction control statement]
    　　将事务对数据的修改永久地保存到数据库(COMMIT)
    　　还原事务对数据的修改，可还原到事务开始处或任意保存点[savepoint](ROLLBACK)
    　　设置保存点以标识回滚位置(SAVEPOINT)
    　　设置事务的属性(SET TRANSACTION)

　　会话控制语句[session control statement]
    　　执行特定操作，修改当前会话，例如启用或禁用 SQL 跟踪功能[SQL trace facility](ALTER SESSION)
    　　为当前会话启用或禁用角色[role](即一组权限的集合)(SET ROLE)
    　　系统控制语句[system control statement]
    　　ALTER SYSTEM是唯一的系统控制语句。
    　　嵌入 SQL 语句[embedded SQL statement]
    　　定义，分配，及释放游标[cursor](DECLARE CURSOR，OPEN，CLOSE)
    　　选择一个 Oracle 数据库并进行连接(DECLARE DATABASE，CONNECT)
    　　分配变量名(DECLARE STATEMENT)
    　　初始化描述符[descriptor](DESCRIBE)
    　　设定如何处理错误及警告(WHENEVER)
    　　解析并执行 SQL 语句(PREPARE，EXECUTE，EXECUTE IMMEDIATE)
    　　从数据库中取回数据(FETCH)
