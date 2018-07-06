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
			
	 	  ②cd /usr/local/mysql
	 		# yum install -y perl-Module-Install.noarch  若报错
	 		
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
			datadir=/mysqldata/mysql-01	      数据库实例存放数据的目录
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
		5.7之前不是使用 -S 参数指定路径，所以才使用软连接的方式。
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
























			
	
	
	
	1
	
	
	