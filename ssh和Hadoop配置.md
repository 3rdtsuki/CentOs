#### 虚拟机间ssh连接

`/etc/hosts`中保存了每个IP和对应的主机名

`/etc/hostname`修改主机名

##### 不同主机上两台Ubuntu虚拟机之间如何ping通

首先关闭Windows防火墙和Linux防火墙

参考[不同的虚拟机之间怎么保证能互相ping通-百度经验 (baidu.com)](https://jingyan.baidu.com/article/29697b91c04e10ea20de3c9e.html)，将虚拟机设置中网络适配器修改为桥接模式，进入Ubuntu后`ifconfig`可发现IP已经变为主机网段。之后直接`ping ip`即可

##### ssh连接

- 首先，两台机器上都要安装openssh-server，`sudo apt-get install openssh-server`
- 保证双方可以互ping
- 在主节点上ssh对方`ssh user@hostname`，输入yes，输密码，即可远程控制对方的shell。


##### scp：基于ssh传输目录

`scp -r 本地path user@hostname:目的path  `

例如`scp -r ./hadoop user@hostname:~/Downloads/hadoop`

##### ssh免密登录

假设acer要免密访问lenovo，需要把本机的rsa公钥发给lenovo

```bash
$ ssh-keygen -t rsa
$ ssh-copy-id lenovo

$ ssh mika@lenovo # 无需再输密码
```



#### Hadoop配置

hdfs：Hadoop分布式文件系统。

hdfs的系统架构：一个主节点namenode，一个备用节点secondary node，若干从节点datanode



##### hadoop配置

（1）修改`hadoop/etc/hadoop/hadoop-env.sh`中的`JAVA_HOME`

（2）修改`core-site.xml`（设置了文件访问端口9000）

```xml
<configuration>
    <!--namenode地址-->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
    <!--tmp目录-->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/opt/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
    </property>
</configuration>
```

（3）修改`hdfs-site.xml`

首先进行集群部署规划

| 虚拟机名 | acer               | lenovo                      | dell     |
| -------- | ------------------ | --------------------------- | -------- |
| HDFS     | NameNode, DataNode | SecondaryNameNode, DataNode | DataNode |

```xml
<configuration>
    <!--namenode的web端访问地址-->
    <property>
        <name>dfs.namenode.name.http-address</name>
        <value>acer:8088</value>
    </property>
    <!--2nd namenode的web端访问地址-->
    <property>
        <name>dfs.namenode.secondary.name.http-address</name>
        <value>lenovo:8088</value>
    </property>
    
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/opt/hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/opt/hadoop/tmp/dfs/data</value>
    </property>
</configuration>
```



端口信息[Hadoop 2.x常用端口及查看方法_SunWuKong_Hadoop的博客-CSDN博客_查看hdfs端口](https://blog.csdn.net/SunWuKong_Hadoop/article/details/60877698)

| 端口 | 意义                   |
| ---- | ---------------------- |
| 9000 | fs.defaultFS，文件系统 |
| 8088 | http服务端口           |



##### 启动hdfs服务器

```bash
$ cd /opt/hadoop# 先进入hadoop目录
$ rm -rf tmp #清空./tmp目录内容
$ hadoop namenode -format # 初始化
$ ./sbin/start-all.sh # 非root模式运行，开启进程
$ jps # 看到出现NameNode进程

$ ./sbin/stop-all.sh # 关闭所有进程
```

打开资源管理器http://localhost:8088/cluster



##### 传文件到hdfs服务器

```bash
$ hdfs dfs -put  本地文件路径   hdfs上传文件路径
#例如$ hdfs dfs -put  本地文件路径 /data
$ hdfs dfs -ls / # 显示服务器所有文件
```

上传文件后，文件路径为hdfs://localhost:9000/data

[Spark读取和存储HDFS上的数据 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1546814)



`/bin/xysnc`分发脚本：通过同步工具rsync将本机文件同步到其他节点。scp是复制，rsync避免复制相同内容

