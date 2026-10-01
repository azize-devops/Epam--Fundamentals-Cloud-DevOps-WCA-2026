<div align="center">

# 💻 Linux Shell Basics

**🇬🇧 English** · [🇹🇷 Türkçe](./Linux%20Shell%20Basics.tr.md)

![Questions](https://img.shields.io/badge/questions-11-blue?style=for-the-badge) ![Topic](https://img.shields.io/badge/topic-Linux%20Shell%20Basics-success?style=for-the-badge)

[⬅️ DevOps BootCamp: Linux](./)

</div>

✅ = correct, ❌ = wrong. Each option has a short explanation.

---

### Question 1

**This file is invoked as interactive non-login shell**

| | Option | Why |
|:-:|---|---|
| ❌ | `.bash_profile` | Read by login shells, not by interactive non-login shells. |
| ✅ | `.bashrc` | Read when you open a terminal window or start a new bash inside bash. That is exactly an interactive non-login shell. |
| ❌ | `.bash_history` | Only stores command history; it isn't executed. |
| ❌ | `.bash_logout` | Runs when the session closes. |

> [!TIP]
> **Correct answer:** `.bashrc`

---

### Question 2

**Select answers where environments are defined properly**

| | Option | Why |
|:-:|---|---|
| ✅ | `KEY=value1` | No spaces around `=`, correct definition. |
| ❌ | `KEY2 = value2` | Because of the spaces the shell treats `KEY2` as a command and fails. |
| ❌ | `KEY 3="value3"` | A variable name can't contain a space. |
| ✅ | `KEY_4=value4:value4_1` | Underscore and `:` in the value are fine, valid. |
| ❌ | `KEY5=value 5` | The `5` after the space is run as a separate command. A value with spaces needs quotes. |
| ✅ | `_KEY6="value 6"` | A name may start with an underscore, and the value with a space is quoted: correct. |

> [!TIP]
> **Correct answer:** `KEY=value1`, `KEY_4=value4:value4_1`, `_KEY6="value 6"`

---

### Question 3

**This env var contains a list of directories to be searched when user executing commands**

| | Option | Why |
|:-:|---|---|
| ❌ | `INSTALL` | No such standard variable. |
| ❌ | `DIRS` | Not standard either. |
| ✅ | `PATH` | When you type a command, the shell looks through the directories in this list in order (`/usr/bin:/bin:...`). |
| ❌ | `SHELLPATH` | Doesn't exist. |
| ❌ | `SYSTEM` | Not a standard variable. |

> [!TIP]
> **Correct answer:** `PATH`

---

### Question 4

**This command is used to change current directory to user home dir**

| | Option | Why |
|:-:|---|---|
| ❌ | `cd /` | Goes to the root directory. |
| ❌ | `cd ..` | Goes up one directory. |
| ✅ | `cd ~` | `~` means the home directory. (Plain `cd` does the same.) |
| ❌ | `cd -` | Returns to the previous directory. |

> [!TIP]
> **Correct answer:** `cd ~`

---

### Question 5

**Command to list files in directory**

| | Option | Why |
|:-:|---|---|
| ❌ | `cat` | Shows file contents. |
| ❌ | `man` | Opens a command's manual page. |
| ✅ | `ls` | Lists the files and folders in a directory. |
| ❌ | `pwd` | Prints the current directory. |

> [!TIP]
> **Correct answer:** `ls`

---

### Question 6

**Using this command with -f key you could track live events in a log file**

| | Option | Why |
|:-:|---|---|
| ❌ | `head` | Shows the start of a file. |
| ❌ | `less` | A pager; `-f` doesn't follow live. |
| ❌ | `more` | A pager, no live following. |
| ✅ | `tail` | `tail -f` shows the end of a file and prints new lines as they arrive. The most common way to watch logs. |

> [!TIP]
> **Correct answer:** `tail`

---

### Question 7

**What command is used to exit vi/vim without saving changes?**

| | Option | Why |
|:-:|---|---|
| ❌ | `:wq` | Saves and quits. |
| ✅ | `:q!` | Force quit without saving; changes are lost. |
| ❌ | `:q` | Refuses to quit if there are changes, warning about unsaved changes. |
| ❌ | `:!wq` | Not a valid vim command (a leading `!` runs an external shell command). |

> [!TIP]
> **Correct answer:** `:q!`

---

### Question 8

**Which commands allow you to find files, that contain a word "ping" in the current directory?**

| | Option | Why |
|:-:|---|---|
| ❌ | `find . -n ping -type f` | `-n` is not a valid `find` option. Also `find` looks at file names, not contents. |
| ✅ | `grep -r ping .` | Searches for "ping" inside all files in the current directory. |
| ✅ | `grep -r ping` | With GNU grep and no path, `-r` searches the current directory, so it does the same. |
| ❌ | `find -name ping -t f` | `-t` isn't valid (it's `-type`), and `-name` matches file names, not contents. |

> [!TIP]
> **Correct answer:** `grep -r ping .`, `grep -r ping`

---

### Question 9

**Which command you need to execute in order to safely eding /etc/sudoers file?**

| | Option | Why |
|:-:|---|---|
| ❌ | `vi` | Opening it directly means a bad entry could lock you out of sudo. |
| ❌ | `nano` | Same problem, no syntax check. |
| ❌ | `vim` | Likewise no check. |
| ✅ | `visudo` | Locks the file and checks the syntax on save. It warns about errors so you don't break the system. |

> [!TIP]
> **Correct answer:** `visudo`

---

### Question 10

**What is true about xargs utility?**

| | Option | Why |
|:-:|---|---|
| ✅ | Can convert lines into single line | Turns input lines into the arguments of a single command. |
| ✅ | Can convert single line into multiple lines | With options like `-n 1` it splits a line and runs the command with one argument at a time. |
| ❌ | Can substitute an argument in a command only once | With `-I {}` you can place the same argument in several spots of the command. |
| ❌ | Can run only one command at a time | With `sh -c '...'` it can run several commands, and `-P` runs in parallel. |

> [!TIP]
> **Correct answer:** The first two options

---

### Question 11

**Select commands that will create an archive**

| | Option | Why |
|:-:|---|---|
| ✅ | `tar -cvf file.tar path/to/` | `-c` means create; it builds an archive. |
| ❌ | `tar -xvf file.tar` | `-x` is extract, it unpacks an archive. |
| ❌ | `tar -tf file.tar` | `-t` is list, it lists the contents. |
| ✅ | `tar -cvzf file.tar.gz path/to/` | `-c` creates the archive, `-z` compresses it with gzip. |

> [!TIP]
> **Correct answer:** `tar -cvf file.tar path/to/`, `tar -cvzf file.tar.gz path/to/`

---

## 🗺️ Cheat sheet

**Bash startup files**

| File | When it is read |
|---|---|
| `.bash_profile` | login shell |
| `.bashrc` | interactive non-login shell (new terminal window) |
| `.bash_logout` | when the session closes |
| `.bash_history` | not executed, only stores history |

**Variables:** `KEY=value` (no spaces around `=`), quote values with spaces: `KEY="a b"`.

**`tar` flags**

| Flag | Meaning |
|:-:|---|
| `c` | create |
| `x` | extract |
| `t` | list |
| `z` | gzip |
| `v` | verbose |
| `f` | file name follows |

**Handy commands**

| Need | Command |
|---|---|
| Follow a log live | `tail -f file` |
| Quit vim without saving | `:q!` |
| Edit sudoers safely | `visudo` |
| Search inside files | `grep -r word .` |
| Go home | `cd ~` or `cd` |
