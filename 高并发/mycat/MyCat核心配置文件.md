#MyCat配置文件概览
APP
server.xml 
 配置系统配置和用户权限
schema.xml 配置逻辑库和逻辑表
rule.xml 配合schema.xml进行配置



#server.xml
    配置系统相关参数
    配置用户访问权限
    配置sql防火墙和sql拦截器
    
标签详解
<mycat:server xmlns:mycat="http://io.mycat/">
        <system>
        <property name="nonePasswordLogin">0</property> <!-- 0为需要密码登陆、1为不需要密码登陆 ,默认为0，设置为1则需要指定默认账户-->
        <property name="useGlobleTableCheck">0</property>  <!-- 1为开启全加班一致性检测、0为关闭 -->
                <property name="sqlExecuteTimeout">300</property>  <!-- SQL 执行超时 单位:秒-->
                <property name="sequnceHandlerType">2</property>
                <!--<property name="sequnceHandlerPattern">(?:(\s*next\s+value\s+for\s*MYCATSEQ_(\w+))(,|\)|\s)*)+</property>-->
                <!--必须带有MYCATSEQ_或者 mycatseq_进入序列匹配流程 注意MYCATSEQ_有空格的情况-->
                <property name="sequnceHandlerPattern">(?:(\s*next\s+value\s+for\s*MYCATSEQ_(\w+))(,|\)|\s)*)+</property>
        <property name="subqueryRelationshipCheck">false</property> <!-- 子查询中存在关联查询的情况下,检查关联字段中是否有分片字段 .默认 false -->
      <!--  <property name="useCompression">1</property>--> <!--1为开启mysql压缩协议-->
        <!--  <property name="fakeMySQLVersion">5.6.20</property>--> <!--设置模拟的MySQL版本号-->
        <!-- <property name="processorBufferChunk">40960</property> -->
        <!-- 
        <property name="processors">1</property> 
        <property name="processorExecutor">32</property> 
         -->
        <!--默认为type 0: DirectByteBufferPool | type 1 ByteBufferArena | type 2 NettyBufferPool -->
                <property name="processorBufferPoolType">0</property>
                <!--默认是65535 64K 用于sql解析时最大文本长度 -->
                <!--<property name="maxStringLiteralLength">65535</property>-->
                <!--<property name="sequnceHandlerType">0</property>-->
                <!--<property name="backSocketNoDelay">1</property>-->
                <!--<property name="frontSocketNoDelay">1</property>-->
                <!--<property name="processorExecutor">16</property>-->
                <!--       
                        对外提供的端口号
                        <property name="serverPort">8066</property>
                        管理端口号
                         <property name="managerPort">9066</property> 
                        <property name="idleTimeout">300000</property>
                         <property name="bindIp">0.0.0.0</property>
                        <property name="dataNodeIdleCheckPeriod">300000</property> 5 * 60 * 1000L; //连接空闲检查
                        <property name="frontWriteQueueSize">4096</property> 
                        <property name="processors">32</property> -->
                <property name="handleDistributedTransactions">0</property>

                        <!--
                        off heap for merge/order/group/limit      1开启   0关闭
                -->
                <property name="useOffHeapForMerge">0</property>

                <!--
                        单位为m
                -->
        <property name="memoryPageSize">64k</property>

                <!--
                        单位为k
                -->
                <property name="spillsFileBufferSize">1k</property>

                <property name="useStreamOutput">0</property>

                <!--
                        单位为m
                -->
                <property name="systemReserveMemorySize">384m</property>


                <!--是否采用zookeeper协调切换  -->
                <property name="useZKSwitch">false</property>

                <!-- XA Recovery Log日志路径 -->
                <!--<property name="XARecoveryLogBaseDir">./</property>-->

                <!-- XA Recovery Log日志名称 -->
                <!--<property name="XARecoveryLogBaseName">tmlog</property>-->
                <!--如果为 true的话 严格遵守隔离级别,不会在仅仅只有select语句的时候在事务中切换连接-->
                <property name="strictTxIsolation">false</property>

                <property name="useZKSwitch">true</property>

        </system>

        <!-- 全局SQL防火墙设置 -->
        <!--白名单可以使用通配符%或着*-->
        <!--例如<host host="127.0.0.*" user="root"/>-->
        <!--例如<host host="127.0.*" user="root"/>-->
        <!--例如<host host="127.*" user="root"/>-->
        <!--例如<host host="1*7.*" user="root"/>-->
        <!--这些配置情况下对于127.0.0.1都能以root账户登录-->
        <!--
        <firewall>
           <whitehost>
              <host host="1*7.0.0.*" user="root"/>
           </whitehost>
       <blacklist check="false">
       </blacklist>
        </firewall>
        -->

        <user name="root" defaultAccount="true">
                <property name="password">123456</property>
                <property name="schemas">TESTDB</property>

                <!-- 表级 DML 权限设置 -->
                <!--            
                <privileges check="false">
                        <schema name="TESTDB" dml="0110" >
                                <table name="tb01" dml="0000"></table>
                                <table name="tb02" dml="1111"></table>
                        </schema>
                </privileges>           
                 -->
        </user>

        <user name="user">
                <property name="password">user</property>
                <property name="schemas">TESTDB</property>
                <property name="readOnly">true</property>
        </user>

</mycat:server>
