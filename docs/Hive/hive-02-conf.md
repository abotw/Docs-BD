# Hive-02: Conf

## 1. What is Apache Hive?

**Apache Hive** is a **data warehouse system** built on top of **Hadoop**.
It allows you to query large datasets stored in **HDFS** using **SQL-like language (HiveQL)** instead of writing complex MapReduce code.

### Key features

-   SQL-like syntax (easy to learn)
-   Runs on Hadoop
-   Suitable for batch data analysis
-   Commonly used in big data platforms

------

## 2. Prerequisites

Before installing Hive, make sure the following components are installed and working.

### 2.1 Operating System

-   Linux (CentOS / Rocky Linux / Ubuntu)
-   64-bit system

### 2.2 Java (Required)

Hive runs on Java.

```bash
java -version
```

Recommended:

-   **JDK 8** or **JDK 11**

If Java is not installed, install it first.

------

### 2.3 Hadoop (Required)

Hive depends on Hadoop (HDFS + YARN).

Check Hadoop:

```bash
hadoop version
```

Make sure:

-   HDFS is running
-   You can execute `hdfs dfs -ls /`

If Hadoop is not installed, install Hadoop **before** Hive.

## 3. Download Apache Hive

### 3.1 Choose a Version

It is recommended to match Hive with your Hadoop version.

Example:

-   Hadoop 3.x → Hive 3.x

### 3.2 Download Hive

```bash
cd /opt
wget https://downloads.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
```

## 4. Install Hive

### 4.1 Extract Hive

```bash
tar -zxvf apache-hive-3.1.3-bin.tar.gz
```

-   `z` - gzip
-   `x` - extract
-   `v` - verbose
-   `f` - file

Rename for convenience:

```bash
mv apache-hive-3.1.3-bin hive
```

Hive directory:

```bash
/opt/hive
```

## 5. Configure Environment Variables

### 5.1 Edit `/etc/profile` (or `~/.bashrc`)

```bash
vi /etc/profile
```

Add the following:

```bash
# HIVE_HOME
export HIVE_HOME=/opt/hive
export PATH=$PATH:$HIVE_HOME/bin
```

Apply:

```bash
source /etc/profile
```

Verify:

```bash
hive --version
```

## 5.2 Resolving Library Conflicts

Hive includes a logging implementation that often conflicts with Hadoop's version. To prevent startup errors, rename the following file in the Hive `lib` folder:

```bash
cd $HIVE_HOME/lib/
mv log4j-slf4j-impl-2.17.1.jar log4j-slf4j-impl-2.17.1.jar.bak
```

## 6. Configure Hive

Hive configuration files are located in:

```bash
$HIVE_HOME/conf
```

### 6.1 Create `hive-site.xml`

Hive does not provide this file by default.

```bash
cd $HIVE_HOME/conf
cp hive-default.xml.template hive-site.xml
```

------

### 6.2 Configure Metastore (Very Important)

Hive needs a **metastore** to store table metadata.

There are three types:

-   Embedded Derby (default, **only for testing**)
-   MySQL / MariaDB (**recommended**)
-   PostgreSQL

For beginners, **MySQL** is recommended.

------

## 7. Install and Configure MySQL (Metastore)

### 7.1 Install MySQL

```bash
yum install mysql-server -y
systemctl start mysqld
systemctl enable mysqld
```

------

### 7.2 Create Hive Metastore Database

```bash
mysql -u root -p
```

Inside MySQL:

```sql
CREATE DATABASE hive_metastore;
CREATE USER 'hive'@'localhost' IDENTIFIED BY 'hive';
GRANT ALL PRIVILEGES ON hive_metastore.* TO 'hive'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

------

### 7.3 Add MySQL JDBC Driver

Download MySQL Connector:

```bash
wget https://downloads.mysql.com/archives/get/p/3/file/mysql-connector-java-8.0.25.tar.gz
```

Extract:

```bash
tar -zxvf mysql-connector-java-8.0.25.tar.gz
```

Copy JAR to the Hive `lib` directory so Hive can communicate with the MySQL database.

```bash
cp mysql-connector-java-8.0.25/mysql-connector-java-8.0.25.jar $HIVE_HOME/lib/
```

------

## 8. Edit `hive-site.xml`

Open:

```bash
vi $HIVE_HOME/conf/hive-site.xml
```

Add the following properties **inside `<configuration>`**:

```xml
<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://localhost:3306/hive_metastore?useSSL=false</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.cj.jdbc.Driver</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>hive</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value>hive</value>
</property>

<property>
  <name>hive.metastore.warehouse.dir</name>
  <value>/user/hive/warehouse</value>
</property>
```

毕业实习的配置如下：

```xml
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

## 9. Initialize Hive Metastore

### 9.1 Create Warehouse Directory in HDFS

```bash
hdfs dfs -mkdir -p /user/hive/warehouse
hdfs dfs -chmod -R 777 /user/hive
```

------

### 9.2 Initialize Schema

Log into MySQL and create the database:

```
mysql -uroot -p
> CREATE DATABASE metastore;
> QUIT;
```

Then initializa schema:

```bash
schematool -initSchema -dbType mysql -verbose
```

If successful, you will see:

```
schemaTool completed
```

### 9.3 Fixing Character Encoding for Comments

By default, Hive metastore uses `Latin1`, which causes issues when using Chinese characters in table or column comments. Modify the following tables in MySQL to support `UTF-8`:

```SQL
USE metastore;

-- Modify Column Comment Encoding
ALTER TABLE COLUMNS_V2 MODIFY COLUMN COMMENT VARCHAR(256) CHARACTER SET utf8;

-- Modify Table Parameter Encoding
ALTER TABLE TABLE_PARAMS MODIFY COLUMN PARAM_VALUE MEDIUMTEXT CHARACTER SET utf8;
```

## 10. Start Hive

### 10.1 Start Hive Shell

Ensure your Hadoop cluster is running before starting the Hive client:

```bash
# Start Hadoop
hdp.sh start

# Launch Hive
hive
```

You should see:

```text
hive>
```

------

### 10.2 Test Hive

```sql
SHOW DATABASES;
```

Create a database:

```sql
CREATE DATABASE testdb;
SHOW DATABASES;
```

------

## 11. Common Problems for Beginners

### 11.1 `java.lang.NoSuchMethodError`

-   Version conflict between Hadoop and Hive
-   Ensure compatible versions

------

### 11.2 Cannot Connect to Metastore

Check:

-   MySQL is running
-   JDBC driver is in `$HIVE_HOME/lib`
-   Database user/password is correct

------

### 11.3 Permission Denied in HDFS

```bash
hdfs dfs -chmod -R 777 /user/hive
```

------

## 12. Hive Configuration Files Summary

| File                   | Purpose                    |
| ---------------------- | -------------------------- |
| hive-site.xml          | Main configuration         |
| hive-env.sh            | Hive environment variables |
| hive-log4j2.properties | Logging                    |
| core-site.xml          | Hadoop config              |
| hdfs-site.xml          | HDFS config                |

------

## 13. Next Steps

After installation, you can learn:

-   HiveQL basics (`SELECT`, `WHERE`, `GROUP BY`)
-   External vs Managed tables
-   ODS / DWD / DWS / ADS architecture
-   Hive on Tez / Spark

------

## 14. Quick Checklist

✅ Java installed
✅ Hadoop running
✅ MySQL metastore configured
✅ Warehouse directory created
✅ `hive` command works