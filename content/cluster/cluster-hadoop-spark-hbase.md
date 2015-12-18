---
title: Hadoop、Spark、HBase集群的安装与配置
date: 2015-12-15 20:20:11
tag: cluster
---
[TOC]

### 集群环境
三台机器，分别为```namenode```，```datanode1```， ```datanode2```，配置为1 master + 2 slaves 的结构。

### 修改hostname
```bash
vim /etc/hostname
vim /etc/hosts
sudo service hostname start
hostname
```
### 添加一个统一的用户hadoop
```
sudo adduser hadoop
sudo usermod -a -G sudo hadoop
```
### ssh免密码登陆

创建公钥
```
ssh-keygen -t rsa
```

把公钥id_rsa.pub复制到远程机器的 /home/username/.ssh目录并命名为authorized_keys
```
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"
```

### 安装scala
解压至/usr/lib目录，修改系统变量
```bash
#set scala environment
export SCALA_HOME=/usr/lib/scala-2.10.6
export PATH=$PATH:$SCALA_HOME/bin
```

```
source /etc/profile
scala -version
```

### 安装配置Hadoop和Yarn

#### 在master上配置hadoop (/opt目录下)
解压至/opt目录下
```
sudo tar -xzvf hadoop-2.7.1.tar.gz
```

更改文件夹权限
```
chown –R hadoop:hadoop hadoop-2.7.1
```

需要配置有以下几个文件:```hadoop-env.sh```,```yarn-env.sh```,```mapred-env.sh```,```core-site.xml```,```hdfs-site.xml```,```maprd-site.xml```,```yarn-site.xml```,```slaves```.

在```hadoop-env.sh```,```yarn-env.sh```,```mapred-env.sh```中配置一些环境变量，如java路径等。

##### 配置系统变量
```bash
#set hadoop environment
export HADOOP_PREFIX=/opt/hadoop-2.7.1
export HADOOP_COMMON_HOME=$HADOOP_PREFIX
export HADOOP_HDFS_HOME=$HADOOP_PREFIX
export HADOOP_MAPRED_HOME=$HADOOP_PREFIX
export HADOOP_YARN_HOME=$HADOOP_PREFIX
export HADOOP_CONF_DIR=$HADOOP_PREFIX/etc/hadoop
export YARN_CONF_DIR=$HADOOP_PREFIX/etc/hadoop
export PATH=$PATH:$HADOOP_PREFIX/bin:$HADOOP_PREFIX/sbin:$HADOOP_PREFIX/lib
```

##### 配置core-site.xml文件
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://namenode:9000</value>
        <description>The name of the default file system.</description>
    </property>

    <property>
        <name>hadoop.tmp.dir</name>
        <!-- remember to create the directory -->
        <value>/opt/hadoop-2.7.1/tmp</value>
        <description>A base for other temporary directories.</description>
    </property>

    <property>
        <name>io.file.buffer.size</name>
        <value>131072</value>
    </property>

</configuration>
```

##### 配置hdfs-site.xml文件
```xml
<configuration>

    <property>
        <name>dfs.nameservices</name>
        <value>hadoop-cluster</value>
    </property>

    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>namenode:50090</value>
    </property>

    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///opt/hadoop-2.7.1/dfs/name</value>
        <final>true</final>
    </property>

    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///opt/hadoop-2.7.1/dfs/data</value>
        <final>true</final>
    </property>

    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>

    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>

    <property>
        <name>dfs.permissions</name>
        <value>true</value>
    </property>

</configuration>
```

##### 配置maprd-site.xml文件
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <final>true</final>
    </property>

    <property>
        <name>mapreduce.jobtracker.http.address</name>
        <value>nameNode:50030</value>
    </property>

    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>namenode:10020</value>
    </property>

    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>namenode:19888</value>
    </property>

    <property>
        <name>mapreduce.jobhistory.intermediate-done-dir</name>
        <value>/mr-history/tmp</value>
    </property>

    <property>
        <name>mapreduce.jobhistory.done-dir</name>
        <value>/mr-history/done</value>
    </property>
</configuration>
```

##### 配置yarn-site.xml文件
```xml
<configuration>

<!-- Site specific YARN configuration properties -->

    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

    <property>
        <name>yarn.resourcemanager.address</name>
        <value>namenode:8032</value>
    </property>

    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>namenode:8030</value>
    </property>

    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>namenode:8031</value>
    </property>

    <property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>namenode:8033</value>
    </property>

    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>namenode:8088</value>
    </property>


    <property>
        <name>yarn.nodemanager.local-dirs</name>
        <value>${hadoop.tmp.dir}/nodemanager/local</value>
    </property>

    <property>
        <name>yarn.nodemanager.log-dirs</name>
        <value>${hadoop.tmp.dir}/nodemanager/logs</value>
    </property>

    <property>
    <name>yarn.nodemanager.log.retain-seconds</name>
    <value>10800</value>
    </property>

    <property>
        <!-- Where to aggregate logs to. -->
        <name>yarn.nodemanager.remote-app-log-dir</name>
        <value>${hadoop.tmp.dir}/nodemanager/remote</value>
    </property>

    <property>
        <name>yarn.nodemanager.remote-app-log-dir-suffix</name>
        <value>logs</value>
    </property>

    <property>
        <!-- enable log aggregation -->
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>

    <property>
        <!-- How long to keep aggregation logs before deleting them. -1 disables. -->
        <name>yarn.log-aggregation.retain-seconds</name>
        <!-- three days -->
        <value>259200</value>
    </property>

    <property>
        <!-- How long to wait between aggregated log retention checks. If set to 0 or a negative value then the value is computed as one-tenth of the aggregated log retention time.  -->
        <name>yarn.log-aggregation.retain-check-interval-seconds</name>
        <value>-1</value>
    </property>
</configuration>
```
##### 配置slaves文件
```
datanode1
datanode2
```

#### 将master上的hadoop目录分发到其他节点 (/opt目录)
注意使用chown更改权限

#### 启动和测试

##### 在Master執行格式化
```
hdfs namenode -format
```

##### 启动hdfs和yarn
```
start-dfs.sh
start-yarn.sh
```

##### 检验相应进程是否启动
jps

master上:  
- NameNode  
- ResourceManager  

slave上:  
- DataNode  
- NodeManager  

##### 访问web管理界面
http://namenode:50070  
http://namenode:8088  

### 安装配置Spark ###

#### 在master上配置Spark
解压至/opt目录下，并注意更改文件夹权限


##### 配置系统变量
```bash
#set spark environment
export SPARK_HOME=/opt/spark-1.5.2
export PATH=$PATH:$SPARK_HOME/bin
```

##### 配置spark-env.sh
如果使用的版本没有提供hadoop，记得要配置```SPARK_DIST_CLASSPATH```，参考[Using Spark's "Hadoop Free" Build](https://spark.apache.org/docs/latest/hadoop-provided.html)。
```bash
cp spark-env.sh.template spark-env.sh

#set jdk environment
JAVA_HOME=/usr/lib/jvm/jdk8
SCALA_HOME=/usr/lib/scala-2.10.6

HADOOP_HOME=/opt/hadoop-2.7.1
HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop

SPARK_HOME=/opt/spark-1.5.2

#remember to provide hadoop classes, since it is not included in this distribution
SPARK_DIST_CLASSPATH=$($HADOOP_HOME/bin/hadoop classpath)

SPARK_MASTER_IP=namenode
SPARK_LOCAL_DIRS=/opt/spark-1.5.2
```

##### 配置slaves文件

#### 将文件夹分发至其它节点

#### 启动并测试
```
sbin/start-all.sh
```

jps 在master和slave节点上分别能看到Master和Worker进程

http://master:8080进入web界面，可以看到节点信息。

#### 配置Spark History Server[参考](http://spark.apache.org/docs/latest/monitoring.html)

修改```spark-default.conf```文件：
```bash
#是否记录作业产生的事件或者运行状态(job，stage等使用内存等信息)
spark.eventLog.enabled			true
#日志存放位置
spark.eventLog.dir			    hdfs://namenode:9000/user/spark/logs
#是否对日志启用压缩
spark.eventLog.compress			true
```

修改```spark-env.sh```文件：
```bash
#指定history-server读取日志文件的位置，如不在环境变量中指定，则需要在启动history-server的命令中指定
#更新频率设为30s
SPARK_HISTORY_OPTS='-Dspark.history.fs.logDirectory=hdfs://namenode:9000/user/spark/logs -Dspark.history.fs.update.interval=30'
```

访问namenode:18080即可查看

Spark History Server还提供了一些REST接口，详细见[参考](http://spark.apache.org/docs/latest/monitoring.html)。

### 安装配置HBase

#### 在master上配置HBase
解压至/opt目录下，并注意更改文件夹权限

##### 配置系统变量
```bash
#set hbase environment
export HBASE_HOME=/opt/hbase-1.0.2
export PATH=$PATH:$HBASE_HOME/bin
```

##### 配置hbase-env.sh
```bash
export JAVA_HOME=/usr/lib/jvm/jdk8
export HBASE_CLASSPATH=/opt/hadoop-2.7.1/etc/hadoop
export HBASE_MANAGES_ZK=true
```
其中，```HBASE_MANAGES_ZK=true```表明将使用HBase内置的zookeeper，也可以单独安装zookeep，这里暂不作介绍。

##### 配置hbase-site.xml
```xml
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <!-- keep same with haddop core-site.xml -->
        <value>hdfs://namenode:9000/hbase</value>
    </property>

    <property>
        <!-- true means distributed mode -->
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>

    <property>
        <!-- zookeeper server location, should be odd number -->
        <name>hbase.zookeeper.quorum</name>
        <value>namenode,datanode1,datanode2</value>
    </property>

    <property>
        <!-- dir for sava zookeep property data -->
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/opt/hbase-1.0.2/zookeeper/data</value>
    </property>

    <property>
        <name>hbase.tmp.dir</name>
        <value>/opt/hbase-1.0.2/tmp</value>
    </property>

</configuration>
```
第一个属性指定本机的hbase的存储目录，必须与Hadoop集群的core-site.xml文件配置保持一致；第二个属性指定hbase的运行模式，true代表全分布模式；第三个属性指定 Zookeeper 管理的机器，一般为奇数个；第四个属性是zookeeper属性文件存放的路径。

##### 配置regionservers文件
```
datanode1
datanode2
```

#### 将目录分发至所有节点

#### 启动HBase
```
./bin/start-hbase.sh
```

jps 在master和slave节点上分别能看到HMaster和HRegionServer进程，以及HQuorumPeer。

访问namenode:16010可以看到web UI.

#### 常见问题(根据log定位问题)

##### 时间同步
所有节点的时间必须是同步的，注意配置好ntp。

##### 修改 ulimit 限制
HBase 会在同一时间打开大量的文件句柄和进程，超过 Linux 的默认限制，导致可能会出现如下错误：
```
2010-04-06 03:04:37,542 INFO org.apache.hadoop.hdfs.DFSClient: Exception increateBlockOutputStream java.io.EOFException
2010-04-06 03:04:37,542 INFO org.apache.hadoop.hdfs.DFSClient: Abandoning block blk_-6935524980745310745_1391901
```
所以编辑```/etc/security/limits.conf```文件，添加以下两行，提高能打开的句柄数量和进程数量:
```
hadoop  -       nofile  32768
hadoop  -       nproc   32000
```

还需要在 ```/etc/pam.d/common-session``` 加上这一行，否则配置不会生效:
```
session required pam_limits.so
```

最后还要注销（logout或者exit）后再登录，这些配置才能生效！使用```ulimit -n -u```命令查看最大文件和进程数量是否改变了。记得在每台安装 HBase 的机器上运行。

### 安装配置Sqoop

#### 下载解压

#### 配置环境变量
- SQOOP_HOME
- PATH

#### 配置sqoop-env.sh

#### 驱动路径 $SQOOP_HOME/lib

#### sqoop help查看具体命令

#### [文档](http://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html)
