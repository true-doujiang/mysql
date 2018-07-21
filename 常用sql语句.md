1.查询没有合同的用户

	①子查询
	not in
	select 
	    *
	from user 
	where id not in (select user_id from contract);
	
	not exists
	select 
		* 
	from user 
	where not exists (select 1 from contract where contract.user_id = user.id);
	
	②非子查询
	都是对关联查询后的结果进行过滤但是有很大不同
	select 
		user.*,
		contract.id,
		contract.name 
	from user 
	left join contract on contract.user_id = user.id 
	where contract.name is null;
	
	where contract.name = null; 就不同

2.查询重复用户名的用户id及用户名	

	select 
		* 
	from user 
	where name in (select name from user group by name having count(name)>1);
	
3.分组查询
	
	having关键字，指定条件表达式，对分组后的内容进行过滤 
	查询student表，按照gender字段进行分组，查询出grade字段值之和大于300的分组
	select 
		sum(grade) ss, 
		gender 
	from student 
	group by gender having sum(grade) > 300;
	
 4.行转列
 
    max(case col1 when 'aa' then col2 else 0 end) '微信'	



--------------------------------------------------------------------------------------------------------------------------
一、 mysql事务
	
	1. 事务的概念
        事务
        原子性
        永久性
        隔离性
        一致性
	
	2. 事务之间没有隔离性将导致的问题
        丢失更新
        脏读
        不可重复读
        幻读
	
	3. innodb事务隔离级别
	
	
	
	4. 锁的概念

二、 隔离级别讲解
	
	1. read uncommitted
        
	mysql> select @@tx_isolation;
	mysql> set session transaction isolation level read uncommitted;
	
	
	
	
