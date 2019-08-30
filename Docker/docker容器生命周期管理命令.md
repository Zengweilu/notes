
容器生命周期管理命令
docker run 创建一个新的容器并运行一个命令
docker run -itd --name zookeeper -h zookeeper -p2181:2181 zwl/zookeeper:3.4.13 bash
-i以交互模式运行容器，通常与 -t 同时使用
-t为容器重新分配一个伪输入终端，通常与 -i 同时使用
-d 后台运行容器，并返回容器ID；
-itd 以后台形式运行容器，并给该容器分配交互终端
-h 给该容器分配名字
-p 映射地址

docker start 启动一个或多个已经被停止的容器

dokcer start 容器名

docker stop 停止一个运行中的容器

docker stop 容器名

docker restart 重启容器

docker restart 容器名


docker kill 杀掉一个运行中的容器

docker kill -s KILL zookeeper 
-s 给容器发送一个命令

docker rm 删除一个或多个容器

OPTIONS说明：

-f :通过SIGKILL信号强制删除一个运行中的容器

-l :移除容器间的网络连接，而非容器本身

-v :-v 删除与容器关联的卷


可通过
docker ps | grep zwl
查看具体版本的容器

docker pause 容器名 暂停该容器类的所有线程

docker unpause 容器名 恢复改容器内的所有线程


docker create ：创建一个新的容器但不启动它
语法和docker run 一致


docker exec ：在运行的容器中执行命令
docker exec -i -t zookeeper /bin/bash
输入exit可退出



