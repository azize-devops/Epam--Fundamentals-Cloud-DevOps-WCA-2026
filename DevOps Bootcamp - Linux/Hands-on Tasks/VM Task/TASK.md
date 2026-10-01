<div align="center">

# 📋 VM Task — Task Description

**🇬🇧 English** · [🇹🇷 Türkçe](./TASK.tr.md)

[⬅️ Back to README](./README.md) · [🛠️ Solution](./SOLUTION.md)

</div>

This file describes **what is asked**. For how it was solved, see [SOLUTION.md](./SOLUTION.md).

> [!NOTE]
> The task text is summarized from the course material, not copied verbatim.

## Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Verification (checker)](#verification-checker)
- [Tasks](#tasks)
- [Key concepts](#key-concepts)

## Overview

Prepare an **Ubuntu 20.04 Server** virtual machine locally and complete 12 configuration tasks on it. They cover user management, storage (LVM, swap), software installation (Docker, NGINX), automation (cron, logrotate), file attributes and networking (static route).

| 🧮 Tasks | 🧪 Tests | 🔁 Attempts per task |
|:-:|:-:|:-:|
| **12** | **27** | **3** |

## Prerequisites

**VM requirements**

- 🐧 OS: **Ubuntu 20.04 Server**
- 👤 Default user: `ubuntu` (`vagrant` on the Vagrant image)
- 🚫 **No swap partition**
- 💽 **2 disks**: the system disk and a second disk (e.g. 1 GB) for the LVM task
- 🔧 `jq` installed

**Two ways to prepare the VM**

1. **Vagrant** *(used here)* — the `Vagrantfile` creates the VM and the second disk. The `disks` feature is experimental, so `VAGRANT_EXPERIMENTAL="disks"` is required.
2. **Manual** — create the VM in Oracle VirtualBox and attach the second disk yourself.

> [!NOTE]
> The Static Route task also needs **`yq`** installed (the checker uses it to read YAML).

## Verification (checker)

The checker is an executable you download for your platform (AMD64 or ARM64). Run it inside the VM:

```bash
./checker -course 'linux' -course-version 'v1.0' -test-suite 'vm_task'
```

Every passing test prints a **secret phrase**. Enter these on the course page.

> [!WARNING]
> Download the checker that matches the **VM's** CPU architecture. For a Linux x86_64 VM use **AMD64**, even if your computer is an ARM Mac. See [Problems & fixes](./SOLUTION.md#problems--fixes) in SOLUTION.md.

## Tasks

### 1. Change of VM hostname

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 2 | 1–2 | hostname, `/etc/hosts` |

Set the VM hostname to `student`. It must resolve to `127.0.0.1` and the change must be **persistent** (survive a reboot).

- `hostname -s`, `-f` and `-i` must show `student` and `127.0.0.1`.
- A `sudo: unable to resolve host` warning means the task is not finished.

### 2. User Creation

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 6 | 3–8 | users, groups |

Create a new user:

| Property | Value |
|---|---|
| Username | `student` |
| UID | `1040` |
| Primary group | `student_group` (GID `1050`) |
| Home directory | `/home/student_home` |
| Supplementary group | `cdrom` |

### 3. LVM Configuration

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 4 | 9–12 | LVM, ext4, fstab |

Configure LVM on the second disk:

- Create a **physical volume** on an empty device.
- **Volume group** named `student`.
- **Logical volume** named `student`, **50%** of the free space, filesystem **ext4**.
- Mount it on `/lvm/student` **persistently, using the UUID**.

### 4. Sudo Rights

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 1 | 13 | sudoers |

Give `student` these sudo rights:

- `sudo apt install` and `sudo apt-get install` work **without a password**.
- Every other sudo command **asks for a password**.
- **Do not touch** the default `/etc/sudoers` (editing it fails the test). Find the best-practice alternative.

> [!TIP]
> `sudo -k` clears the cached sudo credentials. Handy while testing.

### 5. Swap Creation

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 2 | 14–15 | swap |

Create a swap file named `swapfile`:

- Size: **1 GB**
- **Persistent**: active again after a reboot.

### 6. Docker Installation

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 1 | 16 | Docker, apt repo |

Install Docker **from the Docker repository**:

- Version: **19.03.15**
- Must run as `student` **without sudo**.

> [!NOTE]
> `permission denied ... docker.sock` means the user cannot use Docker yet.

### 7. NGINX Installation

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 5 | 17–21 | NGINX, JSON |

Install and run NGINX:

- Port: **8080**
- A static HTML page returns **JSON** with these fields (values may be hand-written):

```json
{
  "name": "student",
  "os": "<ubuntu version>",
  "ip": "<vm private ip>",
  "cpu": "<number of cpus>",
  "memory": "<memory in Gi>"
}
```

The test suite reads the page with `jq`, so `jq` must be installed. Tests check that the service listens on 8080 and verify `name`, `os`, `ip` and `cpu`.

### 8. Cron Job

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 1 | 22 | cron |

Create a cron job that backs up `/etc/nginx/nginx.conf`:

- Backup name pattern: `<file_name>-<timestamp>.conf.bak`
- Timestamp: **seconds** since 1970-01-01 00:00:00 UTC
- Save backups in `/etc/nginx/conf.d/`.
- **No extra script**: the command goes straight into the crontab.

### 9. NGINX Logrotate

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 1 | 23 | logrotate |

Rotate the NGINX logs **weekly** with `logrotate` (keep `rotate 14`).

Expected `logrotate -d` output: `rotating pattern: /var/log/nginx/*.log  weekly (14 rotations)`

### 10. Make an immutable file /immutable.txt

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 1 | 24 | `chattr`, permissions |

Create `/immutable.txt`:

- Owner `student`, group `cdrom`
- All permissions for everyone (**rwx**, i.e. 777)
- **Nobody can delete it** (not even root)

### 11. Bash Alias Creating

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 1 | 25 | Bash, alias |

Create a Bash alias `eip` for user `student`:

- It shows the **external** IP address.
- It is **persistent** (works after a reboot).

### 12. Static Route

| ⭐ Points | 🧪 Tests | 🏷️ Topics |
|:-:|:-:|---|
| 2 | 26–27 | netplan, routing |

Create a **static route** so packets for `8.8.8.8` go through `4.4.4.4` (advanced: make it **persistent**).

- With the route active, `8.8.8.8` must be unreachable; other addresses (e.g. `www.google.com`) must keep working.
- Tests: the route is in the routing table and it is persistent.

## Key concepts

The task text asks about a few concepts. Short answers:

<details>
<summary><b>📄 What is <code>/etc/netplan/50-cloud-init.yaml</code>?</b></summary>

<br>

It is Ubuntu's **netplan** network configuration file. `cloud-init` in the name shows that cloud-init generated it, and cloud-init may rewrite it on every boot. To keep manual, persistent changes, cloud-init's network configuration is disabled.

</details>

<details>
<summary><b>🔐 Why not edit <code>/etc/sudoers</code> directly?</b></summary>

<br>

`/etc/sudoers` can be changed by package updates and a bad edit can break sudo. The best practice is a separate file under `/etc/sudoers.d/`, edited with `visudo -f`.

</details>

<details>
<summary><b>🆔 Why mount by UUID?</b></summary>

<br>

Device names like `/dev/sdX` can change between reboots. A UUID does not, so it is the reliable way to mount persistently.

</details>
