备份：
备份数据库：
	数据库test全部导出：exp system/manager@TEST file=d:\daochu.dmp full=y
	数据库中用户表导出：exp system/manager@TEST file=d:\daochu.dmp owner=(system,sys)
	导出表：exp system/manager@TEST file=d:\daochu.dmp tables=(table1,table2)
备份一个数据表：
	CREATE TABLE course1 AS SELECT * FROM course;
查看全部数据库表：
	SELECT * FROM user_tables;
	
清表：
delete 删除一张大表时空间不释放，非常慢是因为占用大量的系统资源，支持回退操作，空间还被这张表占用着。
truncate table 表名 (删除表中记录时释放表空间) 。

select中的关键字：
1.distinct ：Select distinct name from user; --取消列重复。
2.Order by ：select name from user order by name desc;--desc为倒序，默认为asc正序。
3.rownum：select * from user where rownum<30;  --取前30调数据。
4.[charlist]通配符：select * from where name LIKE '[ade]%'；以ade开头。
5.select * from user where name in ('xinxin','zhaozhao');选取特定的人。
6.select* from user where name  between 'xinxin' AND 'zhaozhao';选取介于两者间的数据。
7.SELECT * FROM Persons INNER JOIN User ON Persons.Id_P = user.Id_P;链表查。
	JOIN和inner join: 如果表中有至少一个匹配，则返回行。 
	LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行。
	RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行。
	FULL JOIN: 只要其中一个表中存在匹配，就返回行。
8.select id,name where user UNION select id,name where persons;----UNION 操作符用于合并两个或多个 SELECT 语句的结果集。（UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。）、（union all 取出全部，union会去掉重复值。）
9.select * into user_new from user;----从一个表中选取数据，然后把数据插入另一个表中。
10.exists和in后带select：
	exists强调是否返回结果集，不关心返回什么，有返回结果就算成立。
	in只返回一个字符。

SQL 约束 (Constraints)：
一共有5中约束，分别为not null,unique,check,primary key,foreign key。
当为某一列或者多列创建唯一约束的时候，会创建唯一索引。不建议创建唯一约束！！
2列组合唯一和这两列单独建立唯一不同，前者只要是两列的组合不出现重复，后者是这两个单独的不能出现重复！！
--命名约束
	CREATE TABLE gxx(
	g varchar(2) not null,
	x NUMBER,
	CONSTRAINT con_gx UNIQUE (g,x),
	CONSTRAINT chk_user CHECK (x>0 AND x='xn')
	);
--已定义表修改和删除
	alter table user add constraint con_id unique (id); 
	alter table user drop constraint id;
	--只有非空使用modify，其他都是用add。
	alter table user modify id not null;
	--如果在两表存在主从关系，那么在删除主表主键约束时，必须带上cascade选项。
	alter table user drop constraint id cascade;
	
索引：
--创建索引
	create unique index 索引名on 表名(列名);
--删除索引
	DROP index table_new_indx1;
更新一个包含索引的表需要比更新一个没有索引的表更多的时间，这是由于索引本身也需要更新。
	
alter：
--增加列
	ALTER TABLE table_new ADD (gxx VARCHAR(1)); 
--修改列的数据类型
	ALTER TABLE table_new MODIFY (gxx NUMBER);
--删除列
	ALTER TABLE table_new DROP COLUMN gxx;
--修改列名字
	alter table 表名 rename column旧的字段名 to 新的字段名名;
可以为新增的字段增加默认值，default 'xxx',只能对以后插入的数据产生影响。

给表加注释：
--给表加注释
	comment on table SYS_USER_INFO  is '系统用户信息表';
--给表中的字段加注释
	comment on column SYS_USER_INFO.USER_ID  is '用户ID';

序列（sequence）：
--创建名为 seq_person 的序列对象，它以 1 起始且以 1 递增。该对象缓存 10 个值以提高性能。CACHE 选项规定了为了提高访问速度要存储多少个序列值。
	CREATE SEQUENCE seq_person
		MINVALUE 1
		START WITH 1
		INCREMENT BY 1
		CACHE 10;
--在 "Persons" 表中插入一条新记录。"P_Id" 的赋值是来自 seq_person 序列的下一个数字。
	INSERT INTO Persons (P_Id,FirstName,LastName)VALUES (seq_person.nextval,'Lars','Monsen');

视图（view）：
视图是基于 SQL 语句的结果集的可视化的表。视图总是显示最近的数据。每当用户查询视图时，数据库引擎通过使用 SQL 语句来重建数据。
要在视图中添加一列，重新创建一个视图。
CREATE or REPLACE VIEW view 
AS  
SELECT ProductID,ProductName FROM Products WHERE Discontinued=No

函数（Function）：
1.创建一个函数：
	Sql代码
	    create [or replace] function functionName
		 ( parameterName1  mode1  dataType1,
	        parameterName2  mode2  dataType2,
	        …  )
	  return returnDataTyp is/as
	   begin
	       function_body
	        return expression
	   end functionName;
	实例
	create or replace function get_publicholidaytime(fromtime in Date, totime in Date, isAvailable in number) return number    
	as  
	  --定义变量   
	  free_day number :=0;   
	  mindate Date;   
	  maxdate Date;   
	  total number :=0;   
	  fromdate Date := TO_DATE(to_char(fromtime,'yyyy/MM/dd'),'yyyy/MM/dd');   
	  todate Date := TO_DATE(to_char(totime,'yyyy/MM/dd'),'yyyy/MM/dd');   
	begin  
	  --if语句   
	  if isAvailable=1 then  
	    --sql语句   
	    select min(calendar_date) ,max(calendar_date),count(calendar_date) into mindate,maxdate,total    
	    from T_BI_TNT_DATE    
	    where calendar_date between fromdate and todate   
	    and IS_PUBLIC_HOLIDAY=1 ;   
	    if mindate = fromdate then    
	       total := total - 1;free_day := free_day + ((mindate + 1) - fromtime);   
	    end if;   
	    if maxdate = todate then  
	       total := total - 1;free_day := free_day + (totime - maxdate);   
	    end if;   
	    if mindate = maxdate then    
	       free_day := totime - fromtime;   
	    else  
	       free_day := free_day + total;   
	    end if;   
	  end if;   
	  return free_day;   
	end get_publicholidaytime;
	
2.函数的基本类型：-------------



分页查询
SELECT *
  FROM (SELECT a.*, rownum rn FROM (SELECT * FROM table_name) a)
 WHERE rn BETWEEN 21 AND 40；

SELECT *
  FROM (SELECT a.*, rownum rn
           FROM (SELECT * FROM apply_base) a
          WHERE rownum <= 40)
 WHERE rn >= 21；



1.删除重复列
	1、查找表中多余的重复记录，重复记录是根据单个字段（Id）来判断
	select * from 表 where Id in (select Id from 表 group byId having count(Id) > 1)
	
	2、删除表中多余的重复记录，重复记录是根据单个字段（Id）来判断，只留有rowid最小的记录
	DELETE from 表 WHERE (id) IN ( SELECT id FROM 表 GROUP BY id HAVING COUNT(id) > 1) AND ROWID NOT IN (SELECT MIN(ROWID) FROM 表 GROUP BY id HAVING COUNT(*) > 1);
	
	3、查找表中多余的重复记录（多个字段）
	select * from 表 a where (a.Id,a.seq) in(select Id,seq from 表 group by Id,seq having count(*) > 1)
	
	4、删除表中多余的重复记录（多个字段），只留有rowid最小的记录
	delete from 表 a where (a.Id,a.seq) in (select Id,seq from 表 group by Id,seq having count(*) > 1) and rowid not in (select min(rowid) from 表 group by Id,seq having count(*)>1)
	
	5、查找表中多余的重复记录（多个字段），不包含rowid最小的记录
	select * from 表 a where (a.Id,a.seq) in (select Id,seq from 表 group by Id,seq having count(*) > 1) and rowid not in (select min(rowid) from 表 group by Id,seq having count(*)>1)

2.清除缓存
	alter session set events = 'immediate trace name flush_cache';
	
3.查看当前运行的sql
    SELECT b.sid oracleID,b.username 登录Oracle用户名,b.serial#,  
           spid 操作系统ID, paddr, sql_text 正在执行的SQL, b.machine 计算机名  
    FROM v$process a, v$session b, v$sqlarea c  
    WHERE a.addr = b.paddr  AND b.sql_hash_value = c.hash_value

4.查看锁表
	SELECT l.session_id sid,  s.serial#,  l.locked_mode 锁模式,  l.oracle_username 登录用户,  l.os_user_name 登录机器用户名,  
	       s.machine 机器名,  s.terminal 终端用户名,  o.object_name 被锁对象名,  s.logon_time 登录数据库时间  
	FROM v$locked_object l, all_objects o, v$session s  
	WHERE l.object_id = o.object_id  AND l.session_id = s.sid  
	ORDER BY sid, s.serial#;
	
	杀掉 alter system kill session 'sid,s.serial#';
	
5.查看表空间使用率
	--查询表空间的总容量
	select tablespace_name,sum(bytes) /1024/1024 as MB from dba_data_files group by tablespace_name;
	--查询表空间使用率
	select total.tablespace_name,
	       round(total.MB, 2) as Total_MB,
	       round(total.MB - free.MB, 2) as Used_MB,
	       round((1 - free.MB / total.MB) * 100, 2) || '%' as Used_Pct from
	       (select tablespace_name,sum(bytes) / 1024 / 1024 as MB from dba_free_space group by tablespace_name) free,
	       (select tablespace_name,sum(bytes) / 1024 / 1024 as MB from dba_data_files group by tablespace_name) total
	       where free.tablespace_name = total.tablespace_name order by used_pct desc;
	
	--对象类型：表   分区表   索引   分区索引共四种，我们统计一下不同种类对象各多少size
	 select segment_type,sum(bytes)/1024/1024 total_size from dba_segments where wner='CCARE' group by segment_type;
	--SQL计算出某个用户下所有对象的大小
	select owner,segment_name,segment_type,partition_name,bytes from dba_segments where wner='CCARE';
	
6.查看编码格式
	select * from nls_database_parameters where parameter ='NLS_CHARACTERSET';--数据库
	select * from nls_instance_parameters where parameter='NLS_LANGUAGE';--客户端

	

