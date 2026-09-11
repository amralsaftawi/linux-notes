# Linux Filesystem, Partitioning & Mounting

Understanding the Linux filesystem is important before learning permissions, storage administration, Docker, servers, and DevOps.

The Linux filesystem is not just a collection of folders. It is a **hierarchical namespace** that starts at a single root:

```text
/
```

The filesystem hierarchy defines where different types of files should live and gives the operating system a consistent structure.

The **Filesystem Hierarchy Standard (FHS)** defines a standard hierarchy for Linux and other Unix-like systems. Its goal is to standardize the location and purpose of files and directories. The FHS is a reference standard for system integrators, package developers, and administrators.

---

# 1. The Linux Filesystem Hierarchy

The Linux filesystem starts at:

```text
/
```

This is called the **root directory**.

A simplified filesystem may look like:

```text
/
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── root
├── run
├── sbin
├── srv
├── tmp
├── usr
└── var
```

The FHS defines the purpose of these directories.

For example:

```text
/boot   → boot-related files
/dev    → device files
/etc    → system configuration
/home   → user home directories
/opt    → add-on application software
/root   → root user's home directory
/run    → runtime data
/tmp    → temporary files
/usr    → secondary hierarchy
/var    → variable data
```

The FHS specifies these directories as part of the root filesystem hierarchy.

---

# 2. Root Directory `/`

The `/` directory is the starting point of the entire Linux filesystem hierarchy.

For example:

```text
/
├── home
│   └── amr
│       └── project
│           └── app.cs
```

The absolute path of `app.cs` is:

```text
/home/amr/project/app.cs
```

Everything is located somewhere below `/`.

## Important: Root Directory vs Root User

Do not confuse:

```text
/
```

with:

```text
root
```

They are completely different concepts.

### Root Directory

```text
/
```

The root of the filesystem hierarchy.

### Root User

```text
root
```

The superuser with UID `0`.

For example:

```text
root@server:/root#
```

Here:

```text
root        → current user
/           → root directory
/root       → root user's home directory
#           → privileged shell prompt
```

---

# 3. Linux Does Not Organize Storage Like Windows

One of the biggest differences between Linux and Windows is how storage is represented.

In Windows, you commonly see:

```text
C:\
D:\
E:\
```

Each drive/volume appears as a separate drive letter.

For example:

```text
C:\Users\Amr
D:\Projects
E:\Backup
```

Linux instead presents storage as one unified directory tree:

```text
/
├── home
├── etc
├── usr
├── var
└── ...
```

Different filesystems or partitions can be attached to directories inside this tree.

This is called **mounting**.

---

# 4. Disk, Partition, Filesystem, and Mount Point

These four concepts should not be confused.

## Disk

A physical or virtual storage device.

Example:

```text
/dev/sda
```

or:

```text
/dev/nvme0n1
```

---

## Partition

A partition is a logical division of a disk.

For example:

```text
/dev/sda
│
├── /dev/sda1
├── /dev/sda2
└── /dev/sda3
```

The partitions are separate regions of the disk.

A partition can contain a filesystem.

---

## Filesystem

A filesystem defines how files and directories are organized and stored inside a storage area.

Examples include:

```text
ext4
XFS
Btrfs
NTFS
```

Conceptually:

```text
Partition
    ↓
Filesystem
    ↓
Files and directories
```

A partition and a filesystem are therefore **not the same thing**.

---

## Mount Point

A mount point is a directory where a filesystem becomes accessible inside the Linux directory tree.

For example:

```text
/dev/sda2
    ↓
   /home
```

Here:

```text
/dev/sda2 → filesystem/device
/home     → mount point
```

After mounting it, the filesystem stored on `/dev/sda2` becomes accessible through:

```text
/home
```

---

# 5. Understanding Mounting

The concept of mounting is one of the most important differences between Linux and Windows.

Suppose we have:

```text
/dev/sda1
```

containing a filesystem.

We can mount it at:

```text
/data
```

Conceptually:

```text
/dev/sda1
     │
     │ mount
     ↓
    /data
```

After mounting:

```text
/data
├── database
├── backups
└── files
```

The user accesses the filesystem through:

```bash
cd /data
```

The user does not normally need to think about `/dev/sda1` while using the files.

---

# 6. A Single Linux Filesystem Tree

Imagine a system with:

```text
/dev/sda1
/dev/sda2
/dev/sda3
```

We could have:

```text
/dev/sda1 → /
/dev/sda2 → /home
/dev/sda3 → /data
```

The resulting filesystem appears to the user as:

```text
/
├── boot
├── etc
├── home
│   └── amr
├── usr
├── var
└── data
    ├── files
    └── backups
```

Even though these directories may physically reside on different partitions/filesystems, they appear as one hierarchy.

This is the key idea:

```text
Physical Storage
       ↓
Partitions / Filesystems
       ↓
Mount Points
       ↓
One Unified Directory Tree
```

---

# 7. Why Does Linux Use Mount Points?

The FHS specifically allows parts of the hierarchy to be placed on separate filesystems.

For example, `/usr`, `/opt`, and `/var` are designed so they may be located on other partitions or filesystems. The root filesystem must contain enough software and data to boot the system and mount the other filesystems.

This allows a system administrator to organize storage according to the system's requirements.

For example:

```text
Root filesystem:
/
```

could be relatively small, while:

```text
/home
/var
```

could use separate filesystems.

---

# 8. Why Separate `/home`?

The `/home` directory is intended for user home directories.

For example:

```text
/home
├── amr
├── john
└── sara
```

The FHS describes `/home` as the standard location for user home directories, while noting that the exact setup can differ between systems.

It is possible to have:

```text
/dev/sda1 → /
/dev/sda2 → /home
```

Then:

```text
/
├── etc
├── usr
├── var
└── home
    └── amr
```

is still one filesystem hierarchy from the user's perspective.

But `/home` is actually backed by a different filesystem.

---

# 9. What Happens During Mounting?

Suppose:

```text
/dev/sda2
```

contains:

```text
amr/
john/
sara/
```

and we have an empty directory:

```text
/home
```

When we mount:

```text
/dev/sda2 → /home
```

the contents of that filesystem become accessible through `/home`.

Conceptually:

```text
Before mounting:

/
└── home
    └── empty


/dev/sda2
├── amr
├── john
└── sara
```

After mounting:

```text
/
└── home
    ├── amr
    ├── john
    └── sara
```

The mount point acts as the location where the other filesystem is attached to the existing hierarchy.

---

# 10. What Happens to Existing Files Under a Mount Point?

Suppose `/home` already contains:

```text
/home/test.txt
```

and then another filesystem is mounted on `/home`.

The existing files underneath the mount point are not normally deleted.

Instead, the mounted filesystem becomes what you see through that path while it is mounted.

Conceptually:

```text
Before:

/home
└── test.txt


After mounting another filesystem:

/home
└── files from the mounted filesystem
```

The old contents underneath the mount point are hidden from the normal path while the filesystem is mounted.

They can become visible again after unmounting.

This is an important concept when troubleshooting Linux servers.

---

# 11. Unmounting

To detach a mounted filesystem, Linux uses:

```bash
umount
```

For example:

```bash
sudo umount /data
```

After unmounting:

```text
/dev/sda2
    ↓
   /data
```

is no longer attached to `/data`.

The FHS explicitly lists `umount` as the utility used to unmount filesystems.

---

# 12. `/mnt`

The FHS defines:

```text
/mnt
```

as a mount point intended for a **temporarily mounted filesystem**.

For example:

```bash
sudo mount /dev/sdb1 /mnt
```

Conceptually:

```text
/dev/sdb1
    ↓
   /mnt
```

The FHS describes `/mnt` as a location provided for administrators to temporarily mount a filesystem.

---

# 13. `/media`

The FHS defines:

```text
/media
```

for mount points associated with **removable media**.

Historically, examples include:

```text
/media/cdrom
/media/floppy
```

The FHS describes `/media` as the location for mount points for removable media.

The important distinction is:

```text
/mnt
→ temporary filesystem mounts

/media
→ removable media mount points
```

---

# 14. `/boot`

The `/boot` directory contains files needed during the boot process.

The FHS describes `/boot` as containing the data required for the boot process before the kernel begins executing user-mode programs. The operating system kernel must be located in either `/` or `/boot`.

Conceptually:

```text
/boot
├── kernel
├── bootloader-related files
└── other boot files
```

The exact files depend on the distribution and boot configuration.

---

# 15. `/dev`

The:

```text
/dev
```

directory contains **special/device files**.

The FHS defines `/dev` as the location of special or device files.

Examples may include devices such as:

```text
/dev/sda
/dev/sda1
/dev/tty
```

This is one reason Linux often talks about devices through paths under `/dev`.

For example:

```text
/dev/sda
```

can represent a storage device.

And:

```text
/dev/sda1
```

can represent a partition.

---

# 16. `/etc`

The:

```text
/etc
```

hierarchy contains system configuration files.

The FHS describes `/etc` as the location for host-specific system configuration. Configuration files control the operation of programs and should be static configuration data rather than executable binaries.

Examples include:

```text
/etc/passwd
/etc/shadow
/etc/group
```

which we discussed in the Users and Groups section.

This makes the filesystem hierarchy logical:

```text
/etc
→ system configuration
```

---

# 17. `/home`

The:

```text
/home
```

directory is used for user home directories.

For example:

```text
/home/amr
/home/john
/home/sara
```

User-specific configuration files can also exist in home directories as hidden "dot files" or dot directories:

```text
/home/amr/.config
/home/amr/.bashrc
```

The FHS describes user-specific application configuration files as dot files/directories in the user's home directory.

---

# 18. `/root`

Do not confuse:

```text
/
```

with:

```text
/root
```

`/root` is the recommended default home directory for the **root user**.

So:

```text
/
└── root
```

means:

```text
/root
→ home directory of root user
```

The FHS specifies `/root` as the recommended default location for the root account's home directory.

---

# 19. `/usr`

The `/usr` hierarchy contains a large portion of the system's programs, libraries, documentation, and other static/shareable data.

Examples include:

```text
/usr/bin
/usr/lib
/usr/sbin
/usr/share
```

The FHS treats `/usr` as a **secondary hierarchy** under the root filesystem.

For example:

```text
/usr/bin
```

contains many user commands.

And:

```text
/usr/share
```

contains architecture-independent data such as documentation and manual pages.

---

# 20. `/var`

The `/var` hierarchy is intended for **variable data**.

Variable data means data that changes during normal system operation.

Examples can include:

```text
logs
spools
cache
application state
```

The FHS specifically separates static and variable data. One reason is that static data can potentially be stored on read-only media while variable data needs to remain writable.

Conceptually:

```text
/usr
→ mostly static data

/var
→ variable/changing data
```

---

# 21. Static vs Variable Data

This is an important idea behind the FHS.

## Static

Static files are files that generally do not change without administrator intervention.

Examples include:

```text
binaries
libraries
documentation
```

## Variable

Variable files change during normal system operation.

Examples include:

```text
logs
runtime/application state
spool data
cache
```

The FHS uses this distinction as one reason for separating different parts of the filesystem hierarchy.

This gives administrators more flexibility when deciding how different filesystems should be organized.

---

# 22. Why Can `/usr` Be a Separate Filesystem?

The FHS explicitly states that `/usr`, `/opt`, and `/var` may be located on other partitions or filesystems.

For example:

```text
/dev/sda1 → /
/dev/sda2 → /usr
/dev/sda3 → /var
```

The system still appears as:

```text
/
├── usr
├── var
├── etc
└── ...
```

The difference is where the underlying data is physically/logically stored.

The root filesystem must contain enough software and data to boot the system and mount these other filesystems.

---

# 23. Why Keep the Root Filesystem Manageable?

The FHS discusses keeping the root filesystem as small as reasonably possible while still providing the software and data required for boot, recovery, and repair.

One reason is that the root filesystem contains system-specific information and essential tools.

Another reason is recovery: if other filesystems are unavailable, the root filesystem needs to contain enough tools to diagnose and repair the system.

Conceptually:

```text
Root filesystem
      ↓
Essential system
      ↓
Boot / recovery / repair
      ↓
Mount other filesystems
```

---

# 24. Partitioning Example

Imagine a 500 GB disk:

```text
/dev/sda
```

We could partition it conceptually like:

```text
/dev/sda1   50 GB
/dev/sda2  400 GB
/dev/sda3   50 GB
```

Then create filesystems and mount them:

```text
/dev/sda1 → /
/dev/sda2 → /home
/dev/sda3 → /var
```

The resulting Linux hierarchy is:

```text
/
├── boot
├── dev
├── etc
├── home
│   └── amr
├── usr
├── var
│   ├── log
│   └── ...
└── ...
```

But physically/logically:

```text
/        → /dev/sda1
/home    → /dev/sda2
/var     → /dev/sda3
```

This is the important distinction:

```text
Directory Hierarchy
        ≠
Partition Layout
```

They are related through **mount points**.

---

# 25. Linux vs Windows

## Windows

A typical Windows system exposes storage using drive letters:

```text
C:\
D:\
E:\
```

For example:

```text
C:\Users\Amr
D:\Projects
```

The storage namespace is divided into drive letters.

Conceptually:

```text
C:\
│
└── Users
    └── Amr

D:\
│
└── Projects
```

---

## Linux

Linux uses one main filesystem hierarchy:

```text
/
```

Additional filesystems are mounted into directories:

```text
/
├── home
├── usr
├── var
└── data
```

For example:

```text
/dev/sda1 → /
/dev/sda2 → /home
/dev/sdb1 → /data
```

So Linux can make several different filesystems appear as one unified tree.

---

# 26. Windows Drive Letter vs Linux Mount Point

A useful conceptual comparison is:

```text
Windows:

C:\

D:\

E:\
```

versus:

```text
Linux:

/
├── home
├── data
└── backup
```

But be careful:

> A Linux mount point is not exactly the same thing as a Windows drive letter.

A Windows drive letter identifies a separate storage namespace.

A Linux mount point attaches a filesystem to a location within the existing directory hierarchy.

For example:

```text
Linux:

/data
   ↑
mount point
   ↑
/dev/sdb1
```

---

# 27. Multiple Filesystems in One Linux Tree

Linux can have:

```text
Filesystem A
    ↓
    /

Filesystem B
    ↓
    /home

Filesystem C
    ↓
    /var

Filesystem D
    ↓
    /data
```

Yet the user sees:

```text
/
├── home
├── var
├── data
└── ...
```

This is one of the most important concepts in Linux storage.

---

# 28. Mounting Is Not Copying

Suppose we have:

```text
/dev/sdb1
```

and mount it at:

```text
/mnt/data
```

We are **not copying** the files from `/dev/sdb1` into `/mnt/data`.

Instead, we are attaching the filesystem so its contents become accessible through that directory.

Conceptually:

```text
/dev/sdb1
     │
     │ mount
     ↓
/mnt/data
```

No file-copy operation is implied by mounting.

---

# 29. Partitioning vs Mounting

These are two different operations.

## Partitioning

Divides a disk into partitions.

Example:

```text
/dev/sda
│
├── /dev/sda1
├── /dev/sda2
└── /dev/sda3
```

## Mounting

Makes a filesystem accessible at a location in the directory tree.

Example:

```text
/dev/sda2
     ↓
   /home
```

So:

```text
Partitioning
→ How storage is divided

Mounting
→ Where a filesystem appears in the directory tree
```

---

# 30. Partition ≠ Filesystem ≠ Mount Point

Keep these three separate in your mind.

```text
Disk
 │
 ├── Partition
 │      │
 │      └── Filesystem
 │
 └── Partition
        │
        └── Filesystem
```

Then:

```text
Filesystem
     ↓
Mount
     ↓
Mount Point
     ↓
Directory Tree
```

Example:

```text
/dev/sda2
    ↓
  ext4 filesystem
    ↓
  mounted at
    ↓
  /home
```

---

# 31. A Complete Mental Model

The complete picture is:

```text
                    Physical Disk
                         │
                         ↓
                    Partitions
                         │
                         ↓
                    Filesystems
                         │
                         ↓
                      Mount
                         │
                         ↓
                    Mount Points
                         │
                         ↓
              Linux Filesystem Tree
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
      /home             /var             /data
        │                │                │
      users             logs           application
```

The user interacts mainly with:

```text
/
├── home
├── var
├── data
└── ...
```

while the system administrator manages the relationship between:

```text
devices
partitions
filesystems
mount points
```

---

# 32. Important FHS Directories

| Directory | Purpose                                       |
| --------- | --------------------------------------------- |
| `/`       | Root filesystem / top of hierarchy            |
| `/boot`   | Static files needed for booting               |
| `/dev`    | Device/special files                          |
| `/etc`    | Host-specific system configuration            |
| `/home`   | User home directories                         |
| `/lib`    | Essential shared libraries and kernel modules |
| `/media`  | Mount points for removable media              |
| `/mnt`    | Temporary filesystem mount point              |
| `/opt`    | Add-on application software                   |
| `/root`   | Root user's home directory                    |
| `/run`    | Runtime system data                           |
| `/sbin`   | Essential system administration binaries      |
| `/srv`    | Data for services provided by the system      |
| `/tmp`    | Temporary files                               |
| `/usr`    | Secondary hierarchy                           |
| `/var`    | Variable data                                 |

These purposes follow the FHS hierarchy definitions.

---

# 33. Commands Related to Filesystems

Some useful commands when working with Linux storage include:

```bash
ls
```

List directory contents.

```bash
pwd
```

Show the current working directory.

```bash
mount
```

Display or manage mounted filesystems.

```bash
umount
```

Unmount a filesystem.

```bash
df -h
```

Show filesystem disk-space usage in human-readable form.

```bash
du -sh directory
```

Show the amount of disk space used by a directory.

```bash
lsblk
```

Display block devices and their partition layout.

These commands become especially useful when troubleshooting disks, partitions, mount points, and storage usage.

---

# 34. Practical Example

Imagine a server with:

```text
Disk:
    /dev/sda

Partitions:
    /dev/sda1
    /dev/sda2
    /dev/sda3
```

We could have:

```text
/dev/sda1 → /
/dev/sda2 → /home
/dev/sda3 → /var
```

The filesystem tree appears as:

```text
/
├── boot
├── dev
├── etc
├── home
│   ├── amr
│   └── john
├── usr
└── var
    ├── log
    └── ...
```

The important thing is that:

```text
/home
```

is still a directory from the user's point of view.

But the data underneath it may be stored on a completely different filesystem from `/`.

---

# 35. Why This Matters for Backend and DevOps

This concept becomes very important when working with:

* Linux servers
* Docker
* Databases
* Logs
* Backups
* VPSs
* Cloud servers
* Storage management
* Production troubleshooting

For example, imagine:

```text
/var
```

fills up because an application generates huge logs.

If `/var` is a separate filesystem:

```text
/var → /dev/sda3
```

then the problem is isolated to that filesystem.

Understanding mount points also becomes important when dealing with persistent application data.

For example:

```text
/data
   ↓
separate filesystem
   ↓
database files
```

The application still accesses:

```text
/data/database
```

without needing to know the physical partition layout.

---

# 36. The Most Important Concepts to Remember

### 1. Linux has one main filesystem hierarchy

```text
/
```

Everything is organized under it.

---

### 2. Partitions are not directories

```text
/dev/sda2
```

is a device/partition.

```text
/home
```

is a directory/mount point.

---

### 3. A filesystem can be mounted at a directory

```text
Filesystem
    ↓
  /home
```

---

### 4. Multiple filesystems can appear as one hierarchy

```text
/
├── home  → filesystem A
├── var   → filesystem B
└── data  → filesystem C
```

---

### 5. Partitioning and mounting are different

```text
Partitioning
→ divides storage

Mounting
→ attaches a filesystem to the directory tree
```

---

### 6. `/mnt` and `/media` have specific roles

```text
/mnt
→ temporary filesystem mounts

/media
→ removable media mount points
```

The FHS explicitly defines these roles.

---

### 7. The root filesystem is special

It must contain enough software and data to:

```text
Boot
 ↓
Mount other filesystems
 ↓
Run the system
 ↓
Recover / repair when necessary
```

This is explicitly part of the FHS rationale for the root filesystem.

---

# Final Mental Model

If you remember only one diagram from this topic, remember this:

```text
                    DISK
                     │
              ┌──────┴──────┐
              ↓             ↓
          Partition 1   Partition 2
              │             │
              ↓             ↓
         Filesystem A   Filesystem B
              │             │
              │ mount       │ mount
              ↓             ↓
              /           /home
              │             │
              └──────┬──────┘
                     ↓
            Linux Filesystem Tree
                     │
                     ↓
        ┌────────────┼────────────┐
        ↓            ↓            ↓
       /etc        /home         /var
        │            │             │
   configuration   users          logs
```

And compare that with the simplified Windows mental model:

```text
Windows

C:\
├── Windows
├── Users
└── Program Files

D:\
├── Projects
└── Backup
```

while Linux conceptually provides:

```text
Linux

/
├── etc
├── home
├── usr
├── var
└── data
```

where different filesystems can be mounted at different points in this single hierarchy.

That unified namespace + mount-point model is one of the fundamental ideas that distinguishes the Linux filesystem architecture.
