#什么是MyCAT
1.MyCAT 相当与MySql server层
2.mysql 相同于mycat的存储层
3.mycat中不存储数据，所有数据存储在mysql中
4.mycat是一个数据库中间层


对于研发人员来说mycat就是mysql,所以mycat对于研发人员来说是透明的
MyCAT的基本元素
逻辑表
 对应用来说相当于MySQL中的数据表
 逻辑表可对应后端多个物理数据库中的表
 逻辑表中并不保存数据
逻辑表的类别
 分片表于非分片表按是否被分片划分
 全局表，在所有分片中多存在的表
 ER关系表，按照Er关系进行分片的表
 
 

#如何安装MyCat
1.下载并解压MYCAT
    wget http://dl.mycat.io/1.6.7.3/Mycat-server-1.6.7.3-release-20190828135747-linux.tar.gz
    tar zxf Mycat-server-1.6.7.3-release-20190828135747-linux.tar.gz
    mv mycat/ /usr/local/
   
2.安装java运行环境 >=1.7
    不做解释
3.新建mycat运行系统账号
    adduser mycat
    chown mycat:mycat -R mycat/
4.配置系统环境变量
export PATH=/usr/local/nginx/sbin:/usr/local/mycat:/usr/local/php/bin:/usr/local/mysql/bin:$PATH
#Java Env
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_191
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
export MyCAT_HOME=/usr/local/mycat
export  PKG_CONFIG_PATH=/usr/local/lib/pkgconfig

5.修改mycat配置，并启动

#安装一个mysql
使用docker安装
 1.安装docker 环境
 yum install -y yum-utils device-mapper-persistent-data lvm2
 yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
 yum makecache fast
 yum -y install docker-ce
 service docker start
 2.安装mysql 
 docker pull mysql
 docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=javatom -d mysql
 给mysql添加远程访问权限
 docker exec -it mysql bash
 登录mysql
 mysql -u root -p
 ALTER USER 'root'@'localhost' IDENTIFIED BY 'javatom';
 添加远程登录用户
 CREATE USER 'username'@'%' IDENTIFIED WITH mysql_native_password BY 'javatom';
 GRANT ALL PRIVILEGES ON *.* TO 'username'@'%';
