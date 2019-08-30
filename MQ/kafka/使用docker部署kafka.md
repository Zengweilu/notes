# 简单了解一下kafka

kafka  高性能跨语言的分布式发布/订阅消息系统，数据持久化，全分布式，同时支持在线和离线处理

kafka 设计目标
-高吞吐率 在廉价的商用机器上单机可支持每秒100万条消息的读写
-消息持久化 所有消息均被持久化到磁盘，无消息丢失，支持消息重放
- 完全分布式 Producer，Broker，Consumer均支持水平扩展
-同时适应在线流处理和离线批处理



编写 zookooper 的dockerfile
FROM centos:6.6

RUN yum -y install vim lsof wget tar bzip2 unzip vim-enhanced passwd sudo yum-utils hostname net-tools rsync man git make automake cmake patch logrotate python-devel libpng-devel libjpeg-devel pwgen python-pip

RUN mkdir /opt/java &&\
	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "https://download.oracle.com/otn-pub/java/jdk/8u192-b12/750e1c8617c5452694857ad95c3ee230/jdk-8u192-linux-x64.tar.gz" -P /opt/java

RUN tar zxvf /opt/java/jdk-8u192-linux-x64.tar.gz -C /opt/java &&\
	JAVA_HOME=/opt/java/jdk1.8.0_192 &&\
	sed -i "/^PATH/i export JAVA_HOME=$JAVA_HOME" /root/.bash_profile &&\
	sed -i "s%^PATH.*$%&:$JAVA_HOME/bin%g" /root/.bash_profile &&\
	source /root/.bash_profile

ENV ZOOKEEPER_VERSION "3.4.13"

RUN mkdir /opt/zookeeper &&\
	wget http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-$ZOOKEEPER_VERSION/zookeeper-$ZOOKEEPER_VERSION.tar.gz -P /opt/zookeeper

RUN tar zxvf /opt/zookeeper/zookeeper*.tar.gz -C /opt/zookeeper

RUN echo "source /root/.bash_profile" > /opt/zookeeper/start.sh &&\
	echo "cp /opt/zookeeper/zookeeper-"$ZOOKEEPER_VERSION"/conf/zoo_sample.cfg /opt/zookeeper/zookeeper-"$ZOOKEEPER_VERSION"/conf/zoo.cfg" >> /opt/zookeeper/start.sh &&\
	echo "/opt/zookeeper/zookeeper-$"ZOOKEEPER_VERSION"/bin/zkServer.sh start-foreground" >> /opt/zookeeper/start.sh

EXPOSE 2181

ENTRYPOINT ["sh", "/opt/zookeeper/start.sh"]


#简单介绍一下此Dockerfile 中涉及的命令
FROM 依赖基本镜像
RUN 功能为运行指定的命令
ENV 功能为设置环境变量
语法有两种
1. ENV <key> <value>
2. ENV <key>=<value> ...
两者的区别就是第一种是一次设置一个，第二种是一次设置多个
EXPOSE
功能为暴漏容器运行时的监听端口给外部
但是EXPOSE并不会使容器访问主机的端口
如果想使得容器与主机的端口有映射关系，必须在容器启动的时候加上 -P参数
ENTRYPOINT 功能是启动时的默认命令
语法如下：
1. ENTRYPOINT ["executable", "param1", "param2"]
2. ENTRYPOINT command param1 param2



编写完毕后
执行
docker build -t -f zookeeper.Dockerfile .
-f :指定要使用的Dockerfile路径；
-t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
如果中途报错证明此dockerfile代码有异常

可通过
docker images | grep zwl
查看具体版本的images

执行
docker run -itd --name zookeeper -h zookeeper -p2181:2181 zwl/zookeeper:3.4.13 bash
-i以交互模式运行容器，通常与 -t 同时使用
-t为容器重新分配一个伪输入终端，通常与 -i 同时使用
-d 后台运行容器，并返回容器ID；
-itd 以后台形式运行容器，并给该容器分配交互终端
-h 给该容器分配名字
-p 映射地址

编写kafka.Dockerfile
FROM centos:6.6

ENV KAFKA_VERSION "1.0.2"

RUN yum -y install vim lsof wget tar bzip2 unzip vim-enhanced passwd sudo yum-utils hostname net-tools rsync man git make automake cmake patch logrotate python-devel libpng-devel libjpeg-devel pwgen python-pip

RUN mkdir /opt/java

COPY jdk-8u191-linux-x64.tar.gz /opt/java

RUN mkdir /opt/kafka &&\
	wget http://apache.fayea.com/kafka/$KAFKA_VERSION/kafka_2.11-$KAFKA_VERSION.tgz -P /opt/kafka

RUN tar zxvf /opt/java/jdk-8u191-linux-x64.tar.gz -C /opt/java &&\
	JAVA_HOME=/opt/java/jdk1.8.0_191 &&\
	sed -i "/^PATH/i export JAVA_HOME=$JAVA_HOME" /root/.bash_profile &&\
	sed -i "s%^PATH.*$%&:$JAVA_HOME/bin%g" /root/.bash_profile &&\
	source /root/.bash_profile

RUN tar zxvf /opt/kafka/kafka*.tgz -C /opt/kafka &&\
	sed -i 's/num.partitions.*$/num.partitions=3/g' /opt/kafka/kafka_2.11-$KAFKA_VERSION/config/server.properties

RUN echo "source /root/.bash_profile" > /opt/kafka/start.sh &&\
	echo "cd /opt/kafka/kafka_2.11-"$KAFKA_VERSION >> /opt/kafka/start.sh &&\
	echo "sed -i 's%zookeeper.connect=.*$%zookeeper.connect=zookeeper:2181%g'  /opt/kafka/kafka_2.11-"$KAFKA_VERSION"/config/server.properties" >> /opt/kafka/start.sh &&\
	echo "bin/kafka-server-start.sh config/server.properties" >> /opt/kafka/start.sh &&\
	chmod a+x /opt/kafka/start.sh

EXPOSE 9092

ENTRYPOINT ["sh", "/opt/kafka/start.sh"]


特别注意因为下载jdk 需要临时的token下载比较麻烦 所有我将wget改成COPY
COPY 本地linux系统路径 docker的路径
如果使用COPY 请在编译dokerfile的路径保证 jdk-8u191-linux-x64.tar.gz此文件

docker 编写完毕后
执行
docker build -t zwl/kafka:1.0 -f kafka.Dockerfile

生成images后
执行
docker run -itd --name kafka -h kafka --link zookeeper zwl/kafka:1.0 bash

请注意--link zookeeper 与sed -i 's%zookeeper.connect=.*$%zookeeper.connect=zookeeper:2181%g'对应

docker ps | grep zwl


