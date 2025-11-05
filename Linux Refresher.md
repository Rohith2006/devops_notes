# 📘 Notes: Introduction to AWS & Linux

---

## ☁️ AWS Fundamentals

### 🌍 AWS Regions – The Global Layer

#### What is a Region?

* A **Region** is a **geographically isolated area** containing multiple **Availability Zones (AZs)**.
* Designed for **data sovereignty**, **fault tolerance**, and **low latency**.
* Each region has independent resources (EC2, RDS, S3, etc.).

**Example:**

| Region Name           | Code        | Location      |
| --------------------- | ----------- | ------------- |
| US East (N. Virginia) | `us-east-1` | North America |

---

### 🏢 Availability Zones (AZs)

* Each Region has **2–6 AZs**, each being a **physically separate datacenter** with **low-latency connectivity**.
* Provides redundancy and high availability.

**Example:**

```
Region: ap-south-1
├── ap-south-1a
├── ap-south-1b
└── ap-south-1c
```

---

### 🌐 VPC (Virtual Private Cloud)

A **VPC** is an **isolated virtual network** inside a region, where you launch AWS resources.

**You can define:**

* IP address ranges (CIDR blocks)
* Subnets
* Route tables
* Internet Gateways
* Security groups / NACLs

💡 *You can have multiple VPCs per region.*

---

### 🧩 Subnets in AWS

**Definition:**
A **Subnet** is a smaller **network segment** inside your VPC. Each subnet belongs to **one AZ only**.

**Use:**

* Divide VPC into logical groups (e.g., *public*, *private*).
* Control routing and access via route tables and security groups.

**✅ Best Practices**

1. Use **multiple AZs** for redundancy.
2. Separate subnets into **tiers** (Public / Private / Isolated).
3. Use **NAT Gateways** for private internet access.
4. Reserve **sufficient CIDR space** for scaling.
5. **Tag subnets** clearly (e.g., `Env=Prod`, `Tier=Public`).
6. Use **Route Tables** and **Security Groups** for traffic isolation.

---

## ⚙️ AWS EC2 Instance Creation Fields

### 1. 🏷️ Name & Tags

* Add a **Name tag** (Key=`Name`, Value=`MyInstance`) for easy identification.
* Add more tags (e.g., `Environment=Production`, `Role=WebServer`) for **organization**, **cost tracking**, and **automation**.

---

### 2. 💾 Application & OS Image (AMI)

* **AMI** = Pre-configured OS image (e.g., *Amazon Linux, Ubuntu, Windows*).
* Choose **Free Tier eligible** AMIs to avoid cost.
* Options: Quick Start, Marketplace, or Community AMIs.

---

### 3. 🧮 Instance Type

* Defines hardware (vCPU, RAM, network performance).
* Example: `t3.micro`, `m5.large`.
* Choose based on workload (CPU-heavy, memory-heavy, or general purpose).
* **Free Tier** supports small instances (`t2.micro`, `t3.micro`).

---

### 4. 🔑 Key Pair (Login)

* Used for **SSH (Linux)** or **RDP (Windows)** access.
* Always create or select a **key pair** before launching (avoid proceeding without one in production).

---

### 5. 🌐 Network Settings

* Choose **VPC** and **Subnet** for your instance.
* **Auto-assign Public IP:** Makes instance internet-accessible.
* **Security Groups:** Act as virtual firewalls controlling inbound/outbound traffic.
* Advanced: Add **multiple network interfaces** if needed.

---

### 6. 💽 Configure Storage

* **Root volume** comes from the AMI; you can attach extra **EBS volumes**.
* **Volume types:** `gp3`, `gp2`, `io1`, `st1`, etc. (vary by performance/cost).
* Configure:

  * Size (GiB)
  * IOPS / Throughput (for performance)
  * Delete-on-termination
  * Encryption (KMS)
  * Device name (e.g., `/dev/xvda`)

---

### 7. ⚙️ Advanced Details

* **IAM Role:** Assign permissions (e.g., access to S3).
* **Shutdown Behavior:** Stop or terminate when shutdown from OS.
* **Termination Protection:** Prevent accidental deletion.
* **Monitoring:** Enable detailed **CloudWatch monitoring** (1-min interval).
* **Placement Group:** Choose clustering strategy (for HPC or fault isolation).
* **Tenancy:** Shared or Dedicated hardware.
* **User Data:** Add startup scripts (install packages, bootstrap tasks).
* **Network Optimization:** Enable enhanced networking (ENA, SR-IOV).
* **Metadata Options:** Configure IMDS version for security.
* **Licensing:** Choose applicable license for OS/software.
* **Tag Specifications:** Apply tags to instances, volumes, and interfaces.

---

## 🐧 Linux Fundamentals

### 🧠 What is Linux?

Linux is an **open-source operating system** widely used in **servers, cloud systems, DevOps, and automation**.

---

### 🧩 Linux Architecture Overview

#### 🔹 Kernel

* Manages **CPU, memory, devices, filesystems**.
* Provides **system calls** for safe hardware interaction.
* Controls **process scheduling, networking, and security**.
  🧠 *Think of it as the “brain” of Linux.*

#### 🔹 Shell

* The **command-line interpreter** to interact with the Kernel.
* Common shells: `bash`, `zsh`, `sh`, `fish`.

**Why Learn Shell?**

* Automate tasks faster.
* Essential for DevOps scripting.
* Easier system debugging.

---

## 🧱 1. Basic Linux Commands

| Command   | Purpose                            | Example                                             |
| --------- | ---------------------------------- | --------------------------------------------------- |
| `pwd`     | Show current directory             | `pwd → /home/user`                                  |
| `cd`      | Change directory                   | `cd /tmp/`, `cd ..`, `cd ~`                         |
| `mkdir`   | Create directories                 | `mkdir projects`, `mkdir -p /home/user/code/python` |
| `rmdir`   | Remove empty directories           | `rmdir old_folder`                                  |
| `touch`   | Create empty file/update timestamp | `touch file1.txt`                                   |
| `cat`     | View/concatenate file content      | `cat file1.txt`                                     |
| `echo`    | Print or write text                | `echo "Linux" > file.txt`                           |
| `history` | View previous commands             | `history`                                           |

🧪 **Lab Exercise 1**

* Create `starting_point` directory.
* Inside it, create `secret_lair.txt`.
* Display path (`pwd`) and command history.
  ✅ *Goal: Understand directory navigation and file creation.*

---

## 📂 2. File Management Commands

| Command    | Purpose                  | Example                         |
| ---------- | ------------------------ | ------------------------------- |
| `ls`       | List directory contents  | `ls -l`, `ls -a`, `ls -lh`      |
| `cp`       | Copy files/directories   | `cp file1.txt /backup/`         |
| `mv`       | Move/rename files        | `mv old.txt new.txt`            |
| `rm`       | Delete files/directories | `rm file1.txt`, `rm -r folder/` |
| `find`     | Search for files         | `find /home/user -name "*.log"` |
| `du`, `df` | Check disk usage         | `du -sh /var/log/`, `df -h`     |

⚠️ Use `rm` carefully — **no recycle bin**!

🧪 **Lab Exercise 2**

* Delete directory `delete_directory`.
* Locate all `.txt` files.
* Check disk space under `/var/log/`.

---

## 🧩 3. Process Management Commands

| Command | Purpose                     | Example                     |             |
| ------- | --------------------------- | --------------------------- | ----------- |
| `ps`    | Show running processes      | `ps -ef`, `ps aux           | grep nginx` |
| `top`   | Real-time process monitor   | `top`                       |             |
| `htop`  | Interactive process monitor | `apt install htop -y`       |             |
| `kill`  | Terminate processes         | `kill 1234`, `kill -9 1234` |             |
| `jobs`  | List background jobs        | `jobs`                      |             |
| `bg`    | Move job to background      | `bg %1`                     |             |
| `fg`    | Bring job to foreground     | `fg %1`                     |             |

🧪 **Lab Exercise 3**

* List all processes (`ps -ef`).
* Identify PID of `sshd`.
* Run `sleep 60 &`, bring it to foreground (`fg`), and kill it.

✅ *Goal: Learn process handling safely.*

---

## 🔒 Extended Practice — Permissions & Ownership

| Command | Purpose                | Example                    |
| ------- | ---------------------- | -------------------------- |
| `chmod` | Change permissions     | `chmod 755 file.sh`        |
| `chown` | Change ownership       | `chown user:group file.sh` |
| `chgrp` | Change group ownership | `chgrp devs project/`      |

🧪 **Lab Exercise 4**

* Work inside hidden directory `secret_lair`.
* Count subdirectories, identify restricted ones, change ownership, and log results to `/home/user/answer.txt`.

---

## 🏁 Summary

* **AWS** → Regions, AZs, VPCs, Subnets, and EC2 setup.
* **Linux** → Core commands for navigation, file, and process management.
* Both form the **foundation of DevOps & Cloud Engineering**.

💬 **Mastering AWS + Linux = Essential skillset for DevOps success.**
