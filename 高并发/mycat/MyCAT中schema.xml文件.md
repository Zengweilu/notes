#schema.xml文件用途
1.配置逻辑表及逻辑表
2.配置逻辑表所存储的数据节点
3.配置数据节点所对应的物理数据库服务器的信息


#定义逻辑库
<schema name="逻辑库名" 
checkSQLschema="false" sqlMaxLimit="1000"></schema>


<table> 定义逻辑表
<table name="逻辑表的名字与真实物理数据库中的表的名字必须一致"
primaaryKey="逻辑表的主键与真实物理数据库中的表主键名一致"
dataNode="db01,db02 定义表数据实际存在的数据库的名"
rule="定义了逻辑表的分片规则，对应的rule.xml中的<tableRule>"
>

<dataNode>
定义逻辑表存储的物理数据库
<dataNode name="定义数据节点的名称，必须唯一" 
dataHost="mysql"


<dataHost> 定义后端数据库主机信息
<dataHost name="mysql0103" maxCon="1000" minCon="10"
balance="1" writType="0" dbType="mysql" dbDriver="native"
switchType="1" >
    <heartbeat> select user()</heartbeat>
    <writeHost host="ip" url="ip:端口"
user="用户名" password="密码">
<readHost host="ip" url="ip:3306" user="用户名" password="123456">
</writeHost>
<dataHost>

maxCon 可以提供连接池 最大连接数
minCon 最小连接数
balance 读写分离工作方式
0：不开启读写分离机制
1：全部的readhost与stand by writeHost参与selectyu语句的负载均衡
2：所有的readhOST与writeHost多参与select语句的负载均衡
3：所有readHost参与select语句的负载均衡

writType 写的工作机制 只有 0 1
0写到
1随机

swutchType 写的高可用的工作机制 1 -1


<heartbeat> 定义如何检查后端数据库是否可用



