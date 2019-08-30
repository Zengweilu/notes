docker 容器操作命令

docker ps 列出容器

语法
-a :显示所有的容器，包括未运行的。
-f :根据条件过滤显示的内容。
--format :指定返回值的模板文件
-l :显示最近创建的容器。
-n :列出最近创建的n个容器。
--no-trunc :不截断输出。
-q :静默模式，只显示容器编号。
-s :显示总的文件大小。

docker ps -a 
显示所有的容器，包括未运行的。
docker ps -a | grep zwl


docker inspect 获取容器/镜像的元数据。

-f :指定返回值的模板文件
-s :显示总的文件大小。
--type :为指定类型返回JSON。

docker top mymysql
查看容器mymysql的进程信息。

docker attach :连接到正在运行中的容器。
容器mynginx将访问日志指到标准输出，连接到容器查看访问信息。
docker attach --sig-proxy=false mynginx

docker events : 从服务器获取实时事件
-f ：根据条件过滤事件；

--since ：从指定的时间戳后显示所有事件;

--until ：流水时间显示到指定的时间为止；

 docker events  --since="1467302400"
 
 
 docker logs : 获取容器的日志
 -f : 跟踪日志输出
 
 --since :显示某个开始时间的所有日志
 
 -t : 显示时间戳
 
 --tail :仅列出最新N条容器日志
 docker logs -f zookeeper
 跟踪查看容器zookeeper的日志输出。
 docker logs --since="2018-07-01" --tail=10 zookeeper
 查看容器zookeeper从2016年7月1日后的最新10条日志
 
 
 docker wait : 阻塞运行直到容器停止，然后打印出它的退出代码。
 docker wait zookeeper
 
 
 docker port :列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。
 查看容器zookeeper的端口映射情况。
 docker port zookeeper 