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



`/bin/xysnc`分发脚本：通过同步工具rsync将本机文件同步到其他节点的相同位置`xsync filename`。scp是复制，rsync避免复制相同内容



##### ssh免密登录

假设acer要免密访问lenovo，需要把本机的rsa公钥发给lenovo

```shell
ssh-keygen -t rsa
ssh-copy-id lenovo

ssh mika@lenovo # 无需再输密码
```



#### Hadoop配置

hdfs：Hadoop分布式文件系统。

hdfs的系统架构：一个主节点namenode，一个备用节点secondary node，若干从节点datanode



##### 1.hadoop配置

（1）修改`/home/mika/Documents/hadoop/etc/hadoop/hadoop-env.sh`中的`JAVA_HOME`

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
        <value>file:/home/mika/Documents/hadoop/tmp</value>
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
        <value>file:/home/mika/Documents/hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/home/mika/Documents/hadoop/tmp/dfs/data</value>
    </property>
</configuration>
```

注：Hadoop端口信息

[Hadoop 2.x常用端口及查看方法_SunWuKong_Hadoop的博客-CSDN博客_查看hdfs端口](https://blog.csdn.net/SunWuKong_Hadoop/article/details/60877698)

| 端口 | 意义                   |
| ---- | ---------------------- |
| 9000 | fs.defaultFS，文件系统 |
| 8088 | http服务端口           |

（4）修改hadoop/etc/hadoop/workers

```
acer
lenovo
dell
```

xsync分发到各节点



##### 2.启动hadoop集群

```shell
cd /home/mika/Documents/hadoop# 先进入hadoop目录
rm -rf tmp #清空./tmp目录内容
hadoop namenode -format # 初始化
sbin/start-all.sh # 非root模式运行，开启进程
jps # 看到出现NameNode进程

sbin/stop-all.sh # 关闭所有进程
```

打开资源管理器http://localhost:8088/cluster

可以看到主节点上出现进程：namenode, datanode

<img src="pic/namenode.png" style="zoom: 80%;" />

从节点上：datanode

<img src="pic/datanode.png" style="zoom:80%;" />

##### 3.spark配置

修改spark下的workers，参考[Spark 2.0分布式集群环境搭建_厦大数据库实验室博客 (xmu.edu.cn)](http://dblab.xmu.edu.cn/blog/1187-2/)

在`./sbin/spark-config.sh`末尾添加`JAVA_HOME`

xsync分发到各节点



##### 4.启动spark集群

```shell
# 启动
sbin/start-master.sh
sbin/start-slaves.sh

# 关闭
sbin/stop-master.sh
sbin/stop-slaves.sh
```

master节点jps后，可以看到出现Master, Worker进程；slavers节点出现Worker进程

<img src="pic/spark_master.png" style="zoom:80%;" />

##### start脚本启动所有进程

为了方便，可编写脚本`/home/mika/Documents/start.sh`，通过`./start.sh`运行

```shell
#!/bin/shell
cd /home/mika/Documents/hadoop
rm -rf tmp
hadoop namenode -format
sbin/start-all.sh
cd /home/mika/Documents/spark
sbin/start-master.sh
sbin/start-slaves.sh
jps
```



##### stop脚本停止所有进程

`/home/mika/Documents/stop.sh`停止所有进程，通过`./stop.sh`运行

```shell
#!/bin/shell
cd /home/mika/Documents/spark
sbin/stop-master.sh
sbin/stop-slaves.sh
cd /home/mika/Documents/hadoop
sbin/stop-all.sh
jps
```

#### 传文件到hdfs

```shell
hdfs dfs -put  本地文件路径   hdfs上传文件路径
#例如hdfs dfs -put  本地文件路径 /data
hdfs dfs -ls / # 显示服务器所有文件
```

上传文件后，文件路径为hdfs://localhost:9000/data

[Spark读取和存储HDFS上的数据 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1546814)

