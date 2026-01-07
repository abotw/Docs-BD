---
date: Tue Dec 30 13:30:24 CST 2025
---

# Hadoop-01: Cluster Setup and Configuration

Before installing **Hadoop**, you must prepare the environment across multiple virtual machines. The following steps must be performed on all three machines in the cluster (`handoop01`, `handoop02`, and `hadoop03`).

## 1. Cluster Planning

A successful deployment requires a clear map of which services run on which hardware.

| **Node Name** | **IP Address** | **Services / Roles**                              |
| ------------- | -------------- | ------------------------------------------------- |
| **hadoop01**  | 192.168.184.31 | JDK 1.8, NameNode, DataNode, NodeManager          |
| **hadoop02**  | 192.168.184.32 | JDK 1.8, ResourceManager, DataNode, NodeManager   |
| **hadoop03**  | 192.168.184.33 | JDK 1.8, DataNode, NodeManager, SecondaryNameNode |

## 2. Basic Environment Configuration

If you are using a Virtual Machine (VM), you can configure one "Master" machine and then clone it to create the other two nodes to save time.

### A. Network Configuration 

To ensure nodes can always find each other, we must change the dynamic IP to a **Static IP**.

1.  Open the network configuration file:

    `vim /etc/sysconfig/network-scripts/ifcfg-ens33`

2.  Update the following fields:

    -   `BOOTPROTO=static`: Tells the system to use a manual IP instead of DHCP.
    -   `ONBOOT=yes`: Ensures the network starts automatically on boot.
    -   `IPADDR`: Set this to your specific machine's IP (e.g., 192.168.184.31).

3.  Restart the network services to apply changes:

    ```bash
    systemctl stop NetworkManager
    systemctl disable NetworkManager
    service network restart
    ```

### B. Security and Host Mapping

1.  **Disable the Firewall**: Firewalls can block communication between Hadoop nodes.

    ```sh
    systemctl stop firewalld.service
    systemctl disable firewalld.service
    ```

2.  **Configure Hostname Mapping**: This allows you to use names (like `hadoop01`) instead of IP addresses. Edit `/etc/hosts` and add:

    ```
    192.168.184.31 hadoop01
    192.168.184.32 hadoop02
    192.168.184.33 hadoop03
    ```
    
    Pattern: `IPADDR HOSTNAME`

### C. Update Package Repository (Yum Sources)

We switch to Aliyun mirrors to speed up software downloads in China.

```bash
cd /etc/yum.repos.d
# 1. Backup original
mv CentOS-Base.repo CentOS-Base.repo.backup
# 2. Download new source
wget http://mirrors.aliyun.com/repo/Centos-7.repo
# 3. Set as default
mv Centos-7.repo CentOS-Base.repo
```

## 3. Java Development Kit (JDK) Installation

Hadoop is built on Java, so a specific version (JDK 1.8) is required.

1.  Remove default Java: Centos 7 often comes with a pre-installed version that may conflict.

    `rpm -qa | grep -i java | xargs -n1 rpm -e --nodeps`

    -q: **q**uery

    -a: **a**ll

    -i: case **i**nsensitive

    -e: **e**rase
    deps: **dep**endencie**s**

    -n: **n**ext

2.  Create Installation Directory:

    `mkdir -p /usr/local/soft`

3.  Extract JDK: Upload your `jdk-8u212-linux-x64.tar.gz` to the folder and extract it.

    `tar -zxvf jdk-8u212-linux-x64.tar.gz`

    `tar -xzvf` - to extract a .tgz or .tar.gz archive

4.  **Set Environment Variables**: This tells the system where Java is located. Create a new script `vim /etc/profile.d/my_env.sh` and add:

    ```sh
    export JAVA_HOME=/usr/local/soft/jdk1.8.0_212
    export PATH=$PATH:$JAVA_HOME/bin
    ```

5.  Refresh and Verify:

    `source /etc/profile.d/my_env.sh`

    Check the version by running `java -version`. You should see "1.8.0_212" in the output.

## Key Commands Explained for Beginners

-   **`vim`**: A text editor used in the terminal to modify configuration files.
-   **`systemctl stop/disable`**: Used to turn off services immediately and prevent them from starting when the computer reboots.
-   **`tar -zxvf`**: Used to "unzip" compressed software packages.
-   **`source`**: Executes a file immediately to apply changes (like new environment variables) without restarting the session.