# Hadoop-02

In this guide, we will build a 3-node cluster. We will use **hadoop01** as our master node, **hadoop02** as our Resource Manager, and **hadoop03** for backup services.

------

## Step 1: Setting up SSH Password-less Login

Hadoop nodes need to communicate with each other automatically. SSH keys allow them to log in to one another without a password1.

### On hadoop01 (The Master):

1.  **Generate the keys**:

    ```bash
    cd ~ && mkdir .ssh && cd .ssh/
    ssh-keygen -t rsa 
    # Press Enter 3 times for default settings
    ```

2.  **Distribute the "ID card" to all nodes**:

    ```bash
    ssh-copy-id hadoop01
    ssh-copy-id hadoop02
    ssh-copy-id hadoop03
    ```

### On hadoop02 (The Resource Manager):

Repeat the process so hadoop02 can also talk to the others2:

```Bash
ssh-keygen -t rsa
ssh-copy-id hadoop01
ssh-copy-id hadoop02
ssh-copy-id hadoop03
```

------

## Step 2: Hadoop Installation & Environment

Now we prepare the Hadoop software and tell the system where to find it3.

1.  **Extract the software**:

    ```Bash
    # Move your tar package to /usr/local/soft
    tar -zxvf hadoop-3.3.4.tar.gz
    mv hadoop-3.3.4 hadoop
    ```

2.  Set Environment Variables:

    Edit the profile: `vim /etc/profile.d/my_env.sh`. Paste the following:

    ```Bash
    export HADOOP_HOME=/usr/local/soft/hadoop
    export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
    # Define root as the user for all Hadoop services
    export HDFS_NAMENODE_USER=root
    export HDFS_DATANODE_USER=root
    export HDFS_JOURNALNODE_USER=root
    export HDFS_SECONDARYNAMENODE_USER=root
    export YARN_RESOURCEMANAGER_USER=root
    export YARN_NODEMANAGER_USER=root
    ```

3.  **Sync to all nodes**:

    ```Bash
    scp -r /etc/profile.d/my_env.sh hadoop02:/etc/profile.d/my_env.sh
    scp -r /etc/profile.d/my_env.sh hadoop03:/etc/profile.d/my_env.sh
    # Run this on ALL THREE machines:
    source /etc/profile.d/my_env.sh
    ```

------

## Step 3: Configuring the Cluster

We need to edit 5 main configuration files in `/usr/local/soft/hadoop/etc/hadoop/`.

### 1. core-site.xml (The Core)

This tells Hadoop where the NameNode is and where to store data.

```XML
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop01:8020</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/usr/local/soft/hadoop/data</value>
    </property>
</configuration>
```

### 2. hdfs-site.xml (The Storage)

Defines web addresses and data redundancy.

```XML
<configuration>
    <property>
        <name>dfs.namenode.http-address</name>
        <value>hadoop01:9870</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop03:9868</value>
    </property>
</configuration>
```

### 3. yarn-site.xml (The Manager)

Designates the Resource Manager and sets memory limits.

```XML
<configuration>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop02</value>
    </property>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

### 4. mapred-site.xml (The Engine)

Tells MapReduce to run on YARN.

```XML
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

### 5. workers

List your worker nodes: `vim workers`.

```
hadoop01
hadoop02
hadoop03
```

------

## Step 4: Launching the Cluster

1.  **Distribute your hard work**:

    ```Bash
    cd /usr/local/soft/
    scp -r hadoop hadoop02:/usr/local/soft/
    scp -r hadoop hadoop03:/usr/local/soft/
    ```

2.  **Format and Start**:

    ```Bash
    # Run ONLY on hadoop01 the first time
    hdfs namenode -format
    start-dfs.sh
    # Run on hadoop02
    start-yarn.sh
    ```

------

## Step 5: Automation Script (`hdp.sh`)

Create a script in `/root/bin/hdp.sh` to start/stop everything at once.

```Bash
#!/bin/bash
case $1 in
"start")
    ssh hadoop01 "start-dfs.sh" [cite: 238]
    ssh hadoop02 "start-yarn.sh" [cite: 240]
    ssh hadoop01 "mapred --daemon start historyserver" [cite: 242]
;;
"stop")
    ssh hadoop01 "mapred --daemon stop historyserver" [cite: 247]
    ssh hadoop02 "stop-yarn.sh" [cite: 249]
    ssh hadoop01 "stop-dfs.sh" [cite: 251]
;;
esac
```

**Don't forget to give it permission**: `chmod +x hdp.sh`. You can now verify everything is running by typing `jps` on each node.