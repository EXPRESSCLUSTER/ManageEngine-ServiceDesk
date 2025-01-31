# ManageEngine-ServiceDesk
Quick Start Guide for setting up high availability (HA) for ManageEngine ServiceDesk Plus MSP using NEC EXPRESSCLUSTER X on Windows. This guide covers installation, cluster configuration, failover setup, and monitoring to ensure minimal downtime and seamless failover. 🚀

# ManageEngine ServiceDesk HA with EXPRESSCLUSTER – Quick Start Guide

## Overview  
This repository provides a step-by-step **Quick Start Guide (QSG)** for configuring **high availability (HA)** for **ManageEngine ServiceDesk Plus MSP** using **NEC EXPRESSCLUSTER X** on Windows servers.  

By following this guide, you will be able to:  
✅ Install and configure ManageEngine ServiceDesk Plus MSP  
✅ Set up an EXPRESSCLUSTER-based high-availability cluster  
✅ Enable automatic failover and data replication  
✅ Monitor and manage the cluster with EXPRESSCLUSTER tools  

## Prerequisites  
Ensure that your system meets the following requirements before proceeding:  

### Minimum Requirements for ManageEngine  
Refer to the official documentation: [ManageEngine System Requirements](https://help.servicedeskplus.com/installing-servicedesk-plus#)  

### System Requirements for EXPRESSCLUSTER  
Refer to the official documentation: [EXPRESSCLUSTER System Requirements](https://www.nec.com/en/global/prod/expresscluster/en/sysreq/os_win.html)  

## Installation and Configuration Steps  

### 1️⃣ Install ManageEngine ServiceDesk  
Follow the official installation guide: [ManageEngine Installation](https://help.servicedeskplus.com/installing-servicedesk-plus#)  

### 2️⃣ Install NEC EXPRESSCLUSTER  
Follow the official installation guide: [EXPRESSCLUSTER Installation](https://docs.nec.co.jp/software/clustering/expresscluster_x/x52/ecx_x52_windows_en/W52_IG_EN/W_IG.html#installing-expresscluster)  

### 3️⃣ Configure the Cluster System  
Set up a **2-node cluster** with **Mirror Disk** and **Failover IP (FIP)** by following:  
[Basic Cluster Setup](https://github.com/EXPRESSCLUSTER/BasicCluster/blob/master/X41/Win/2nodesMirror.md)  

### 4️⃣ Configure ManageEngine for High Availability  
- Move the database to a **mirror disk**  
- Prepare the **secondary server** for failover  
- Add **application and monitoring resources**  

### 5️⃣ Configure Monitoring  
- **Process Monitor:** Ensures ManageEngine is running  
- **PostgreSQL Monitor:** Checks database availability  
- **Failover Testing:** Simulates failover scenarios  

## Architecture Diagram  

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
📖 Documentation
For a detailed step-by-step guide, refer to the Quick Start Guide in this repository.

🛠 Support
For any issues or troubleshooting, refer to:
🔹 EXPRESSCLUSTER Knowledge Base
🔹 ManageEngine Support
