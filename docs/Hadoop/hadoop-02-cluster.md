# Hadoop-02: Cluster Setup

## 1. What Is a Hadoop Cluster?

A **Hadoop cluster** is a group of machines working together to run Hadoop services.

For learning, Hadoop supports **three deployment modes**:

| Mode                   | Description                 | Use Case               |
| ---------------------- | --------------------------- | ---------------------- |
| Local                  | Single JVM, no HDFS         | Testing only           |
| **Pseudo-Distributed** | Single machine, all daemons | **Best for beginners** |
| Fully Distributed      | Multiple machines           | Production             |

👉 In this tutorial, we use **pseudo-distributed mode**.

### 1.1 Cluster Planning

A successful deployment requires a clear map of which services run on which hardware.

In this guide, we will build a 3-node cluster. We will use **hadoop01** as our master node, **hadoop02** as our Resource Manager, and **hadoop03** for backup services.

| **Node Name** | **IP Address** | **Services / Roles**                              |
| ------------- | -------------- | ------------------------------------------------- |
| **hadoop01**  | 192.168.184.31 | JDK 1.8, NameNode, DataNode, NodeManager          |
| **hadoop02**  | 192.168.184.32 | JDK 1.8, ResourceManager, DataNode, NodeManager   |
| **hadoop03**  | 192.168.184.33 | JDK 1.8, DataNode, NodeManager, SecondaryNameNode |

注：上表是毕业实习的Hadoop集群。

## 2. System Requirements

### Hardware

-   OS: Linux or macOS (Linux recommended)
-   RAM: ≥ 4 GB
-   Disk: ≥ 20 GB

### Software

-   Java 8 or 11
-   Hadoop 3.x
-   SSH (localhost access)

------

## 3. Step 1 – Install Java

### Check Java

```bash
java -version
```

If not installed:

**Ubuntu / Debian**

```bash
sudo apt update
sudo apt install openjdk-11-jdk
```

**macOS (Homebrew)**

```bash
brew install openjdk@11
```

### Set JAVA_HOME

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

Verify:

```bash
echo $JAVA_HOME
```

------

## 4. Step 2 – Install Hadoop

### Download Hadoop

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```

### Extract

```bash
tar -xvzf hadoop-3.3.6.tar.gz
# Rename
mv hadoop-3.3.6 ~/hadoop
```

## 5. Step 3 – Configure Environment Variables

Edit `~/.bashrc` or `~/.zshrc`:

```bash
export HADOOP_HOME=~/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

# Define root as the user for all Hadoop services
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_JOURNALNODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```

Reload:

```bash
source ~/.bashrc
```

Test:

```bash
hadoop version
```

------

## 6. Step 4 – Enable SSH (Important)

Hadoop requires **passwordless SSH**, even for localhost.

### Generate SSH Key

```bash
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
# Press Enter 3 times for default settings
```

-   `-t` - type

### Copy Key

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

or:

```
ssh-copy-id HOST
```

Test:

```bash
ssh localhost
```

------

## 7. Step 5 – Configure Hadoop Files

Hadoop config files are in:

```bash
$HADOOP_HOME/etc/hadoop
```

------

### 7.1 core-site.xml (The Core)

This tells Hadoop where the **NameNode** is and where to store data.

```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

example:

```xml
<configuration>
    <!-- 指定 NameNode 的地址 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop01:8020</value>
    </property>

    <!-- 指定 Hadoop 数据的存储目录 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/usr/local/soft/hadoop/data</value>
    </property>

    <!-- 配置 HDFS Web 登录使用的静态用户为 root -->
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>root</value>
    </property>

    <!-- 配置 root (superUser) 允许通过代理访问的主机节点 -->
    <property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
    </property>

    <!-- 配置 root (superUser) 允许通过代理用户所属组 -->
    <property>
        <name>hadoop.proxyuser.root.groups</name>
        <value>*</value>
    </property>

    <!-- 配置 root (superUser) 允许通过代理的用户 -->
    <property>
        <name>hadoop.proxyuser.root.users</name>
        <value>*</value>
    </property>
</configuration>
```

### 7.2 hdfs-site.xml (The Storage)

Defines web addresses and data redundancy.

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>

  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///home/youruser/hadoop_data/namenode</value>
  </property>

  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///home/youruser/hadoop_data/datanode</value>
  </property>
</configuration>
```

Create directories:

```bash
mkdir -p ~/hadoop_data/namenode
mkdir -p ~/hadoop_data/datanode
```

example:

```xml
<configuration>
    <!-- NN Web 端访问地址 -->
    <property>
        <name>dfs.namenode.http-address</name>
        <value>hadoop01:9870</value>
    </property>

    <!-- 2NN Web 端访问地址 -->
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop03:9868</value>
    </property>

    <!-- 测试环境指定 HDFS 副本数量为 1 -->
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

### 7.3 mapred-site.xml (The Engine)

Tells MapReduce to run on YARN.

```bash
cp mapred-site.xml.template mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

example:

```xml
<configuration>
    <!-- 指定 MapReduce 程序运行在 YARN 上 -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

    <!-- 历史服务器端地址 -->
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>hadoop01:10020</value>
    </property>

    <!-- 历史服务器 Web 端地址 -->
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>hadoop01:19888</value>
    </property>
</configuration>
```

### 7.4 yarn-site.xml (The Manager)

Designates the Resource Manager and sets memory limits.

```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

example:

```xml
<configuration>
    <!-- 指定 MR 使用 shuffle 服务 -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

    <!-- 指定 ResourceManager 的地址 -->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>node1</value>
    </property>

    <!-- 环境变量的继承 -->
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>
            JAVA_HOME,
            HADOOP_COMMON_HOME,
            HADOOP_HDFS_HOME,
            HADOOP_CONF_DIR,
            CLASSPATH_PREPEND_DISTCACHE,
            HADOOP_YARN_HOME,
            HADOOP_MAPRED_HOME
        </value>
    </property>

    <!-- Yarn 单个容器允许分配的最小 / 最大内存 -->
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>512</value>
    </property>
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>4096</value>
    </property>

    <!-- Yarn 容器可管理的物理内存大小 -->
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>4096</value>
    </property>

    <!-- 关闭 Yarn 对物理内存和虚拟内存的限制检查 -->
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
    </property>

    <!-- 开启日志聚集功能 -->
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>

    <!-- 设置日志聚集服务器地址 -->
    <property>
        <name>yarn.log.server.url</name>
        <value>http://hadoop01:19888/jobhistory/logs</value>
    </property>

    <!-- 设置日志保留时间为 7 天 -->
    <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
    </property>
</configuration>
```

### 7.5 workders

List your worker nodes: `vim workers`.

```
hadoop01
hadoop02
hadoop03
```

### 7.6 hadoop-env.sh

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

## 8. Step 6 – Format NameNode (One Time Only)

```bash
# Run ONLY on hadoop01 the first time
hdfs namenode -format
```

⚠️ **Do this only once**.

## 9. Step 7 – Start Hadoop Cluster

```bash
start-dfs.sh
# Run on hadoop02
start-yarn.sh
```

Verify:

```bash
jps
```

Expected output:

```
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
```

## 10. Step 8 – Web UIs

| Service         | URL                                             |
| --------------- | ----------------------------------------------- |
| NameNode        | [http://localhost:9870](http://localhost:9870/) |
| ResourceManager | [http://localhost:8088](http://localhost:8088/) |

------

## 11. Step 9 – Test HDFS

### Create Directory

```bash
hdfs dfs -mkdir /input
```

### Upload File

```bash
hdfs dfs -put README.txt /input
```

### List Files

```bash
hdfs dfs -ls /input
```

------

## 12. Step 10 – Run a Sample MapReduce Job

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /input /output
```

View result:

```bash
hdfs dfs -cat /output/part-r-00000
```

------

## 13. Stop Hadoop

```bash
stop-yarn.sh
stop-dfs.sh
```

## 14. Automation Script (`hdp.sh`)

Create a script in `/root/bin/hdp.sh` to start/stop everything at once.

```Bash
#!/bin/bash

# Check if at least one argument is provided
if [ $# -lt 1 ]; then
    echo "No Args Input..."
    exit 1
fi

case $1 in
    "start")
        echo "=================== Starting Hadoop Cluster ==================="

        echo "--------------- Starting HDFS ---------------"
        ssh hadoop01 "start-dfs.sh"

        echo "--------------- Starting YARN ---------------"
        ssh hadoop02 "start-yarn.sh"

        echo "--------------- Starting HistoryServer ---------------"
        ssh hadoop01 "mapred --daemon start historyserver"
        ;;

    "stop")
        echo "=================== Stopping Hadoop Cluster ==================="

        echo "--------------- Stopping HistoryServer ---------------"
        ssh hadoop01 "mapred --daemon stop historyserver"

        echo "--------------- Stopping YARN ---------------"
        ssh hadoop02 "stop-yarn.sh"

        echo "--------------- Stopping HDFS ---------------"
        ssh hadoop01 "stop-dfs.sh"
        ;;

    *)
        echo "Input Args Error..."
        ;;
esac
```

**Don't forget to give it permission**: `chmod +x hdp.sh`. You can now verify everything is running by typing `jps` on each node.

```sh
 hdp.sh stop
 hdp.sh start
```

## 15. Common Beginner Errors

### ❌ SSH asks for password

✔ Passwordless SSH not configured correctly

### ❌ JAVA_HOME error

✔ Java path incorrect in `hadoop-env.sh`

### ❌ Permission denied

✔ HDFS directories owned by wrong user

## 16. From Here to Real Clusters

Once comfortable, next steps are:

1.  Multi-node Hadoop cluster
2.  Hive installation
3.  Spark on YARN
4.  HDFS performance tuning

## 17. Mental Model

>   One machine pretending to be a cluster
>   All Hadoop daemons talking via SSH
>   Same concepts as production