# Linux Basics

## 1. Understanding the Terminal Prompt

Example:

```bash
amr@DESKTOP-LJQ3TE5:/$
```

This prompt contains useful information:

* `amr` → current **username**
* `DESKTOP-LJQ3TE5` → **hostname** (machine name)
* `:` → separator
* `/` → current working directory
* `$` → indicates a **regular user**

> If the prompt ends with `#` instead of `$`, you are usually working as the `root` user.

---

# 2. Basic Commands

## 2.1 `ls` — List Directory Contents

The `ls` command is used to **list the contents of a directory**.

```bash
ls
```

### Common Options

#### `-a` — All

Shows all files and directories, including hidden files.

```bash
ls -a
```

Hidden files/directories usually start with `.`:

```text
.bashrc
.profile
.config
```

#### `-l` — Long Format

Displays detailed information about files and directories.

```bash
ls -l
```

The output includes information such as:

* Permissions
* Number of links
* Owner
* Group
* File size
* Modification time
* File/directory name

#### `-F` — Classify

Adds a character to indicate the type of entry.

```bash
ls -F
```

For example:

```text
Documents/
script.sh*
```

`/` indicates a directory.

> **Note:** Linux options are case-sensitive. `-f` and `-F` are different options.

### Useful Combination

```bash
ls -la
```

Shows hidden files in long format.

---

## 2.2 `clear` — Clear the Terminal

Clears the terminal screen.

```bash
clear
```

---

## 2.3 Command Help

Most Linux commands provide a `--help` option that displays basic usage information and available options.

```bash
ls --help
```

> **Important:** Not every command supports `--help` in exactly the same way, but it is a very common convention.

---

## 2.4 Linux Commands Are Case-Sensitive

Linux is generally **case-sensitive**.

For example:

```bash
ls
```

is different from:

```bash
LS
```

Similarly:

```text
file.txt
File.txt
FILE.txt
```

can represent three different filenames.

---

# 3. `cd` — Change Directory

The `cd` command is used to change the current working directory.

```bash
cd directory_name
```

Example:

```bash
cd Documents
```

### Special Directory Symbols

`.` → current directory

```bash
cd .
```

`..` → parent directory

```bash
cd ..
```

Example:

If you are currently in:

```text
/home/amr/projects
```

then:

```bash
cd ..
```

moves you to:

```text
/home/amr
```

### Home Directory

`~` represents the current user's home directory.

```bash
cd ~
```

For example:

```text
/home/amr
```

> `~` is preferable to hard-coding a username because it automatically refers to the current user's home directory.

---

# 4. `pwd` — Print Working Directory

Displays the **full path of the current working directory**.

```bash
pwd
```

Example:

```text
/home/amr/projects
```

---

# 5. `type -a` — Find How a Command Is Resolved

The command:

```bash
type -a command
```

shows how the shell resolves a command and can display aliases, shell functions, built-ins, and executable locations.

Example:

```bash
type -a ls
```

Possible output:

```text
ls is aliased to `ls --color=auto'
ls is /usr/bin/ls
ls is /bin/ls
```

This tells us that:

* `ls` is configured as an alias.
* The shell can also find executable versions of `ls`.

> **Important:** The exact output depends on your shell and system configuration.

---

# 6. `man` — Manual Pages

`man` displays the **manual page** for a command.

```bash
man command
```

Example:

```bash
man ls
```

The manual page contains detailed information about:

* Command usage
* Options
* Arguments
* Behavior
* Examples

### Exiting `man`

Press:

```text
q
```

to quit the manual page.

### Useful Navigation

Inside a `man` page:

```text
↑ / ↓      Move
Space      Next page
b          Previous page
q          Quit
```

---

# 7. `whoami` — Current User

Displays the username of the currently logged-in user.

```bash
whoami
```

Example:

```text
amr
```

---

# 8. `hostname` — Machine Name

Displays the hostname of the machine.

```bash
hostname
```

Example:

```text
DESKTOP-LJQ3TE5
```

---

# 9. `echo` — Print Text or Values

`echo` prints text or the value of a variable to the terminal.

```bash
echo "Hello Linux"
```

It is also commonly used with environment variables:

```bash
echo $HOME
```

Example output:

```text
/home/amr
```

`echo` becomes particularly useful when learning **Bash scripting and environment variables**.

---

# 10. `history` — Command History

Displays previously executed commands.

```bash
history
```

You can also execute a previous command using its history number:

```bash
!123
```

where `123` is the command number.

---

# 11. `ip` — Network Configuration and Information

The `ip` command is used to inspect and manage network configuration.

For example:

```bash
ip addr
```

or:

```bash
ip a
```

shows network interfaces and IP addresses.

You can also inspect routes:

```bash
ip route
```

> This command will become very important later when studying **Linux networking and Docker networking**.

---

# 12. `mkdir` — Make Directory

Creates a new directory.

```bash
mkdir directory_name
```

Example:

```bash
mkdir projects
```

### Create Nested Directories

```bash
mkdir -p projects/backend/api
```

The `-p` option creates parent directories if they do not already exist.

---

# 13. `touch` — Create Files / Update Timestamps

`touch` is commonly used to create an empty file.

```bash
touch file.txt
```

If the file does not exist, it is created.

If the file already exists, `touch` updates its timestamps by default.

Example:

```bash
touch notes.txt
```

> **Important:** `touch` does not exist only to create files. Its primary purpose is to update file timestamps, although creating empty files is one of its most common uses.

---

# 14. `rm` — Remove Files and Directories

### Remove a file

```bash
rm file.txt
```

### Remove an empty directory

```bash
rmdir directory_name
```

### Remove a directory and its contents

```bash
rm -r directory_name
```

`-r` means **recursive**.

### ⚠️ Important

Be careful with:

```bash
rm -r
```

and especially:

```bash
rm -rf
```

These commands can permanently delete files and directories without moving them to a recycle bin.

---

# 15. `cat` — Display and Concatenate Files

`cat` can display the contents of a text file:

```bash
cat file.txt
```

It can also concatenate multiple files.

Example:

```bash
cat file1.txt file2.txt
```

The name `cat` comes from **concatenate**.

---

# 16. `root` — The Superuser

`root` is the Linux **superuser**.

The root user has extensive privileges and can perform operations that regular users normally cannot.

For example, some system files and directories cannot be modified by a normal user.

A regular user can execute a command with elevated privileges using:

```bash
sudo command
```

Example:

```bash
sudo mkdir /example
```

The system may ask for the user's password.

### Important Distinction

`sudo` does **not** mean "become root permanently."

It normally means:

> Execute this particular command with elevated privileges.

You can check the current user with:

```bash
whoami
```

---

# 17. Important Linux Directories

Linux has a standard filesystem hierarchy.

Some important directories include:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── proc
├── root
├── tmp
├── usr
└── var
```

### `/`

The root of the entire Linux filesystem.

It is **not the same thing as the `root` user**.

### `/home`

Contains the home directories of regular users.

Example:

```text
/home/amr
```

### `/root`

The home directory of the `root` user.

### `/dev`

Contains device files representing hardware and virtual devices.

For example:

```text
/dev/null
/dev/sda
```

> **Important correction:** `/dev` is not simply a directory that you "cannot read." Some entries inside `/dev` can be accessed by normal users, while others require appropriate permissions.

### `/etc`

Contains system-wide configuration files.

### `/tmp`

Used for temporary files.

### `/var`

Contains variable data such as logs, caches, and other files that change during system operation.

---

# Key Concepts to Remember

```text
ls       → list directory contents
pwd      → show current path
cd       → change directory
mkdir    → create directory
touch    → create file / update timestamp
rm       → remove files/directories
rmdir    → remove empty directory
cat      → display/concatenate files
clear    → clear terminal
echo     → print text/variables
history  → show command history
whoami   → show current user
hostname → show machine name
ip       → inspect/manage networking
man      → read command manual
type -a  → see how a command is resolved
sudo     → execute command with elevated privileges
file     → Know Type of file
```

## Essential Linux Symbols

```text
.     → current directory
..    → parent directory
~     → current user's home directory
/     → filesystem root
$     → usually indicates a regular user prompt
#     → usually indicates root/superuser prompt
```

> **Mindset:** Don't try to memorize every command. Learn what each command is used for, then practice it in the terminal. Repeated usage will make the commands stick naturally.
