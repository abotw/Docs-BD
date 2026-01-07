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

------

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
mv hadoop-3.3.6 ~/hadoop
```

------

## 5. Step 3 – Configure Environment Variables

Edit `~/.bashrc` or `~/.zshrc`:

```bash
export HADOOP_HOME=~/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
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
```

### Copy Key

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
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

### 7.1 core-site.xml

```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

------

### 7.2 hdfs-site.xml

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

------

### 7.3 mapred-site.xml

```bash
cp mapred-site.xml.template mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

------

### 7.4 yarn-site.xml

```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

------

### 7.5 hadoop-env.sh

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

------

## 8. Step 6 – Format NameNode (One Time Only)

```bash
hdfs namenode -format
```

⚠️ **Do this only once**.

------

## 9. Step 7 – Start Hadoop Cluster

```bash
start-dfs.sh
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

------

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

------

## 14. Common Beginner Errors

### ❌ SSH asks for password

✔ Passwordless SSH not configured correctly

### ❌ JAVA_HOME error

✔ Java path incorrect in `hadoop-env.sh`

### ❌ Permission denied

✔ HDFS directories owned by wrong user

------

## 15. From Here to Real Clusters

Once comfortable, next steps are:

1.  Multi-node Hadoop cluster
2.  Hive installation
3.  Spark on YARN
4.  HDFS performance tuning

------

## 16. Mental Model

>   One machine pretending to be a cluster
>   All Hadoop daemons talking via SSH
>   Same concepts as production