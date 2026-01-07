# MySQL: 8.0.31 Installation

This document provides a step-by-step procedure for installing MySQL 8.0.31 on a Linux system (e.g., CentOS 7) using RPM packages and an automated configuration script.

## 1. Preparation & Package Upload

Before starting the installation, you must prepare the directory structure and upload the necessary files.

**Create the target directory:**

```Bash
mkdir -p /usr/local/soft/mysql
cd /usr/local/soft/mysql/
```

**Upload the following files to the directory:**

-   **Script:** `install_mysql.sh`
-   **RPM Packages:**
    -   `mysql-community-common-8.0.31-1.el7.x86_64.rpm`
    -   `mysql-community-client-plugins-8.0.31-1.el7.x86_64.rpm`
    -   `mysql-community-libs-8.0.31-1.el7.x86_64.rpm`
    -   `mysql-community-libs-compat-8.0.31-1.el7.x86_64.rpm`
    -   `mysql-community-icu-data-files-8.0.31-1.el7.x86_64.rpm`
    -   `mysql-community-client-8.0.31-1.el7.x86_64.rpm`
    -   `mysql-community-server-8.0.31-1.el7.x86_64.rpm`
-   **Java Connector:** `mysql-connector-j-8.0.31.jar`

------

## 2. Pre-installation Cleanup

To prevent conflicts, you must remove existing MySQL/MariaDB libraries and install necessary dependencies.

1.  **Remove conflicting libraries:**

    ```Bash
    yum remove mysql-libs
    ```
    
2.  **Install dependencies:**

    ```Bash
    yum -y install libaio autoconf
    ```

------

## 3. Automated Installation Script

The `install_mysql.sh` script automates the environment cleanup, package installation, and initial security configuration.

### Script Content Overview

The script performs the following critical tasks:

-   **Validation:** Verifies root privileges and the presence of all 7 RPM packages.
-   **Force Cleanup:** Stops existing services and wipes old data/logs (`/var/lib/mysql`, `/etc/my.cnf`).
-   **Installation:** Installs the RPMs and starts the `mysqld` service.
-   **Password Policy Adjustment:** Modifies `/etc/my.cnf` to allow simpler passwords (Length: 4, Policy: Low).
-   **Root Initialization:**
    -   Extracts the temporary password from logs.
    -   Resets the root password to `000000`.
    -   Enables remote root login (`host='%'`).
    -   Sets authentication to `mysql_native_password`.

### Executing the Installation

Run the script from within the `/usr/local/soft/mysql` directory:

```Bash
sh install_mysql.sh
```

------

## 4. Post-Installation Summary

| **Setting**               | **Value**               |
| ------------------------- | ----------------------- |
| **Default Root Password** | `000000`                |
| **Remote Access**         | Enabled (`%`)           |
| **Auth Plugin**           | `mysql_native_password` |
| **Password Policy**       | Length 4 / Low Policy   |