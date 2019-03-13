1.查询没有合同的用户

	①子查询
	   **not in**
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
	
	注意： 
	1. where 后的条件是对聚合后的数据进行过滤   on后面的条件是先过滤在聚合
	2. where contract.name = null; 就不同

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
        
        mysql> set binlog_format = row;  设置binlog的模式
	
	mysql> select @@tx_isolation;
	mysql> set session transaction isolation level read uncommitted;   不加session可能会加不上去

三、 binlog日志
	
	1. binlog日志相关参数
        log_bin = mysql-bin
	binlog_format = statement, row, mix
	                statement 记录原生sql语句， 若执行随机函数，则恢复的可能产生不一致情况
			row	  记录行记录
			mix       上面两种混合体
	sync_binlog = 0, 1, 2
	
四、 mysql编程
	
	1. 存储过程
        语法：
	create procedure 存储过程名字 (参数列表)
        begin
	      sql语句代码
        end
	
        参数类型：
	IN     传入，  默认值
	OUT    传出
	INOUT  传入传出都可以
	
        例如：
		mysql> delimiter //
		mysql> create procedure mypro (val1 int, val2 varchar(50)) 
		mysql> begin
		mysql>    insert into teacher values(val1, val2);
		mysql> end
		mysql> //
		mysql> delimiter ;
	
		mysql> create procedure get_count (out val1 int) 
		mysql> begin
		mysql>    select count(*) from teacher;
		mysql> end
		mysql> //
	
	
        使用：
		mysql> call mypro (10, '张三');
		mysql> call get_count (@count);
		mysql> select @count;
	
	2. 用户函数
        语法：
		create function function_name (参数列表)  
		returns type  必须有返回值
		begin
		       sql语句
		end
	
	参数类型：
	IN     只有IN
        
	例如：
		mysql> delimiter //
		mysql> create function fn_count (val1 int)
		mysql> returns int
		mysql> begin
		mysql>    declare m int;
		mysql>    select count(*) into m from teacher where id < n;
		mysql>    return m;
		mysql> end
		mysql> //
		mysql> delimiter ;
	
	使用：
		mysql> select fn_count(100);
	
	
       存储过程和函数区别：
       
       
       
五、 mysql触发器
	
       触发器是基于某个指定事件触发执行的程序体
       mysql触发事件有：
		insert （before， after）
		update （before， after）
		delete （before， after）       
       
       语法：
	       create trigger trigger_name 触发时间 触发事件 on table_name for each row
	       begin
		   sql语句
	       end
       
       触发器中的new和old表：
	       在触发器中有俩个特殊的临时表new、old
	       其表结构与触发器所在的表一致
	       new表捕获插入的新数据
	       old表捕获被删除的就数据
       
       例如：
	       mysql> create trigger get_insert after insert on teacher for each row
	       mysql> begin
	       mysql> 	   insert into teacher2 values(new.id, new.name);
	       mysql> end
       
       
 六、 mysql事件调度
       
       mysql中的事件调度就类似于Oracle中的作业
       可以理解成一个时间触发器
       在特定的时间执行特定的操作，可以执行一次，可以重复执行
       
       
       语法：
	       create event event_name on schedule 时间
	       do sql语句
       
       时间:
	       执行一次： at timestamp [+interval]
	       重复执行： every interval starts timestamp ends timestamp
	       interval：YEAR QUARTER MONTH DAY HOUR MINUTE WEEK.....
       
       例如：
	       mysql> create event sec_insert  on schedule every 1 second
	       mysql> do insert into teacher values(1000, 'second');

	       mysql> create event sec_insert2  on schedule at '2018-7-22 00:58:00'[+interval 1 minute] 
	       mysql> do insert into teacher values(2000, 'atatatatattttttatat');

       
       
       开启调度参数：
	       mysql默认没有开启调度
	       mysql> set global event_scheduler = on;  只能在全局设置
       
       

七、 mysql服务器系统状态
       
       1. 系统性能指标
	       cpu
		  * 空闲时间
		  * 队列长度
	       内存
		  * 已使用内存
		  * 空闲内存
	       磁盘
		  * 每秒io
		  * 物理读写
	  
       2. cpu状态监控
		   # top   # top -u  Linux用户名
		   # sar   #sar 1 
		   # mpstat 
       
       3. 内存监控
		  # free     
		  # free -m     m以MB为单位
	  
       4. 磁盘状态监控
          	  # lostat
	  
       5. 综合状态
       	          # vmstat
		  
		  
		  
		  
		  
		  
	
	
	
	
	
	
