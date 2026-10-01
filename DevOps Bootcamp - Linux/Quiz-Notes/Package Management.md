<div align="center">

# 📦 Package Management

**🇬🇧 English** · [🇹🇷 Türkçe](./Package%20Management.tr.md)

![Questions](https://img.shields.io/badge/questions-5-blue?style=for-the-badge) ![Topic](https://img.shields.io/badge/topic-Package%20Management-success?style=for-the-badge)

[⬅️ DevOps BootCamp: Linux](./)

</div>

✅ = correct, ❌ = wrong. Each option has a short explanation.

---

### Question 1

**This package managers are used in Debian-like distributives**

| | Option | Why |
|:-:|---|---|
| ✅ | `apt` | The main package manager of Debian, Ubuntu and similar. It resolves dependencies itself. |
| ❌ | `rpm` | The package manager/format of Red Hat-based systems. |
| ✅ | `dpkg` | Debian's low-level package tool; installs `.deb` files. `apt` uses it behind the scenes. |
| ❌ | `yum` | The package manager of Red Hat-based systems (CentOS, older RHEL). |

> [!TIP]
> **Correct answer:** `apt`, `dpkg`

---

### Question 2

**Which package could be installed in RedHat-based distributives?**

| | Option | Why |
|:-:|---|---|
| ❌ | `apk` | Alpine Linux's package format. |
| ❌ | `deb` | Debian/Ubuntu's package format. |
| ✅ | `rpm` | The package format of Red Hat, Fedora, CentOS and the like. |
| ❌ | `pkg` | FreeBSD's package system. |

> [!TIP]
> **Correct answer:** `rpm`

---

### Question 3

**Using this program you could manage several versions of different programming languages**

| | Option | Why |
|:-:|---|---|
| ✅ | `alternatives` | `update-alternatives` lets you switch between several versions of the same command (java, python, gcc and other languages/tools). |
| ❌ | `pyenv` | Manages Python versions only, which doesn't fit "different programming languages". |
| ❌ | `nvenv` | No such tool. (The real one for Node is `nvm`.) |
| ❌ | `versions` | No standard tool with this name. |

> [!TIP]
> **Correct answer:** `alternatives`

---

### Question 4

**Please select what is true about systemd**

| | Option | Why |
|:-:|---|---|
| ❌ | a single package | systemd is not one program but a collection of software components. |
| ✅ | a bundle of sofware with journald, networkd, | It contains many components such as journald (logs), networkd (network) and logind. |
| ✅ | an alternative to init.d | The modern init/service manager that replaced the old SysV init.d. |
| ❌ | less flexible than init.d | Quite the opposite, it is more flexible. |
| ✅ | more flexible than init.d | Offers parallel service start, dependency management, timers and more. |

> [!TIP]
> **Correct answer:** 2nd, 3rd and 5th options

---

### Question 5

**Select tasks, that are applicable to be managed by crond**

| | Option | Why |
|:-:|---|---|
| ✅ | backup | Ideal for running a backup at a set time every night. |
| ✅ | disk cleanup | Used to regularly clear temp files or old logs. |
| ✅ | data sync | A sync (e.g. `rsync`) can run at regular intervals. |
| ✅ | restart | A service or server restart can be scheduled for a given time. |
| ✅ | check if service is running | A script can check every minute/hour that a service is up and start it if needed. |

> [!TIP]
> **Correct answer:** All of them, because cron suits any scheduled command or script.

---

## 🗺️ Cheat sheet

| Family | Package format | Low-level tool | High-level manager |
|---|:-:|:-:|:-:|
| Debian / Ubuntu | `.deb` | `dpkg` | `apt` |
| Red Hat / Fedora / CentOS | `.rpm` | `rpm` | `yum` / `dnf` |
| Alpine | `.apk` | `apk` | `apk` |
| FreeBSD | `pkg` | | `pkg` |

**cron schedule format**

```text
┌───────── minute (0-59)
│ ┌─────── hour (0-23)
│ │ ┌───── day of month (1-31)
│ │ │ ┌─── month (1-12)
│ │ │ │ ┌─ day of week (0-7)
* * * * *  command
```
