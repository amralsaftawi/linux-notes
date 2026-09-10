# Users and Groups Management

Linux is a **multi-user operating system**.

This means that multiple users can exist on the same system, and each user can have:

* A username
* A User ID (`UID`)
* A primary group
* Additional groups
* A home directory
* A login shell
* Permissions and other account settings

Understanding users and groups is essential because Linux security and permissions are heavily based on:

```text
Users + Groups + Permissions
```

---

# 1. Types of Users

Linux users can generally be divided into three categories.

## 1.1 Root User

The **root user** is the system's superuser.

Root has the highest level of privileges and can perform administrative operations such as:

* Create and delete users
* Modify system files
* Install software
* Manage services
* Change system configuration
* Access files that normal users may not be allowed to access

The root user has:

```text
UID = 0
```

Example prompt:

```bash
root@DESKTOP-LJQ3TE5:~#
```

The `#` usually indicates that the current shell is running with root privileges.

---

## 1.2 Normal Users

Normal users are created for people who use the system.

For example:

```text
amr
```

A normal user usually has:

* A home directory
* A login shell such as `/bin/bash`
* Limited permissions compared to root

Example:

```text
Username: amr
UID: 1000
Home: /home/amr
Shell: /bin/bash
```

---

## 1.3 System Users

System users are accounts used by:

* Services
* Applications
* Background processes
* System components

Examples may include accounts such as:

```text
www-data
systemd-network
messagebus
```

These accounts are generally **not intended for interactive human login**.

Many system accounts use a shell such as:

```text
/usr/sbin/nologin
```

which prevents normal interactive login.

---

# 2. User ID — UID

Every user account has a numeric identifier called:

```text
UID
```

For example:

```text
amr → UID 1000
```

The root user always has:

```text
UID 0
```

On many modern Linux distributions:

```text
UID 1–999      → system/service accounts
UID 1000+      → normal users
```

However:

> **UID ranges are distribution and configuration dependent.**

For example, some systems may use different ranges for system users and normal users.

The most important rule is:

```text
UID 0 → root privileges
```

---

# 3. Can Two Users Have the Same UID?

Technically, it is possible to create multiple usernames with the same UID.

For example:

```text
user1 → UID 1000
user2 → UID 1000
```

However, this is generally a bad idea.

Linux permissions are primarily based on the numeric UID, not simply the username.

Conceptually:

```text
Username
    ↓
UID
    ↓
Permissions
```

If two users share the same UID, they can effectively be treated as the same identity for many permission checks.

Therefore, normal user accounts should generally have unique UIDs.

---

# 4. Where Are Users Stored?

A common source of confusion is:

```bash
ls /home
```

This may show:

```text
amr
otheruser
```

But this does **not** show all users.

Why?

Because:

* System users may not have home directories.
* Root's home directory is `/root`, not `/home/root`.
* Some accounts use different home directories.

The main account database is:

```text
/etc/passwd
```

You can view it using:

```bash
cat /etc/passwd
```

---

# 5. Understanding `/etc/passwd`

Each line represents a user account.

Example:

```text
amr:x:1000:1000::/home/amr:/bin/bash
```

The general format is:

```text
username:password_placeholder:UID:GID:comment:home_directory:login_shell
```

Let's break down:

```text
amr:x:1000:1000::/home/amr:/bin/bash
```

| Field          | Value       | Meaning                                  |
| -------------- | ----------- | ---------------------------------------- |
| Username       | `amr`       | Account name                             |
| Password field | `x`         | Actual password hash is stored elsewhere |
| UID            | `1000`      | User ID                                  |
| GID            | `1000`      | Primary Group ID                         |
| Comment        | Empty       | Optional user information                |
| Home           | `/home/amr` | User's home directory                    |
| Shell          | `/bin/bash` | Login shell                              |

---

# 6. Example: System User

Example:

```text
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
```

Breaking it down:

| Field          | Value                  | Meaning                               |
| -------------- | ---------------------- | ------------------------------------- |
| Username       | `list`                 | Account name                          |
| Password field | `x`                    | Password hash stored in `/etc/shadow` |
| UID            | `38`                   | User ID                               |
| GID            | `38`                   | Primary Group ID                      |
| Comment        | `Mailing List Manager` | Description                           |
| Home directory | `/var/list`            | Account's home directory              |
| Login shell    | `/usr/sbin/nologin`    | Interactive login is disabled         |

The final field:

```text
/usr/sbin/nologin
```

means that this account is generally not intended to provide an interactive shell.

---

# 7. `/etc/shadow`

The file:

```text
/etc/shadow
```

contains sensitive account information, including password hashes and password aging information.

Example:

```text
amr:$y$j9T$...:...
```

The value after the username represents password-related information.

> **Important:** Passwords are not stored as plain text. Modern Linux systems store password hashes.

Unlike `/etc/passwd`, `/etc/shadow` is normally readable only by privileged users.

A normal user attempting to read it may receive:

```text
Permission denied
```

---

# 8. `/etc/passwd` vs `/etc/shadow`

```text
/etc/passwd
│
├── User account information
├── UID
├── GID
├── Home directory
└── Login shell

/etc/shadow
│
├── Password hashes
└── Password aging information
```

The separation helps protect sensitive password information.

---

# 9. Switching Users

The `su` command allows you to switch to another user.

For example:

```bash
su root
```

or simply:

```bash
su
```

By default, `su` switches to the root user.

---

# 10. `su` vs `su -`

This distinction is important.

## `su`

```bash
su
```

Switches the user, but generally keeps much of the current environment.

## `su -`

```bash
su -
```

or:

```bash
su - root
```

Switches to the target user and starts a login shell.

Conceptually:

```text
su
↓
Switch user
↓
Keep much of current environment
```

```text
su -
↓
Switch user
↓
Load target user's login environment
↓
Use target user's home directory
```

For administrative work, `su -` is generally preferred over plain `su` when you specifically need a full root login environment.

---

# 11. `sudo`

Instead of switching permanently to root, Linux systems commonly allow authorized users to execute individual commands with elevated privileges.

Example:

```bash
sudo apt update
```

The general form is:

```bash
sudo command
```

Example:

```bash
sudo useradd newuser
```

This means:

> Execute this command with elevated privileges.

Usually, `sudo` executes the command as `root`, depending on the system's configuration.

---

# 12. The `sudoers` Configuration

Who is allowed to use `sudo` is controlled by the **sudoers configuration**.

The main configuration file is commonly:

```text
/etc/sudoers
```

Additional configuration files may exist under:

```text
/etc/sudoers.d/
```

> **Important:** Do not edit `/etc/sudoers` directly with a normal text editor.

Use:

```bash
sudo visudo
```

`visudo` checks the syntax before saving, helping prevent configuration errors that could lock you out of administrative access.

---

# 13. Creating Users — `useradd`

The `useradd` command is used to create users.

Basic syntax:

```bash
sudo useradd username
```

However, depending on the distribution and configuration, this may create only the account without creating a home directory.

To create a home directory:

```bash
sudo useradd -m username
```

Example:

```bash
sudo useradd -m john
```

---

## Common `useradd` Options

### `-c` — Comment

Used to add descriptive information:

```bash
sudo useradd -c "John Doe" john
```

---

### `-m` — Create Home Directory

```bash
sudo useradd -m john
```

Creates:

```text
/home/john
```

---

### `-s` — Specify Login Shell

Example:

```bash
sudo useradd -m -s /bin/bash john
```

This creates the user with Bash as the login shell.

---

### `-p` — Password Hash

You may see:

```bash
useradd -p password username
```

However, this can be misleading.

The `-p` option expects an **encrypted password/hash**, not a normal plaintext password.

Therefore, the recommended approach is:

```bash
sudo useradd -m john
sudo passwd john
```

Then enter the password interactively.

> Avoid putting passwords directly in commands because they can be exposed through shell history or process information.

---

# 14. Changing or Resetting a User Password

Use:

```bash
sudo passwd username
```

Example:

```bash
sudo passwd john
```

The system will prompt you to enter a new password.

A user can usually change their own password with:

```bash
passwd
```

---

# 15. Deleting Users — `userdel`

Basic usage:

```bash
sudo userdel username
```

Example:

```bash
sudo userdel john
```

This may remove the account but leave the user's home directory.

To remove the account and home directory:

```bash
sudo userdel -r john
```

> Be careful with `userdel -r` because it removes the user's associated files.

---

# 16. Modifying Users — `usermod`

`usermod` is used to modify an existing user account.

Example:

```bash
sudo usermod [options] username
```

Common examples include changing:

* Groups
* Login shell
* Home directory
* UID

For example:

```bash
sudo usermod -s /bin/bash john
```

Changes John's login shell to Bash.

---

# 17. What Are Groups?

A **group** is a collection of users.

Groups are mainly used to simplify permission management.

Instead of giving permissions to each user individually:

```text
User A → Permission
User B → Permission
User C → Permission
```

we can create:

```text
Developers Group
│
├── User A
├── User B
└── User C
```

Then assign permissions to the group.

---

# 18. Primary Group vs Supplementary Groups

Every user has a **primary group**.

A user can also belong to multiple **supplementary groups**.

Example:

```text
User: amr

Primary Group:
amr

Supplementary Groups:
sudo
docker
developers
```

In `/etc/passwd`:

```text
amr:x:1000:1000::/home/amr:/bin/bash
              ↑
         Primary GID
```

The GID points to the user's primary group.

---

# 19. `/etc/group`

Group information is stored in:

```text
/etc/group
```

Example:

```text
developers:x:1001:amr,john
```

The general format is:

```text
group_name:password_placeholder:GID:group_members
```

Breaking it down:

```text
developers:x:1001:amr,john
```

| Field          | Value        | Meaning                     |
| -------------- | ------------ | --------------------------- |
| Group Name     | `developers` | Group name                  |
| Password field | `x`          | Legacy/placeholder field    |
| GID            | `1001`       | Group ID                    |
| Members        | `amr,john`   | Supplementary group members |

---

# 20. Viewing Groups

To see the groups of the current user:

```bash
groups
```

Example:

```text
amr sudo docker
```

To see groups for a specific user:

```bash
groups username
```

Another useful command is:

```bash
id
```

Example:

```bash
id
```

This can show:

* UID
* Primary GID
* Supplementary groups

Example output:

```text
uid=1000(amr) gid=1000(amr) groups=1000(amr),27(sudo),999(docker)
```

---

# 21. Managing Groups

## Create a Group

```bash
sudo groupadd developers
```

---

## Delete a Group

```bash
sudo groupdel developers
```

---

## Add a User to a Supplementary Group

```bash
sudo usermod -aG developers john
```

### Important

The `-a` option means:

```text
append
```

When using:

```bash
usermod -G
```

without `-a`, you may replace the user's existing supplementary groups.

Therefore:

```bash
sudo usermod -aG developers john
```

is generally safer when adding a user to an existing group.

---

# 22. Applying Group Changes

After adding a user to a group, the user may need to:

* Log out and log back in, or
* Start a new login session

for the new group membership to be reflected.

You can check your current groups using:

```bash
id
```

or:

```bash
groups
```

---

# 23. Practical Example

Let's create a new user:

```bash
sudo useradd -m -s /bin/bash john
```

Set a password:

```bash
sudo passwd john
```

Create a group:

```bash
sudo groupadd developers
```

Add the user to the group:

```bash
sudo usermod -aG developers john
```

Verify:

```bash
id john
```

The conceptual result:

```text
john
 │
 ├── UID: 1001
 │
 ├── Primary Group: john
 │
 └── Supplementary Group:
       developers
```

---

# 24. Important Files

```text
/etc/passwd
→ User account information

/etc/shadow
→ Password hashes and password aging information

/etc/group
→ Group information

/etc/sudoers
→ sudo configuration

/etc/sudoers.d/
→ Additional sudo configuration
```

---

# Key Commands

## User Management

```text
useradd        → create user
userdel        → delete user
usermod        → modify user
passwd         → change/reset password
su             → switch user
sudo           → execute command with elevated privileges
```

## Group Management

```text
groupadd       → create group
groupdel       → delete group
groups         → show user's groups
id             → show UID, GID, and groups
```

---

# Key Takeaways

```text
UID = User ID
GID = Group ID

UID 0
→ root

/etc/passwd
→ user account information

/etc/shadow
→ password hashes and password information

/etc/group
→ group information

Primary Group
→ one main group

Supplementary Groups
→ additional groups

sudo
→ execute a command with elevated privileges
```

The central idea is:

```text
                USER
                  │
        ┌─────────┴─────────┐
        │                   │
       UID              Groups
        │                   │
        └─────────┬─────────┘
                  │
             Permissions
```

Understanding users and groups is essential before moving on to **Linux file permissions**, because permissions are based heavily on the relationship between:

```text
User → Group → File Permissions
```
