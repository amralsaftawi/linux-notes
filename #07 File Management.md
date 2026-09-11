# File Management

Linux provides a hierarchical filesystem for organizing files and directories.

Understanding file management requires understanding:

* Paths
* Directories
* File metadata
* Inodes
* Links
* File system blocks

---

# 1. Absolute Path

An **absolute path** describes the location of a file or directory starting from the root directory:

```text
/
```

For example:

```text
/home/amr/projects/app
```

This path always starts from `/`, so it does not depend on the current working directory.

Example:

```bash
cd /home/amr
```

No matter where you currently are, Linux can resolve this path from the root of the filesystem.

---

# 2. Relative Path

A **relative path** describes a file or directory relative to the current working directory.

For example, if:

```bash
pwd
```

returns:

```text
/home/amr
```

and there is a directory:

```text
/home/amr/projects
```

you can access it using:

```bash
cd projects
```

This is a relative path.

Common relative path symbols:

```text
.   → current directory
..  → parent directory
~   → current user's home directory
```

Example:

```bash
cd ../
```

moves to the parent directory.

---

# 3. Understanding `ls -l`

Consider:

```bash
ls -l
```

Example output:

```text
drwxr-xr-x 2 root root 4096 Sep 5 11:32 PackageKit
```

Let's break it down:

```text
drwxr-xr-x 2 root root 4096 Sep 5 11:32 PackageKit
│           │   │    │    │                │
│           │   │    │    │                └── File/Directory name
│           │   │    │    └── Last modification date/time
│           │   │    └── Size in bytes
│           │   └── Group owner
│           └── User/Owner
│
└── File type + permissions
```

## First Character — File Type

The first character indicates the file type:

```text
-   → regular file
d   → directory
l   → symbolic link
c   → character device
b   → block device
p   → named pipe (FIFO)
s   → socket
```

In:

```text
drwxr-xr-x
```

the first character is:

```text
d
```

so `PackageKit` is a directory.

---

## Permissions

The remaining nine characters:

```text
rwxr-xr-x
```

are divided into three groups:

```text
rwx | r-x | r-x
 │     │     │
 │     │     └── Others
 │     └──────── Group
 └────────────── Owner
```

The detailed meaning of these permissions will be covered in the **File Permissions** section.

---

## Link Count

The number:

```text
2
```

is the file's **hard-link count**.

For a directory, this commonly includes links related to:

```text
.   → the directory itself
..  → its parent directory
```

So a newly created directory often starts with a link count of `2`, although the exact count changes as subdirectories are created.

---

## Owner

```text
root
```

This is the user who owns the file/directory.

---

## Group

```text
root
```

This is the group associated with the file/directory.

---

## Size

```text
4096
```

This is the file's reported size in bytes.

For directories, this is generally the size of the directory's filesystem data structure, **not the total size of all files inside it**.

---

# 4. Useful `ls` Options

## `-h` — Human-Readable Sizes

Usually used with `-l`:

```bash
ls -lh
```

Instead of:

```text
4096
1048576
```

you may see:

```text
4.0K
1.0M
```

---

## `-d` — Show the Directory Itself

Normally:

```bash
ls /home
```

shows the contents of `/home`.

But:

```bash
ls -d /home
```

shows `/home` itself rather than listing its contents.

This becomes particularly useful with wildcards:

```bash
ls -ld /home/*
```

which shows information about the directories themselves.

---

## `-R` — Recursive Listing

```bash
ls -R
```

recursively lists directories and their contents.

For example:

```text
project/
├── src/
│   ├── app.cs
│   └── main.cs
└── README.md
```

`ls -R` will continue into `src` and show its contents.

---

# 5. The `tree` Command

`tree` displays directories in a hierarchical tree structure.

Example:

```bash
tree
```

Output might look like:

```text
project
├── README.md
├── src
│   ├── main.cs
│   └── app.cs
└── tests
    └── app.test.cs
```

This is often easier to understand than:

```bash
ls -R
```

because the directory hierarchy is visually clear.

Depending on the Linux distribution, `tree` may need to be installed separately.

---

# 6. How Linux Represents a File

A common beginner misconception is that a file is simply:

```text
File name + data
```

Internally, a filesystem maintains more information.

A useful simplified model is:

```text
Directory Entry
      │
      ├── File Name
      │
      └── Inode Number
             │
             ↓
           Inode
             │
       ┌─────┴─────┐
       │           │
   Metadata     Data pointers
                   │
                   ↓
              Data Blocks
```

The exact implementation depends on the filesystem, but this model is extremely useful for understanding Linux.

---

# 7. File Name

The filename is stored as part of a **directory entry**.

For example:

```text
org
```

The directory entry associates:

```text
org → inode 941
```

The filename itself is not the inode.

---

# 8. Inode

An **inode** is a data structure containing metadata about a filesystem object.

An inode can contain information such as:

* File type
* Permissions
* Owner UID
* Group GID
* File size
* Timestamps
* Link count
* Pointers/references to the file's data blocks

For example:

```text
inode 941
│
├── File type
├── Permissions
├── UID
├── GID
├── Size
├── Timestamps
├── Link count
└── References to data blocks
```

### Important

The inode **does not normally contain the file's actual contents**.

The actual contents are stored in **data blocks**, and the inode contains information that allows the filesystem to locate them.

---

# 9. Data Blocks

The actual file contents are stored in filesystem blocks.

For example, conceptually:

```text
File:
"Hello Linux"
      │
      ↓
Data Blocks
┌──────────────┐
│ Hello Linux  │
└──────────────┘
```

The inode contains the metadata and references needed to locate these blocks.

The exact block allocation and addressing mechanism depends on the filesystem.

---

# 10. The `stat` Command

The `stat` command displays detailed information about a file.

Example:

```bash
stat org
```

Example output:

```text
File: org
Size: 0          Blocks: 0          IO Block: 4096   regular empty file
Device: 8,48     Inode: 941         Links: 1
Access: (0644/-rw-r--r--)  Uid: (1000/amr)   Gid: (1000/amr)
Access: 2026-09-11 21:57:34.878439728 +0300
Modify: 2026-09-11 21:57:34.878439728 +0300
Change: 2026-09-11 21:57:34.878439728 +0300
Birth: 2026-09-11 21:57:34.878439728 +0300
```

---

# 11. Understanding `stat` Output

## File

```text
File: org
```

The filename is:

```text
org
```

---

## Size

```text
Size: 0
```

The file currently contains zero bytes.

---

## Blocks

```text
Blocks: 0
```

This indicates the number of filesystem blocks allocated to the file according to the filesystem's accounting.

An empty file may have:

```text
Size: 0
Blocks: 0
```

because it contains no data blocks.

---

## IO Block

```text
IO Block: 4096
```

This is the filesystem's preferred block size for I/O in this context.

It does **not** mean that every file must occupy exactly 4096 bytes.

---

## File Type

```text
regular empty file
```

This tells us that `org` is a regular file and currently contains no data.

---

## Device

```text
Device: 8,48
```

This identifies the device/filesystem containing the inode.

The exact meaning of the numbers is system-dependent.

---

## Inode

```text
Inode: 941
```

The file's inode number is:

```text
941
```

This is a very important identifier inside the filesystem.

---

## Links

```text
Links: 1
```

This indicates that one directory entry currently refers to this inode.

If we create a hard link:

```bash
ln org org-hard
```

the link count will normally become:

```text
Links: 2
```

because two filenames now refer to the same inode.

---

# 12. Permissions, UID and GID

```text
Access: (0644/-rw-r--r--)
Uid: (1000/amr)
Gid: (1000/amr)
```

This tells us:

```text
UID 1000 → amr
GID 1000 → amr
Permissions → 0644
```

The permissions will be explained in detail in the next section.

---

# 13. File Timestamps

Linux tracks several important timestamps.

## Access Time — `atime`

```text
Access: ...
```

Generally indicates when the file was last accessed/read.

The exact behavior can depend on filesystem mount options.

---

## Modification Time — `mtime`

```text
Modify: ...
```

Indicates when the **contents** of the file were last modified.

For example:

```bash
echo "some text" > org
```

changes the file's contents, so `mtime` changes.

---

## Change Time — `ctime`

```text
Change: ...
```

Indicates when the file's **metadata or inode information** changed.

For example:

* Permissions changed
* Owner changed
* Link count changed
* File contents changed

can cause `ctime` to change.

> `ctime` does **not** mean "creation time".

---

## Birth Time

```text
Birth: ...
```

If supported by the filesystem, this represents the file's creation time.

However, birth/creation time is filesystem-dependent and may not be available on every Linux filesystem.

---

# 14. Observing Changes with `stat`

Initially:

```bash
touch org
```

The file is empty:

```text
Size: 0
Blocks: 0
```

Now write data:

```bash
echo "some text" > org
```

The file becomes:

```text
Size: 10
```

because:

```text
"some text"
```

contains 9 characters plus the newline added by `echo`:

```text
some text\n
```

Total:

```text
10 bytes
```

The inode number remains the same:

```text
Inode: 941
```

This is an important observation.

Writing to a file normally changes its contents and metadata such as timestamps, but it does not mean the filename gets a new inode.

---

# 15. Links

Linux supports two important types of links:

```text
1. Hard Link
2. Symbolic Link (Soft Link)
```

They work differently.

---

# 16. Hard Links

A **hard link** is another directory entry that refers to the **same inode**.

Example:

```bash
ln org org-hard
```

Conceptually:

```text
        org
         │
         ↓
      Inode 941
         │
         ↓
      Data Blocks

    org-hard
         │
         └──────→ Inode 941
```

Both filenames refer to the same inode and therefore the same file data.

You can think of them as:

```text
Two names → One filesystem object
```

---

# 17. Hard Link Example

Create the original file:

```bash
echo "Hello" > org
```

Create a hard link:

```bash
ln org org-hard
```

Check the inode numbers:

```bash
ls -li org org-hard
```

You should see the **same inode number** for both.

For example:

```text
941 -rw-r--r-- 2 amr amr 6 ... org
941 -rw-r--r-- 2 amr amr 6 ... org-hard
```

Notice:

```text
941
941
```

Same inode.

And:

```text
2
2
```

The link count is now `2`.

---

# 18. What Happens If the Original Filename Is Deleted?

Suppose:

```text
org
org-hard
```

both refer to inode `941`.

Now:

```bash
rm org
```

The filename `org` is removed.

But:

```text
org-hard
```

still exists.

Why?

Because the inode still has another directory entry pointing to it.

Conceptually:

```text
Before:

org ──────┐
          ├──→ inode 941 → data
org-hard ─┘


After rm org:

org-hard ─────→ inode 941 → data
```

The data remains accessible through `org-hard`.

The filesystem can reclaim the inode/data when there are no remaining references to it and no process still has the file open.

---

# 19. Symbolic Links — Soft Links

A **symbolic link** is a separate filesystem object that contains a reference/path to another file or directory.

Create one with:

```bash
ln -s org org-soft
```

Conceptually:

```text
org-soft
   │
   │  symbolic reference
   ↓
  org
   │
   ↓
 inode
   │
   ↓
 data
```

Unlike a hard link:

```text
org-soft
```

has its **own inode**.

---

# 20. Hard Link vs Soft Link

| Feature                     | Hard Link                        | Symbolic Link             |
| --------------------------- | -------------------------------- | ------------------------- |
| Same inode as target?       | Yes                              | No                        |
| Separate filesystem object? | No, another directory entry      | Yes                       |
| Can point to directory?     | Generally no                     | Yes                       |
| Can cross filesystems?      | No                               | Yes                       |
| Can become dangling?        | No, if another hard link remains | Yes                       |
| Shares file data?           | Yes                              | Indirectly through target |
| Created with                | `ln`                             | `ln -s`                   |

---

# 21. What Happens When the Target Is Deleted?

This is where hard links and soft links behave very differently.

Suppose:

```text
org
org-hard
org-soft
```

where:

```text
org
   ↑
org-hard → same inode

org-soft → path "org"
```

If we execute:

```bash
rm org
```

### Hard Link

```text
org-hard
```

still works because it points directly to the same inode.

### Symbolic Link

```text
org-soft
```

becomes a **dangling/broken symbolic link** because its target path:

```text
org
```

no longer exists.

So:

```text
Hard Link
→ points to inode

Soft Link
→ points to pathname
```

This is the most important distinction to remember.

---

# 22. Visual Model

## Hard Link

```text
             ┌──────────────┐
org ─────────┤              │
             │   Inode 941  │
org-hard ────┤              │
             └──────┬───────┘
                    │
                    ↓
               Data Blocks
```

Two names directly reference the same inode.

---

## Symbolic Link

```text
org-soft
    │
    │ contains/reference path
    ↓
   "org"
     │
     ↓
   Inode 941
     │
     ↓
 Data Blocks
```

The symbolic link has its own inode and refers to the target by path.

---

# 23. Useful Commands for Investigating Inodes and Links

Show inode numbers:

```bash
ls -li
```

Detailed file information:

```bash
stat filename
```

Create a hard link:

```bash
ln source target
```

Create a symbolic link:

```bash
ln -s source target
```

Find the target of a symbolic link:

```bash
readlink linkname
```

Show symbolic links clearly:

```bash
ls -l
```

Example:

```text
org-soft -> org
```

---

# 24. Important Mental Model

When thinking about Linux filesystems, don't imagine:

```text
filename → data
```

A better model is:

```text
Directory Entry
      │
      ├── filename
      │
      └── inode number
              │
              ↓
            Inode
              │
       ┌──────┴──────┐
       │             │
   Metadata      Data references
                     │
                     ↓
                Data Blocks
```

This model explains:

* Inodes
* Hard links
* File metadata
* File permissions
* File ownership
* File timestamps
* Why deleting a filename does not necessarily delete the underlying data immediately

---

# Quick Review

### Paths

```text
Absolute Path
→ starts from /

Relative Path
→ starts from current directory
```

### `ls`

```text
ls -lh
→ human-readable sizes

ls -d
→ show directory itself

ls -R
→ recursive listing

tree
→ hierarchical directory view
```

### File Structure

```text
Filename
   ↓
Directory Entry
   ↓
Inode
   ↓
Data Blocks
```

### `stat`

```text
stat file
→ detailed metadata
```

### Links

```text
Hard Link
→ another directory entry for the same inode

Symbolic Link
→ separate object that refers to another pathname
```

### The Key Difference

```text
Hard Link:
name ──→ inode

Soft Link:
name ──→ symlink inode ──→ target pathname ──→ target inode
```

Understanding this structure is essential before learning **Linux file permissions**, because permissions are stored as inode metadata and are applied based on the file's owner, group, and permission bits.
