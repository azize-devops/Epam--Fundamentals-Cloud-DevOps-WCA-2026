<div align="center">

# 🐚 Linux Shell Quiz

### Questions · Correct Answers · Why Right or Wrong

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)
![Questions](https://img.shields.io/badge/Questions-5-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Answers-Ready-success?style=for-the-badge)

**✅ Correct option &nbsp;·&nbsp; ❌ Wrong option**

</div>

---

## Table of Contents

- [Quick Summary](#quick-summary)
- [Question 1: Known shells](#question-1-known-shells)
- [Question 2: Switching shells](#question-2-switching-shells)
- [Question 3: User-specific startup files](#question-3-user-specific-startup-files)
- [Question 4: When not to use shell](#question-4-when-not-to-use-shell)
- [Question 5: When to use shell](#question-5-when-to-use-shell)
- [Key Takeaway](#key-takeaway)

---

## Quick Summary

| # | 📝 Question | ✅ Correct Answer |
|:-:|-------------|-------------------|
| 1 | File that lists the known shells | `/etc/shells` |
| 2 | Switching shells in the active terminal | Enter the name of the new shell |
| 3 | User-specific startup files | `~/.profile` · `~/.bashrc` |
| 4 | Shell should **not** be used for | Data structures · Complex applications · Mission-critical systems |
| 5 | Shell **can** be used for | Mostly calling other utilities |

---

## Question 1: Known shells

> **❓ Which file gives an overview of known shells on a Linux system?**

| | Option | Why? |
|:-:|--------|------|
| ✅ | **`/etc/shells`** | Lists the valid login shells installed on the system (`/bin/bash`, `/bin/sh`, `/usr/bin/zsh`, ...). The `chsh` command also checks this file. |
| ❌ | `/etc/passwords` | No such file. User accounts live in `/etc/passwd` and password hashes in `/etc/shadow`. |
| ❌ | `/etc/known_shells` | Not a standard Linux file; the name is made up. |
| ❌ | `/etc/shells.sh` | The `.sh` extension is for scripts; the list file is not named this way. |

> [!TIP]
> To see the list:
> ```bash
> cat /etc/shells
> ```

---

## Question 2: Switching shells

> **❓ How do you switch from one shell to another in the active terminal?**

| | Option | Why? |
|:-:|--------|------|
| ✅ | **Enter the name of the new shell** | A shell is just a program. Typing `zsh` or `bash` starts the new shell as a **child process**. Type `exit` to return to the previous one. |
| ❌ | Update the active shell's name in `/etc/shells` | That file only lists allowed shells; it does not change the running shell. |
| ❌ | Update the active shell's name in `~/.bashrc` | It runs when a new bash session starts; it does not switch the current terminal. |
| ❌ | It can't be done because the OS must be restarted | Wrong. No restart is needed to change shells. |

```bash
$ zsh        # switch to zsh
$ exit       # go back to the previous shell
```

> [!NOTE]
> To change your default shell **permanently**, use `chsh -s /bin/zsh`. The chosen shell must be listed in `/etc/shells`.

---

## Question 3: User-specific startup files

> **❓ Select all of the user-specific startup files.**

| | Option | Why? |
|:-:|--------|------|
| ❌ | `/etc/profile` | **System-wide** file, runs for all users. |
| ❌ | `/etc/.profile` | Not a standard file. |
| ✅ | **`~/.profile`** | Lives in the home directory; runs at login for that user only. |
| ✅ | **`~/.bashrc`** | Lives in the home directory; runs in interactive bash sessions for that user only. |

```text
/etc/profile   →  🌍 for everyone (system-wide)
~/.profile     →  👤 for you only
~/.bashrc      →  👤 for you only
```

> [!IMPORTANT]
> **Rule of thumb:** files starting with `~/` (home directory) are user-specific; files under `/etc/` are system-wide.

---

## Question 4: When not to use shell

> **❓ Shell should not be used for (select all correct options).**

| | Option | Why? |
|:-:|--------|------|
| ✅ | **Need data structures, such as linked lists or trees** | Shell has no real support for advanced data structures (only variables and arrays). Python, C or Java fit better. |
| ✅ | **Complex applications where structured programming is a necessity** | No type-checking of variables, no function prototypes, etc.; large projects become error-prone. |
| ❌ | If you're mostly calling other utilities and doing relatively little data manipulation | The opposite: this is where shell is **strongest** (see Question 5). |
| ✅ | **Mission-critical applications upon which you are betting the future of the company** | Scripts are fragile, with limited error handling and performance; critical systems need robust, testable languages. |

> [!WARNING]
> This question has 3 correct answers. The third option is the trap: it is the correct answer to Question 5.

---

## Question 5: When to use shell

> **❓ Shell can be used for (select all correct options).**

| | Option | Why? |
|:-:|--------|------|
| ❌ | Need data structures, such as linked lists or trees | Shell is not suited for this. |
| ❌ | Complex applications where structured programming is a necessity | No type-checking, etc. |
| ✅ | **If you're mostly calling other utilities and doing relatively little data manipulation** | This is shell's real job: gluing commands together (pipes, redirection), automation, backups, file operations, system administration scripts. |
| ❌ | Mission-critical applications upon which you are betting the future of the company | Risky and the wrong choice. |

---

## Key Takeaway

<div align="center">

### Shell = 🔗 the "glue" language

| 👍 Good at | 👎 Bad at |
|------------|-----------|
| Combining commands | Data structures (lists, trees) |
| Automation and backups | Complex, large applications |
| Small system scripts | Mission-critical systems |

</div>

<details>
<summary><b>🔁 Difference between Question 4 and Question 5 (click)</b></summary>

<br>

The two questions are opposites:

- **Question 4:** options 1, 2 and 4 are correct (where shell is weak).
- **Question 5:** only option 3 is correct (where shell is strong).

</details>

---

<div align="center">

⭐ Prepared by **Claude** 🤖 · 06.10.2026

</div>