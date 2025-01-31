### Quick Start Guide: High Availability for ManageEngine ServiceDesk using EXPRESSCLUSTER

> **Table of Contents**
> 
> 1. [Introduction](#introduction)
> 2. [Prerequisites](#prerequisites)
>    1. [Minimum Requirements for ManageEngine](#minimum-requirements-for-manageengine)
>    2. [System Requirements for EXPRESSCLUSTER on Windows](#system-requirements-for-expresscluster-on-windows)
> 3. [Installation Steps](#installation-steps)
>    1. [Install ManageEngine ServiceDesk](#install-manageengine-servicedesk)
>    2. [Install EXPRESSCLUSTER](#install-expresscluster)
> 4. [Cluster System Configuration](#cluster-system-configuration)
> 5. [Cluster Configuration](#cluster-configuration)
>    1. [Basic Cluster Setup](#basic-cluster-setup)
> 6. [Configuring ManageEngine for High Availability](#configuring-manageengine-for-high-availability)
>    1. [Initial Setup on Primary Server](#initial-setup-on-primary-server)
>    2. [Prepare Secondary Server for Failover](#prepare-secondary-server-for-failover)
> 7. [Failover Testing](#failover-testing)
> 8. [Add Application Resource to EXPRESSCLUSTER](#add-application-resource-to-expresscluster)
> 9. [Adding Process Name Monitor](#adding-process-name-monitor)
> 10. [Adding PostgreSQL Monitor](#adding-postgresql-monitor)
> 11. [Failover testing](#failover-testing)


## Introduction
This guide provides step-by-step instructions to set up a high-availability (HA) cluster for ManageEngine ServiceDesk Plus MSP using NEC EXPRESSCLUSTER on Windows.
By following this, you can configure automatic failover, reduce downtime, and ensure seamless operation for ManageEngine ServiceDesk Plus MSP.

## Prerequisites
Before beginning, ensure that the system meets the following requirements:

### Minimum Requirements for ManageEngine
Refer to the official documentation: [ManageEngine System Requirements](https://help.servicedeskplus.com/installing-servicedesk-plus#)

### System Requirements for EXPRESSCLUSTER on Windows
Refer to the official documentation: [EXPRESSCLUSTER System Requirements](https://www.nec.com/en/global/prod/expresscluster/en/sysreq/os_win.html)

## Installation Steps
### Install ManageEngine ServiceDesk
Follow the official installation guide: [ManageEngine Installation](https://help.servicedeskplus.com/installing-servicedesk-plus#)

### Install EXPRESSCLUSTER
Follow the official installation guide: [EXPRESSCLUSTER Installation](https://docs.nec.co.jp/software/clustering/expresscluster_x/x52/ecx_x52_windows_en/W52_IG_EN/W_IG.html#installing-expresscluster)

### Cluster System Configuration
```bat
<Public LAN>
 |
 | <Private LAN>
 |  |
 |  |  +--------------------------------+
 +-----| Primary Server                 |
 |  |  |  OS: Windows Server 2022       |
 |  +--|  EXPRESSCLUSTER X 5.x          |
 |  +--|  ManageEngine                  |
 |  |  +--------------------------------+
 |  |
 |  |  +--------------------------------+
 +-----| Secondary Server               |
 |  |  |  OS: Windows Server 2022       |
 |  +--|  EXPRESSCLUSTER X 5.x          |
 |  +--|  ManageEngine                  |
 |  |  +--------------------------------+
 |  |
 |  |  +--------------------------------+
 |  +--| Client machine                 |
 |     +--------------------------------+
 |
[Gateway]
 :
```

## Cluster Configuration
Set up a basic cluster with Mirror Disk and FIP resources by following this guide: [Basic Cluster Setup](https://github.com/EXPRESSCLUSTER/BasicCluster/blob/master/X41/Win/2nodesMirror.md)

### Configuring ManageEngine for High Availability
1. **Initial Setup on Primary Server**
   - Start the ManageEngine service.
   - Access the application at `https://localhost:8080`.
   - Change the administrator password.
   - Stop the service from Windows Services and set it to manual mode.

2. **Move the Database to Mirror Disk (E:/ Drive)**
   - Copy `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\pgsql` to `E:\pgsql`.
   - Modify security properties of Drive E:\ to allow full control for Everyone.
   - Edit `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\bin\setCommonEnv.bat`:
     - Change `set DB_HOME=%SERVER_HOME%\pgsql` to `set DB_HOME="E:\pgsql"`.
   - Save and close the file.
   - Start the ManageEngine service by running `run.bat` from `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\bin`.

3. **Prepare Secondary Server for Failover**
   - Copy `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\conf` from the primary to the secondary server.
   - Backup the `conf` directory before replacement.
   - Ensure the database path modification (`set DB_HOME="E:\pgsql"`) is applied on the secondary server.

4. **Failover Testing**
   - Use Cluster Manager to failover the group to the secondary server.
   - Start ManageEngine on the secondary server using `run.bat`.
   - Stop the service using `shutdown.bat`.

5. **Add Application Resource to EXPRESSCLUSTER**
   - Open Cluster Manager and switch to configuration mode.
   - Add a Failover Resource:
     - Name: `ME-services` (or any preferred name)
     - Recovery Operation: `Stop the cluster service and shutdown the OS`
     - Resident Type: `Resident`
     - Start Path: `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\bin\run.bat`
     - Stop Path: `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\bin\shutdown.bat`
     - Set the Start and Stop directories to `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\bin`.
   - Apply the configuration and return to operation mode.
   - Start the `ME-services` application resource.

6. **Adding Process Name Monitor**
   - Open Cluster Manager and switch to configuration mode.
   - Add a new monitor resource: **Process Name Monitor**.
   - In the **Monitor Common** tab:
     - Set **Monitoring Timing** to `Active`.
     - Set **Target Resource** to `ME-services` (as defined earlier).
   - In the **Monitor Special** tab:
     - Set **Process Name** to `C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\bin\\..\jre\bin\java`.
   - In the **Recovery Action** tab:
     - Set **Recovery Action** to `Custom Setting`.
     - Set **Recovery Target** to `ME-services`.
     - Set **Final Action** to `No Operation`.
   - Apply the settings and finish.

7. **Adding PostgreSQL Monitor**
   - Open Cluster Manager and switch to configuration mode.
   - Add a new monitor resource: **PostgreSQL Monitor**.
   - In the **Monitor Common** tab:
     - Set **Target Resource** to `ME-services` (as defined earlier).
   - In the **Monitor Special** tab:
     - Set **Monitor Level** to `Level 2 monitoring by update/select`.
     - Set **Database Name** to `servicedesk`.
     - Set **IP Address** to `127.0.0.1`.
     - Set **Port Number** to `65432`.
     - Set **User Name** to `postgres`.
     - Enter the **Password** (your database password).
     - Set **Monitor Table Name** to `pg_ecx_mon`.
   - In the **Recovery Action** tab:
     - Set **Recovery Action** to `Executing Failover`.
     - Set **Recovery Target** to `ME-services`.
   - Apply the settings and finish.

8. **Failover testing**
   - Open Manage engine webconsole using FIP address
   - Create a new ticket on active server(where the failover is running).
   - Failover the failover group to standby server:
     - Check dashboard the ticket must be visible here!!.
