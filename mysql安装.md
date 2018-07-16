### 一、mysql5.6 二进制安装

	1. 解压
		tar -zxvf mysql-5.6.40-linux-glibc2.12-x86_64.tar.gz  -C /usr/local/
		glibc
		# rpm -q glibc 查看当前glibc的版本
		
		# mv mysql-5.6.40-linux-glibc2.12-x86_64 mysql 修改名字
		
		解压的过程mysql软件就安装完成了，下一步是用脚本创建mysql数据库(初始化mysql实例)

		mysql软件（DBMS）: 提供了对mysql数据库的管理。
		mysql数据库: 一系列逻辑和物理结构。
		
		一个mysql软件下可以建多个mysql实例，一个mysql实例下可以建多个数据库。
				
	2. 创建mysql数据库 *****
		  ①创建mysql用户
			# useradd mysql
			# passwd mysql  密码随便
			# chown -R mysql:mysql /usr/local/mysql   把该目录的权限也给mysql用户
			
	 	  ②初始化数据
	 	    # cd /usr/local/mysql
	 		# yum install -y perl-Module-Install.noarch  若报错，执行我
	 		
	 		mysql5.7之前用的这个脚本 之后用 ./bin/mysqld
	 		# ./scripts/mysql_install_db --help  查看选项
			# ./scripts/mysql_install_db --user=mysql   读取的默认参数 /etc/my.cnf
			
			[自定义安装]  
			-----------------------------------------------------
			/etc/my.cnf  修改配置参数
			
			[client]    
			port=3306
			socket=/mysqldata/mysql-01/mysql-01.sock
			
			[mysqld]
			user=mysql  
			basedir=/usr/local/mysql  	      mysql软件的目录
			datadir=/mysqldata/mysql-01	      数据库 "实例" 存放数据的目录
			server_id=1
			socket=/mysqldata/mysql-01/mysql-01.sock
			-----------------------------------------------------
			
	3. 启动mysql
		cd /usr/local/mysql/bin
		# ./mysqld_safe &    后台进程启动
		
	4. 登录
	    cd /usr/local/mysql/bin
	    # ./mysql     使用本地套接字登录 ，刚安装mysql没有登录密码，所以需要配置。
	
	5. 配置环境变量
		# vim ./.bash_profile 
		# source ./.bash_profile 
		
	6. 运作初始化安全脚本
		# mysql_secure_installation   会提示设置密码等等
		脚本是找/tmp/mysql.sock  而sock又不在该目录，所以只能建个软连接。
		5.7之前不能使用 -S 参数指定路径，所以才使用软连接的方式。
		# ln -s /mysqldata/mysql-01/mysql-01.sock /tmp/mysql.sock

	7. 配置mysql为系统服务
		# cd /usr/local/mysql/support-files
		# cp mysql.server /etc/init.d/mysql-01   服务名 mysql-01

		service mysql-01 start
		service mysql-01 status
		service mysql-01 stop
		
		设置开机自启
		# chkconfig mysql-01 on
		
	 8. 创建远程登录用户
	    mysql> create user 'root'@'%' identifier by 'ok';
	    mysql> grant all privileges on 库名.表名 to '用户名'@'IP地址';
	    
	    或者, 等于上面俩条语句 
	    mysql> grant all privileges on 库名.表名 to '用户名'@'IP地址' identified by '密码'	with grant option;
	    mysql> flush privileges;



### 二、mysql5.7 二进制安装
	1. 解压
		tar -zxvf mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz  -C /usr/local/
		glibc
		# rpm -q glibc 查看当前glibc的版本
		
		# mv mysql-5.7.22-linux-glibc2.12-x86_64 mysql 修改名字
		# yum install libaio
		
    2. 创建mysql数据库 *****
    	①创建mysql用户
			# useradd -M -s /sbin/nologin/ mysql   
			
			或者
			shell> groupadd mysql
			shell> useradd -r -g mysql -s /bin/false mysql
		
		②修改/etc/my.cnf	
		-----------------------------------------------------
			vim /etc/my.cnf  修改配置参数
			
			[client]    
			port=3306
			socket=/mysqldata/mysql-01/mysql-01.sock
			
			[mysqld]
			user=mysql  
			basedir=/usr/local/mysql  	      
			datadir=/mysqldata/mysql-01	      
			server_id=1
			socket=/mysqldata/mysql-01/mysql-01.sock
		-----------------------------------------------------
		
		③初始化数据
			# cd /usr/local/mysql
			# mkdir -p /mysqldata
				
	 		mysql5.7及之后用 ./bin/mysqld
	 		# cd /usr/local/mysql
    	    # bin/mysqld --initialize --basedir=/usr/local/mysql/ --datadir=/mysqldata/mysql-01 --user=mysql   会生成临时密码 Ghj/YuaCf5pe
    	
    		先创建目录在授予mysql用户
    		# chown -R mysql:mysql /usr/local/mysql   把该目录的权限也给mysql用户
    		# chown -R mysql:mysql /mysqldata
    
    3. 启动mysql
		# cd /usr/local/mysql/support-files
		# ./mysql.server start  {start|stop|restart|reload|force-reload|status} 
		
		
    4. 配置mysql为系统服务
		# cd /usr/local/mysql/support-files
		# cp mysql.server /etc/init.d/mysqld   服务名 mysqld

		service mysqld start
		service mysqld status
		service mysqld stop
		
		设置开机自启
		# chkconfig mysqld on    

    5. 需要修改root用户的临时密码
    	#先用临时密码登录
		mysql> alter user 'root'@'localhost' identified by 'ok';


    6. 创建远程登录用户
	    mysql> create user 'root'@'%' identified by 'ok';
	    mysql> grant all privileges on 库名.表名 to '用户名'@'IP地址';
	    
	    或者, 等于上面俩条语句 
	    mysql> grant all privileges on 库名.表名 to '用户名'@'IP地址' identified by '密码'	with grant option;
	    mysql> flush privileges; 
	    
	    mysql> grant all privileges on *.* to 'root'@'%' identified by 'ok' with grant option;

  
  
    change master to
	 master_host='192.168.35.142',
    master_user='repl',
    master_password='replpass',
    master_log_file='mysql-bin.000001',
    master_log_pos=1247;
	
	start slave;
	stop slave;
	stop sql_therad slave;
	stop io_therad slave;
	reset slave;   断开复制链接。会自动删除Master_Info_File: /mysqldata/mysql-01/master.info	
	show slave status\G;
	
	show master;
	
	
	change master to
	 master_host='192.168.35.142',
    master_user='repl',
    master_password='replpass',
    master_auto_position=1;
    
    报错误：
    mysql主从不同步，提示Last_Error: Coordinator stopped because there were error(s) in the worker(s). The most recent failure being: Worker 1 failed executing transaction
    按照丛库的提示找原因，输入命令
select * from performance_schema.replication_applier_status_by_worker\G;




#全局定义块
global_defs {
    # 邮件通知配置
    notification_email {
        email1
        email2
    }
    notification_email_from email
    smtp_server host
    smtp_connect_timeout num

    lvs_id string
    router_id string    ## 标识本节点的字条串,通常为hostname
}



vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 100
    advert_int 1
    nopreempt
    authentication {
        auth_type PASS
        auth_pass 1111
    }

    virtual_ipaddress {  
        192.168.35.100/24
    }
}

	
    virtual_server 192.168.35.100 3306 { 
	    delay_loop 6
	    protocol TCP
	    real_server 192.168.35.142 3306 { 
	        notify_down "kill -9 $(cat /var/run/keepalived.pid)"
	        TCP_CHECK { 
	            connect_port 3306 
	            connect_timeout 3
	            nb_get_retry 3
	            delay_before_retry 3
	        }
	    }
	}
	
###阶段四
	1.slow_query_log  log_query_time
	分析满查询日志
		./bin/mysqldumpslow [option] slow_query.log
	percona 需要安装  功能更多
	2.
	3.
	4.
	
一、基于二进制日志位置复制

	Master
	vim /etc/my.cnf
	[mysqld]	
	server-id=1
	log-bin=mysql-bin
	
	
	mysql> grant replication slave on *.* to 'slave'@'%' identified by 'slavepass' with grant option;
	
	Slave
	
	
