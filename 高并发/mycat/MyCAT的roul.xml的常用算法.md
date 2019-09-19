常用的分片算法
1.配置简单取模
    可以拥有分片列为整数列表的表
    分片列mod分片基数
<tableRole name="你的表名">
    <rule>
        <columns>你的字段名></columns>
        <algorithm>你的函数名</algorithm>
     </rule>
</tableRole>
<function name="你的函数名"
    class="io.mycat.route.function.PartitionByModel"
   分片的个数
    <property name="count">2</property>
</function>
2.配置哈希取模
    可以拥有多种数据类型 比如字符串，日期等
    hash(分片列) mod 分片基数
<tableRole name="你的表名">
    <rule>
        <columns>你的字段名></columns>
        <algorithm>你的函数名</algorithm>
     </rule>
</tableRole>
<function name="你的函数名"
    class="io.mycat.route.function.PartitionByHashModel"
    <property name="count">2</property> 
</function>

3.枚举分片
    可以人为制定数据在哪个数据库
    
<tableRole name="你的表名">
    <rule>
        <columns>你的字段名></columns>
        <algorithm>你的函数名</algorithm>
     </rule>
</tableRole>
<function name="你的函数名"
    class="io.mycat.route.function.PartitionByFileMap"
    <property name="mapFile">partition-hash-int.txt</property> 
    <property name="type">0</property>
    <property name="defaultNode">0</property> 》=启用默认节点
    《0不启用默认节点
</function>
partition-hash-int.txt
你可能存在的值=你的库
DEFAULT_NODE=0

例如
100=0
1001=1
DEFAULT_NODE=0

字符串范围取模分片 
<function name="你的函数名"
    class="io.mycat.route.function.PartitionByPrefixPattern"
    <property name="mapFile">partition-hash-int.txt</property> 
    取模基数
    <property name="patternValue">128</property>
    取模字符长度
    <property name="prefixLength">2</property>
</function>

0-63=0
64-127=1


