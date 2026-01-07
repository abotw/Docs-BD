# Hive-02: Installation

This guide provides the steps to deploy Hive 3.1.3 on a Linux environment and configure MySQL as the remote metastore to ensure metadata persistence and support for multi-user access.

## 1. Hive Deployment and Environment Setup

### 1.1 Installation and Extraction

1.  Upload the `hive-3.1.3.tar.gz` package to the `/usr/local/soft` directory.

2.  Extract the archive:

    ```bash
    tar -zxvf /usr/local/soft/hive-3.1.3.tar.gz
    ```

3.  Rename the directory for convenience:

    ```bash
    mv apache-hive-3.1.3-bin/ hive
    ```

### 1.2 Environment Variables

Add Hive to your system path by editing your environment script (e.g., `/etc/profile.d/my_env.sh`):

```bash
# HIVE_HOME
export HIVE_HOME=/usr/local/soft/hive
export PATH=$PATH:$HIVE_HOME/bin
```

Refresh the environment variables:

```bash
source /etc/profile.d/my_env.sh
```

### 1.3 Resolving Library Conflicts

Hive includes a logging implementation that often conflicts with Hadoop's version. To prevent startup errors, rename the following file in the Hive `lib` folder:

```bash
cd /usr/local/soft/hive/lib/
mv log4j-slf4j-impl-2.17.1.jar log4j-slf4j-impl-2.17.1.jar.bak
```

------

## 2. Metastore Configuration (MySQL)

### 2.1 JDBC Driver Setup

Upload the MySQL JDBC driver (`mysql-connector-j-8.0.31.jar`) to the Hive `lib` directory so Hive can communicate with the MySQL database.

### 2.2 Configure hive-site.xml

Navigate to `$HIVE_HOME/conf/` and create a `hive-site.xml` file with the following configuration:

```XML
<?xml version="1.0"?>
<?xml-stylesheet type="1.0" href="configuration.xsl"?>
<configuration>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://hadoop01:3306/metastore?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;allowPublicKeyRetrieval=true</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.cj.jdbc.Driver</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>000000</value>
    </property>

    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
    </property>

    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>hadoop01</value>
    </property>
    <property>
        <name>hive.cli.print.header</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.cli.print.current.db</name>
        <value>true</value>
    </property>
</configuration>
```

### 2.3 Initialize the Metastore

1.  Log into MySQL and create the database:

    ```SQL
    mysql -uroot -p000000
    CREATE DATABASE metastore;
    QUIT;
    ```

2.  Initialize the schema:

    ```Bash
    schematool -initSchema -dbType mysql -verbose
    ```

    *Verify that the process ends with "schemaTool completed".*

------

## 3. Fixing Character Encoding for Comments

By default, Hive metastore uses `Latin1`, which causes issues when using Chinese characters in table or column comments. Modify the following tables in MySQL to support `UTF-8`:

```SQL
USE metastore;
-- Modify Column Comment Encoding
ALTER TABLE COLUMNS_V2 MODIFY COLUMN COMMENT VARCHAR(256) CHARACTER SET utf8;
-- Modify Table Parameter Encoding
ALTER TABLE TABLE_PARAMS MODIFY COLUMN PARAM_VALUE MEDIUMTEXT CHARACTER SET utf8;
```

------

## 4. Launching Hive

Ensure your Hadoop cluster is running before starting the Hive client:

```Bash
# Start Hadoop
hdp.sh start

# Launch Hive
hive
```

Test the connection:

```SQL
hive (default)> SHOW DATABASES;
```

