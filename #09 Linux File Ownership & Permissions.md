# Linux File Ownership & Permissions

Linux is a multi-user operating system. Multiple users can access the same system and the same files, so Linux needs a mechanism to control:

- Who owns a file or directory
- Which group is associated with it
- Who can read, modify, or execute it

This is handled mainly through **ownership** and **permissions**.

---

# 1. File Ownership

Every file and directory has an owner and a group associated with it.

For example:

```text
-rw-r--r-- 2 amr amr 10 Sep 11 22:00 org
```

Here:

```text
amr → owner
amr → group
```

The first `amr` is the **user owner**.

The second `amr` is the **group owner**.

A file is normally owned by the user who creates it, and its group ownership is normally determined by the creator's primary group and the system's group-inheritance rules.

---

# 2. Understanding `ls -l`

Consider:

```bash
ls -l
```

Example:

```text
-rw-r--r-- 2 amr amr 10 Sep 11 22:00 org
```

Break it down:

```text
-rw-r--r-- 2 amr amr 10 Sep 11 22:00 org
│          │ │   │   │             │
│          │ │   │   │             └── filename
│          │ │   │   └── modification time
│          │ │   └── group owner
│          │ └── user owner
│          └── number of hard links
└── file type + permissions
```

More precisely:

```text
- rw- r-- r--
│ │   │   │
│ │   │   └── other permissions
│ │   └────── group permissions
│ └────────── owner/user permissions
└──────────── file type
```

---

# 3. File Type

The first character is the **file type**, not a permission.

Examples:

```text
-    regular file
d    directory
l    symbolic link
c    character device
b    block device
```

For example:

```text
-rw-r--r--
```

starts with:

```text
-
```

which means this is a regular file.

A directory might look like:

```text
drwxr-xr-x
```

The first character:

```text
d
```

means directory.

---

# 4. The Three Permission Categories

Linux permissions are divided into three categories:

```text
User     → owner
Group    → owning group
Other    → everyone else
```

The permission string:

```text
rwxrwxrwx
```

can be divided into:

```text
rwx | rwx | rwx
 ↓     ↓     ↓
user  group other
```

For example:

```text
-rwxr-xr--
```

means:

```text
user:  rwx
group: r-x
other: r--
```

---

# 5. The Three Basic Permissions

Linux has three basic permission types:

```text
r → read
w → write
x → execute
```

They have different meanings depending on whether the target is a **file** or a **directory**.

---

# 6. Permissions on Regular Files

## Read `r`

For a regular file:

```text
r → read the contents of the file
```

Example:

```bash
cat file.txt
```

requires read permission on the file.

---

## Write `w`

```text
w → modify the contents of the file
```

For example:

```bash
echo "Hello" > file.txt
```

requires appropriate write access to modify the file.

---

## Execute `x`

```text
x → execute the file as a program/script
```

For example:

```bash
./script.sh
```

requires execute permission on the script.

A file can therefore have:

```text
rw-
```

meaning:

```text
read
write
no execute
```

---

# 7. Permissions on Directories

Directory permissions are slightly different.

This is extremely important.

## Read `r` on a Directory

```text
r → list the names of entries inside the directory
```

For example:

```bash
ls /some-directory
```

requires read permission on the directory to list its entries.

---

## Write `w` on a Directory

```text
w → create, delete, or rename entries inside the directory
```

This permission applies to the directory's entries rather than simply meaning "modify files."

For example, if you have write permission on:

```text
/home/amr/test
```

you may be able to create or remove files inside that directory, subject to the other permission requirements and special bits.

---

## Execute `x` on a Directory

For a directory:

```text
x → traverse/search the directory
```

It allows you to access entries inside the directory when you know their names and to traverse the directory as part of a path.

For example:

```text
/home/amr/project/file.txt
```

To access `file.txt`, you generally need execute (`x`) permission on the directories in the path.

---

# 8. File vs Directory Permissions

This is one of the most important things to remember:

| Permission | Regular File | Directory |
|---|---|---|
| `r` | Read file contents | List directory entries |
| `w` | Modify file contents | Create/delete/rename entries |
| `x` | Execute file | Traverse/search directory |

Therefore:

```text
rwx
```

does **not** have exactly the same meaning for a file and a directory.

---

# 9. Example: `-rw-r--r--`

Consider:

```text
-rw-r--r--
```

Ignore the first `-` for now.

We have:

```text
rw- | r-- | r--
```

### Owner

```text
rw-
```

The owner can:

```text
read
write
```

but cannot execute.

### Group

```text
r--
```

Members of the group can:

```text
read
```

but cannot write or execute.

### Other

```text
r--
```

Everyone else can:

```text
read
```

but cannot write or execute.

---

# 10. Missing Permissions

If a permission is not granted, Linux displays:

```text
-
```

For example:

```text
rw-r--r--
```

means:

```text
r w -
r - -
r - -
```

The `-` means that particular permission is not granted.

---

# 11. Numeric Permission Representation

Permissions can also be represented numerically.

The values are:

```text
r = 4
w = 2
x = 1
```

We add the values together.

### Read Only

```text
r--
```

```text
4
```

So:

```text
r-- = 4
```

### Write Only

```text
-w-
```

```text
2
```

### Execute Only

```text
--x
```

```text
1
```

### Read + Write

```text
rw-
```

```text
4 + 2 = 6
```

### Read + Execute

```text
r-x
```

```text
4 + 1 = 5
```

### Write + Execute

```text
-wx
```

```text
2 + 1 = 3
```

### Read + Write + Execute

```text
rwx
```

```text
4 + 2 + 1 = 7
```

---

# 12. Three-Digit Numeric Permissions

Because Linux has three permission categories:

```text
user | group | other
```

we use three numbers.

For example:

```text
755
```

means:

```text
7 | 5 | 5
│   │   │
│   │   └── other
│   └────── group
└────────── user
```

Convert each number:

```text
7 = 4 + 2 + 1 = rwx
5 = 4 + 1     = r-x
5 = 4 + 1     = r-x
```

Therefore:

```text
755
```

means:

```text
rwxr-xr-x
```

---

# 13. Common Permission Examples

## `644`

```text
644
```

becomes:

```text
rw-r--r--
```

Meaning:

```text
owner → read + write
group → read
other → read
```

This is a very common permission for regular files.

---

## `755`

```text
755
```

becomes:

```text
rwxr-xr-x
```

Meaning:

```text
owner → read + write + execute
group → read + execute
other → read + execute
```

This is commonly used for executable files and directories.

---

## `700`

```text
700
```

becomes:

```text
rwx------
```

Meaning:

```text
owner → read + write + execute
group → no permissions
other → no permissions
```

Useful when something should be accessible only by its owner.

---

# 14. Changing Permissions with `chmod`

The command used to change file access permissions is:

```bash
chmod
```

The FHS lists `chmod` as the utility used to change file access permissions.

There are two common styles:

1. Symbolic mode
2. Numeric mode

---

# 15. Symbolic `chmod`

The permission categories are represented by:

```text
u → user/owner
g → group
o → other
a → all
```

Operators:

```text
+ → add permission
- → remove permission
= → set exactly
```

---

# 16. Adding Permissions

Example:

```bash
chmod u+x script.sh
```

Add execute permission for the owner.

Before:

```text
rw-
```

After:

```text
rwx
```

---

### Add Execute for Group

```bash
chmod g+x script.sh
```

---

### Add Execute for Everyone

```bash
chmod a+x script.sh
```

Equivalent to:

```bash
chmod u+x,g+x,o+x script.sh
```

---

# 17. Removing Permissions

To remove read permission from the owner:

```bash
chmod u-r file.txt
```

To remove write permission from the group:

```bash
chmod g-w file.txt
```

To remove execute permission from others:

```bash
chmod o-x file.txt
```

---

# 18. Setting Exact Permissions

The `=` operator sets the permissions exactly.

Example:

```bash
chmod u=rw file.txt
```

The owner gets:

```text
rw-
```

and execute is removed if it existed.

You can set multiple categories:

```bash
chmod u=rw,g=r,o= file.txt
```

This produces:

```text
rw-r-----
```

---

# 19. Numeric `chmod`

Instead of:

```bash
chmod u=rwx,g=rx,o=rx script.sh
```

you can write:

```bash
chmod 755 script.sh
```

Instead of:

```bash
chmod u=rw,g=r,o=r file.txt
```

you can write:

```bash
chmod 644 file.txt
```

Numeric notation is very common in Linux administration.

---

# 20. Changing File Ownership

The command:

```bash
chown
```

is used to change the owner of a file or directory.

The FHS lists `chown` as the utility for changing file owner and group.

Basic syntax:

```bash
chown USER:GROUP FILE
```

Example:

```bash
sudo chown john:developers project.txt
```

Now:

```text
owner  → john
group  → developers
```

---

# 21. Change Only the Owner

To change only the user owner:

```bash
sudo chown john file.txt
```

The group remains unchanged.

---

# 22. Change Only the Group

To change only the group:

```bash
sudo chown :developers file.txt
```

The owner remains unchanged.

Another command commonly used specifically for group ownership is:

```bash
chgrp developers file.txt
```

---

# 23. Important: `usermod -g` Does Not Change Existing Files

Suppose:

```bash
sudo groupadd admins
```

creates:

```text
admins:x:1001:
```

Then:

```bash
sudo usermod -g admins amr
```

changes `amr`'s **primary group** to:

```text
admins
```

But this does **not** mean that all files previously owned by:

```text
amr:amr
```

automatically become:

```text
amr:admins
```

Existing files keep their current group ownership unless you explicitly change it.

For example:

```bash
sudo chown :admins file.txt
```

changes the file's group.

For many files/directories recursively:

```bash
sudo chown -R amr:admins project/
```

Use `-R` carefully because it affects everything underneath the specified directory.

---

# 24. Primary Group vs File Group

A user can belong to multiple groups.

For example:

```text
User:
amr

Primary group:
amr

Other groups:
developers
docker
admins
```

When a new file is created, its group ownership is normally based on the creator's primary group and the directory's group inheritance rules.

Changing the user's primary group affects future file creation; it does not retroactively rewrite ownership of existing files.

---

# 25. Ownership and Permissions Work Together

Consider:

```text
-rw-r----- 1 amr developers 100 app.log
```

We have:

```text
owner → amr
group → developers
```

Permissions:

```text
rw- | r-- | ---
```

Therefore:

```text
amr
→ read + write

developers
→ read

everyone else
→ no permissions
```

This allows a team to share files through a group while preventing other users from accessing them.

---

# 26. Ownership Does Not Mean "Can Do Everything"

Being the owner does not mean:

```text
owner = unlimited permissions
```

The owner's access is normally determined by the user permission bits.

For example:

```text
-r--r--r-- 1 amr amr file.txt
```

The owner has only:

```text
r--
```

So the file itself is not writable by the owner through ordinary permission checks.

The owner generally has the ability to change the file's permission bits with `chmod`, subject to the system's security model.

The `root` user, however, has much broader privileges.

---

# 27. Directory Example

Suppose:

```text
drwxr-xr-x 2 amr amr 4096 Sep 11 22:00 project
```

Break it down:

```text
d | rwx | r-x | r-x
  |     |     |
  |     |     └── other
  |     └──────── group
  └────────────── owner
```

Because this is a directory:

```text
rwx
```

for the owner means:

```text
r → list entries
w → create/delete/rename entries
x → enter/traverse/search the directory
```

Group:

```text
r-x
```

means:

```text
r → list entries
x → traverse/search
w → cannot modify directory entries
```

Other:

```text
r-x
```

has the same permissions.

---

# 28. Why Directory `x` Is Important

Consider:

```text
/home/amr/project/file.txt
```

To reach:

```text
file.txt
```

the process needs to traverse the directories in the path.

So directory `x` is often described as:

```text
search / traverse permission
```

This is why you can encounter situations where:

```text
you know the filename
```

but still cannot access it because you don't have the necessary directory permissions.

---

# 29. `ls -l` and Permission Analysis

When troubleshooting access problems, start with:

```bash
ls -l
```

Example:

```text
-rw-r----- 1 amr developers 1024 app.log
```

Ask three questions:

### 1. Who is the owner?

```text
amr
```

### 2. What is the group?

```text
developers
```

### 3. What permissions does each category have?

```text
user:   rw-
group:  r--
other:  ---
```

Then determine which category the current user falls into.

---

# 30. Changing Permissions Recursively

You can use:

```bash
chmod -R
```

to recursively change permissions.

Example:

```bash
chmod -R 755 project/
```

This changes permissions for the directory and everything underneath it.

### Be Careful

Recursive permission changes can be dangerous.

For example:

```bash
chmod -R 777 /
```

would be an extremely dangerous command.

Avoid blindly using recursive commands on system directories.

---

# 31. Common Permission Patterns

### Regular File

```text
-rw-r--r--
```

Numeric:

```text
644
```

Common for files that should be readable by others but writable only by the owner.

---

### Executable File

```text
-rwxr-xr-x
```

Numeric:

```text
755
```

---

### Private File/Directory

```text
rwx------
```

Numeric:

```text
700
```

---

# 32. Permission Mental Model

Think of every file or directory as having:

```text
                 Object
                   │
          ┌────────┴────────┐
          ↓                 ↓
       Ownership         Permissions
          │                 │
      ┌───┴───┐       ┌─────┼─────┐
      ↓       ↓       ↓     ↓     ↓
    User    Group     User  Group Other
```

For permissions:

```text
User
 ↓
rwx

Group
 ↓
rwx

Other
 ↓
rwx
```

---

# 33. Full Example

Consider:

```text
-rwxr-x--- 1 amr developers 2048 Sep 13 12:00 deploy.sh
```

### File type

```text
-
```

Regular file.

### Owner

```text
amr
```

### Group

```text
developers
```

### Owner permissions

```text
rwx
```

```text
4 + 2 + 1 = 7
```

### Group permissions

```text
r-x
```

```text
4 + 1 = 5
```

### Other permissions

```text
---
```

```text
0
```

Therefore:

```text
chmod 750 deploy.sh
```

represents:

```text
rwxr-x---
```

Meaning:

```text
owner:
read + write + execute

developers group:
read + execute

everyone else:
no permissions
```

---

# 34. Ownership Commands Summary

| Command | Purpose |
|---|---|
| `chown user file` | Change owner |
| `chown user:group file` | Change owner and group |
| `chown :group file` | Change group |
| `chgrp group file` | Change group |
| `usermod -g group user` | Change user's primary group |
| `ls -l` | View ownership and permissions |

---

# 35. Permission Commands Summary

| Command | Purpose |
|---|---|
| `chmod 644 file` | Set numeric permissions |
| `chmod 755 file` | Set executable-style permissions |
| `chmod u+x file` | Add execute for owner |
| `chmod g+w file` | Add write for group |
| `chmod o-r file` | Remove read from others |
| `chmod a+x file` | Add execute for everyone |
| `chmod u=rw file` | Set owner permissions exactly |
| `chmod -R ... dir/` | Apply recursively |

---

# 36. Important Distinction: `chmod` vs `chown` vs `usermod`

These commands solve different problems:

```text
usermod
   ↓
changes USER configuration


chown
   ↓
changes FILE ownership


chmod
   ↓
changes FILE permissions
```

Example:

```bash
sudo usermod -g developers amr
```

changes:

```text
amr's primary group
```

While:

```bash
sudo chown amr:developers project.txt
```

changes:

```text
project.txt ownership
```

And:

```bash
chmod 640 project.txt
```

changes:

```text
project.txt permissions
```

---

# 37. A Practical Access-Checking Workflow

When you get:

```text
Permission denied
```

do not immediately use:

```bash
sudo
```

Instead, inspect the object.

### Step 1 — Check ownership and permissions

```bash
ls -l file.txt
```

### Step 2 — Check your current user

```bash
whoami
```

### Step 3 — Check your groups

```bash
groups
```

### Step 4 — Compare

For example:

```text
-rw-r----- 1 amr developers file.txt
```

If you are:

```text
john
```

and are a member of:

```text
developers
```

then you use the **group permissions**:

```text
r--
```

You do not use the owner permissions simply because you can see the owner's name.

---

# 38. The Three Layers You Should Always Check

For a path such as:

```text
/home/amr/project/app.log
```

access can depend on permissions for the directories in the path as well as the file itself.

Think:

```text
/home
   ↓
/home/amr
   ↓
/home/amr/project
   ↓
app.log
```

The directories need appropriate **execute/traverse** permissions to reach the file.

Then the file's own permissions determine what you can do with its contents.

---

# 39. Quick Review

### Ownership

Every file/directory has:

```text
user owner
group owner
```

Example:

```text
amr developers
```

---

### Permissions

There are three categories:

```text
user
group
other
```

Each can have:

```text
r
w
x
```

---

### Numeric values

```text
r = 4
w = 2
x = 1
```

Therefore:

```text
rwx = 7
rw- = 6
r-x = 5
r-- = 4
-wx = 3
-w- = 2
--x = 1
--- = 0
```

---

### Main commands

```text
ls -l
   ↓
inspect ownership + permissions

chmod
   ↓
change permissions

chown
   ↓
change owner/group

chgrp
   ↓
change group

usermod
   ↓
change user configuration
```

---

# Final Mental Model

When you see:

```text
-rwxr-x--- 1 amr developers 2048 deploy.sh
```

read it as:

```text
File type:
    regular file

Owner:
    amr

Group:
    developers

Owner permissions:
    rwx = 7

Group permissions:
    r-x = 5

Other permissions:
    --- = 0
```

So:

```text
755? No.

750
```

because:

```text
rwx = 7
r-x = 5
--- = 0
```

Therefore:

```bash
chmod 750 deploy.sh
```

is equivalent to:

```text
-rwxr-x---
```

The central idea is:

```text
                    FILE
                      │
          ┌───────────┴───────────┐
          ↓                       ↓
      OWNERSHIP                PERMISSIONS
          │                       │
     ┌────┴────┐           ┌──────┼──────┐
     ↓         ↓           ↓      ↓      ↓
   User      Group        User   Group  Other
                            │      │      │
                           rwx    r-x    ---
```

Once you understand this model, commands like `chmod`, `chown`, `groups`, `ls -l`, and `sudo` stop being commands to memorize and become tools for controlling **who can access what**.
