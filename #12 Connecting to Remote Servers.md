# Connecting to Remote Servers

One of the most important skills when working with Linux servers is the ability to connect to a remote Linux machine and manage it from your local machine.

For backend developers and DevOps engineers, this is especially important because applications are often deployed to remote:

* VPS servers
* Cloud servers
* Production servers
* Development servers
* Docker hosts

---

# 1. What Does "Remote Server" Mean?

Suppose we have two machines:

```text
Local Machine                         Remote Machine
(My Computer)                         (Linux Server)
     │                                      │
     │                                      │
     └──────────── Network ─────────────────┘
```

Instead of physically sitting in front of the Linux server, we can connect to it remotely.

After connecting:

```text
Your Keyboard
     │
     ↓
Local Terminal
     │
     ↓
    SSH
     │
     ↓
Remote Linux Server
     │
     ↓
Remote Shell
```

When you execute:

```bash
ls
```

after connecting through SSH, the command is executed on the **remote machine**, not on your local machine.

---

# 2. SSH

**SSH** stands for:

> Secure Shell

SSH is a network protocol used to securely access and communicate with remote machines.

The default SSH port is:

```text
22
```

A simplified connection looks like:

```text
┌──────────────────────┐
│   Local Machine      │
│                      │
│  SSH Client          │
└──────────┬───────────┘
           │
           │ SSH
           │ Port 22
           ↓
┌──────────────────────┐
│   Remote Machine     │
│                      │
│  SSH Server          │
│       ↓              │
│      Shell           │
└──────────────────────┘
```

---

# 3. SSH Is a Protocol

SSH is a **protocol**, similar conceptually to protocols such as HTTP.

For example:

```text
HTTP
  ↓
Web server implementation
  ↓
Nginx / Apache / application server
```

Similarly:

```text
SSH
  ↓
SSH server implementation
  ↓
OpenSSH Server
```

SSH itself is the protocol.

**OpenSSH** is a widely used implementation of that protocol.

---

# 4. SSH Client vs SSH Server

SSH communication has two sides:

```text
Client
   │
   │ SSH connection
   ↓
Server
```

### SSH Client

The client starts the connection.

Examples:

```text
Linux
Windows
macOS
```

On Linux, a common SSH client is:

```text
OpenSSH Client
```

---

### SSH Server

The server accepts incoming SSH connections.

On Linux, a common implementation is:

```text
OpenSSH Server
```

The SSH server normally runs an SSH daemon such as:

```text
sshd
```

and listens on a network port, commonly:

```text
22
```

---

# 5. OpenSSH

OpenSSH is a collection of tools implementing the SSH protocol.

Important components include:

```text
OpenSSH Client
    ↓
ssh

OpenSSH Server
    ↓
sshd

File transfer
    ↓
scp / sftp
```

On Debian/Ubuntu systems, the relevant packages commonly include:

```text
openssh-client
openssh-server
```

The exact package names can vary by distribution.

---

# 6. Client and Server Installation

Suppose:

```text
Machine A → Client
Machine B → Server
```

Machine A needs an SSH client.

Machine B needs an SSH server.

```text
Local Linux
┌───────────────────┐
│ openssh-client    │
│                   │
│ ssh               │
└─────────┬─────────┘
          │
          │ SSH
          ↓
┌───────────────────┐
│ openssh-server    │
│                   │
│ sshd              │
└───────────────────┘
Remote Linux
```

You do **not** necessarily need the SSH server package on the client machine.

Likewise, the server doesn't need to be the machine initiating the connection.

---

# 7. Checking the SSH Client

On Debian/Ubuntu:

```bash
dpkg -l | grep openssh
```

You may see:

```text
ii  openssh-client  ...
```

This means the OpenSSH client package is installed.

You can also check:

```bash
ssh -V
```

to display the SSH client version.

---

# 8. Connecting to a Remote Server

The basic syntax is:

```bash
ssh username@hostname
```

or:

```bash
ssh username@ip-address
```

For example:

```bash
ssh amr@192.168.1.10
```

The SSH client attempts to connect to the server and authenticate you.

If password authentication is enabled, you may be asked for the remote user's password.

---

# 9. What Happens After SSH Login?

Suppose you run:

```bash
ssh amr@192.168.1.10
```

The connection is established:

```text
Local Machine
     │
     │ SSH
     ↓
Remote Linux Server
     │
     ↓
   sshd
     │
     ↓
Remote user's shell
```

You might see:

```text
amr@server:~$
```

Now:

```bash
pwd
```

runs on the **remote server**.

Likewise:

```bash
ls
```

and:

```bash
ps
```

operate on the remote machine.

You are effectively interacting with a shell running on the remote server through your local terminal.

---

# 10. Running a Single Remote Command

You don't have to open an interactive SSH session.

You can execute one command remotely:

```bash
ssh amr@192.168.1.10 "ls -la"
```

The SSH client:

```text
Local shell
    │
    ↓
ssh
    │
    ↓
Remote server
    │
    ↓
execute command
    │
    ↓
return output
```

For example:

```bash
ssh amr@192.168.1.10 "df -h"
```

This is extremely useful for automation and DevOps.

---

# 11. SSH Configuration

Typing the full connection information every time can be annoying:

```bash
ssh amr@192.168.1.10
```

SSH allows you to define shortcuts in:

```text
~/.ssh/config
```

Example:

```text
Host AzureLinux
    HostName 192.168.1.10
    Port 22
    User amr
```

Now you can simply run:

```bash
ssh AzureLinux
```

and SSH knows:

```text
Host
    AzureLinux

HostName
    192.168.1.10

Port
    22

User
    amr
```

This becomes very useful when working with multiple servers.

---

# 12. SSH Directory

SSH-related client configuration and keys are commonly stored under:

```text
~/.ssh/
```

For example:

```text
~/.ssh/
├── config
├── known_hosts
├── id_ed25519
└── id_ed25519.pub
```

The exact files present depend on how SSH has been configured.

---

# 13. SSH Host Keys

When connecting to a server for the first time, SSH may show a message asking you to verify the server's identity.

This is related to the server's **host key**.

SSH stores information about previously seen hosts in:

```text
~/.ssh/known_hosts
```

Conceptually:

```text
First connection
       ↓
Server presents host identity
       ↓
SSH asks for confirmation
       ↓
Known host information stored
       ↓
Future connections can detect unexpected changes
```

This helps protect against certain **man-in-the-middle attacks**.

---

# 14. SSH Authentication

SSH supports multiple authentication methods.

One common method is:

```text
Password authentication
```

Another very important method is:

```text
Public-key authentication
```

Public-key authentication is commonly preferred for servers because it avoids repeatedly entering a password and can be configured more securely.

---

# 15. SSH Keys

SSH public-key authentication uses a **key pair**:

```text
Private Key
+
Public Key
```

For example:

```text
id_ed25519
id_ed25519.pub
```

The naming convention is:

```text
id_ed25519
     ↑
private key

id_ed25519.pub
     ↑
public key
```

---

# 16. Private Key vs Public Key

This distinction is extremely important.

### Private Key

The private key must remain secret.

```text
PRIVATE KEY
     │
     └── NEVER share it
```

It stays on your client machine.

### Public Key

The public key can be placed on the server.

```text
PUBLIC KEY
     │
     └── Can be stored on the server
```

Conceptually:

```text
Client
┌───────────────────┐
│ Private Key       │
│ 🔒 Keep Secret    │
│                   │
│ Public Key        │
└─────────┬─────────┘
          │
          │ Public key
          ↓
Server
┌───────────────────┐
│ Authorized Keys   │
└───────────────────┘
```

---

# 17. Generating SSH Keys

Use:

```bash
ssh-keygen
```

A modern example is:

```bash
ssh-keygen -t ed25519
```

This generates a key pair.

Typically:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

The private key:

```text
id_ed25519
```

must remain secret.

The public key:

```text
id_ed25519.pub
```

can be copied to the server.

---

# 18. How Public-Key Authentication Works

Suppose:

```text
Client
       │
       │ Private key
       │
       └──────────────┐
                      ↓
                   SSH Server
```

The server has the client's public key stored in:

```text
~/.ssh/authorized_keys
```

The authentication process is based on **proof that the client possesses the corresponding private key**.

A simplified model:

```text
Client                              Server
  │                                    │
  │────── SSH connection ─────────────>│
  │                                    │
  │<──── authentication challenge ─────│
  │                                    │
  │ Uses private key to prove          │
  │ possession                         │
  │                                    │
  │──────── proof ────────────────────>│
  │                                    │
  │             Server verifies        │
  │             using public key       │
  │                                    │
  │<──────── Authentication OK ────────│
```

The important point is:

> The private key is **not sent to the server**.

The server uses the stored public key to verify that the client possesses the corresponding private key.

---

# 19. `authorized_keys`

On the server, public keys allowed to authenticate are commonly stored in:

```text
~/.ssh/authorized_keys
```

For example:

```text
/home/amr/.ssh/authorized_keys
```

Conceptually:

```text
Server
│
└── /home/amr/
    └── .ssh/
        └── authorized_keys
              │
              ├── Client A public key
              ├── Client B public key
              └── Client C public key
```

If your public key is in this file and SSH server configuration permits public-key authentication, you can authenticate using the corresponding private key.

---

# 20. Copying a Public Key with `ssh-copy-id`

Instead of manually editing:

```text
~/.ssh/authorized_keys
```

you can use:

```bash
ssh-copy-id
```

For example:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub amr@192.168.1.10
```

You will normally need to authenticate to the server, commonly with the user's password, during this initial setup.

The command copies your public key to the server's authorized keys.

Afterward:

```bash
ssh amr@192.168.1.10
```

can authenticate using the key, assuming the server is configured accordingly.

---

# 21. Password vs SSH Key Authentication

### Password

```text
Client
   │
   │ username + password
   ↓
Server
```

### Public Key

```text
Client                         Server
Private Key                    Public Key
     │                              │
     │──── proof of possession ────>│
     │                              │
     └──────── authenticated ───────┘
```

For server administration, public-key authentication is commonly preferred.

---

# 22. Windows → Linux

You can connect from Windows to a Linux server.

Modern Windows versions can include an OpenSSH client.

Other SSH clients are also available, such as:

* PuTTY
* Windows Terminal / PowerShell with OpenSSH

The important thing is that the local machine needs an SSH client, while the remote Linux machine needs an SSH server.

```text
Windows
SSH Client
   │
   │ SSH / Port 22
   ↓
Linux Server
OpenSSH Server
```

---

# 23. Linux → Linux

Connecting from one Linux machine to another is very common:

```text
Linux Client
     │
     │ ssh
     ↓
Linux Server
```

A typical setup is:

```text
Client:
openssh-client

Server:
openssh-server
```

---

# 24. Transferring Files with `scp`

SSH can also be used for secure file transfer.

`scp` stands for:

> Secure Copy

To copy a local file to a remote server:

```bash
scp file.txt amr@192.168.1.10:/home/amr/
```

Conceptually:

```text
Local Machine
     │
     │ file.txt
     │
     ↓
Remote Server
/home/amr/
```

---

# 25. Copying from Server to Client

The direction can also be reversed.

```bash
scp amr@192.168.1.10:/home/amr/file.txt .
```

Here:

```text
Remote Server
     │
     │ file.txt
     ↓
Local Machine
     .
```

The `.` means the current directory on the local machine.

---

# 26. Copying Directories with `scp`

To recursively copy a directory:

```bash
scp -r myfolder amr@192.168.1.10:/home/amr/
```

The `-r` means recursive.

---

# 27. `rsync`

`rsync` is another powerful tool for transferring and synchronizing files.

Example:

```bash
rsync -avz ./project/ amr@192.168.1.10:/home/amr/project/
```

It can synchronize directories efficiently by transferring only the differences when possible.

Conceptually:

```text
Local Project
     │
     │ rsync
     ↓
Remote Project
     │
     └── only required changes are synchronized
```

`rsync` is particularly useful for:

* Server synchronization
* Backups
* Deployment workflows
* Large directories
* Repeated file transfers

It can commonly use SSH as its transport:

```text
rsync
  │
  ↓
SSH
  │
  ↓
Remote Server
```

---

# 28. `wget`

`wget` is primarily a command-line tool for downloading resources from URLs.

Example:

```bash
wget https://example.com/file.zip
```

It is commonly used for:

* Downloading files
* Scripts
* Archives
* Packages
* Resources from HTTP/HTTPS servers

It is different from SSH:

```text
SSH
→ remote machine access

wget
→ download resources
```

---

# 29. `curl`

`curl` is a command-line tool for transferring data using network protocols.

It is extremely common in backend development.

For example:

```bash
curl https://example.com
```

You can also call REST APIs:

```bash
curl https://api.example.com/users
```

For a POST request:

```bash
curl -X POST https://api.example.com/users
```

with data:

```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"name":"Amr"}' \
     https://api.example.com/users
```

This makes `curl` very useful when testing backend APIs from Linux.

---

# 30. `wget` vs `curl`

A simplified comparison:

| Tool    | Common Use                           |
| ------- | ------------------------------------ |
| `wget`  | Download files/resources             |
| `curl`  | Transfer data and interact with APIs |
| `ssh`   | Connect to remote machines           |
| `scp`   | Copy files over SSH                  |
| `rsync` | Synchronize files/directories        |

They can overlap in some capabilities, but these are their common use cases.

---

# 31. Linux Application Distribution

Linux applications can be distributed through different systems.

Common approaches include:

```text
Traditional packages
       │
       ├── .deb
       ├── .rpm
       └── .apk

Other application distribution systems
       │
       ├── Snap
       ├── Flatpak
       └── AppImage
```

These systems solve somewhat different problems.

---

# 32. Snap

**Snap** is an application packaging and distribution system developed by Canonical.

A Snap package can contain an application and much of what it needs to run.

The package format is:

```text
.snap
```

Snap applications are managed using:

```bash
snap
```

---

# 33. Snap Commands

Search for a package:

```bash
snap find <package>
```

Install:

```bash
sudo snap install <package>
```

Show installed snaps:

```bash
snap list
```

Refresh/update installed snaps:

```bash
sudo snap refresh
```

Remove:

```bash
sudo snap remove <package>
```

Show information:

```bash
snap info <package>
```

The general model is:

```text
snap find
    ↓
Find application

snap install
    ↓
Install application

snap refresh
    ↓
Update/refresh application

snap remove
    ↓
Remove application
```

---

# 34. Flatpak

**Flatpak** is another application distribution and sandboxing technology, commonly used for desktop Linux applications.

Flatpak applications are generally distributed through **repositories/remotes**, with Flathub being a major source.

The command-line tool is:

```bash
flatpak
```

Examples:

```bash
flatpak search firefox
```

Install:

```bash
flatpak install flathub org.mozilla.firefox
```

List installed applications:

```bash
flatpak list
```

Update:

```bash
flatpak update
```

Remove:

```bash
flatpak uninstall org.mozilla.firefox
```

---

# 35. AppImage

**AppImage** is another way of distributing Linux desktop applications.

The basic idea is that an AppImage is a **self-contained application image** containing the application and many of its dependencies.

Instead of installing it through a traditional package manager, you typically:

```text
Download AppImage
       ↓
Make it executable
       ↓
Run it
```

For example:

```bash
chmod +x MyApp.AppImage
```

Then:

```bash
./MyApp.AppImage
```

The important idea is:

> An AppImage aims to package an application and its required userspace components into a portable file, rather than installing it as a traditional `.deb` or `.rpm` package.

It is therefore not quite accurate to describe it simply as "a folder containing all packages."

---

# 36. Traditional Package vs Snap vs Flatpak vs AppImage

| Technology | Main Idea                                        |
| ---------- | ------------------------------------------------ |
| `.deb`     | Debian package format                            |
| `.rpm`     | RPM package format                               |
| Snap       | Application package + distribution/update system |
| Flatpak    | Desktop application distribution + sandboxing    |
| AppImage   | Portable/self-contained application image        |

A simplified picture:

```text
Traditional Package
       │
       ├── .deb
       └── .rpm
             │
             ↓
      System Package Manager
```

While:

```text
Snap
  ↓
Snap package + Snap ecosystem
```

```text
Flatpak
  ↓
Flatpak application + remotes
```

```text
AppImage
  ↓
Portable application image
```

---

# 37. SSH Architecture — Full Picture

Putting everything together:

```text
                    Network
                       │
                       │
┌──────────────────────┴──────────────────────┐
│                                             │
│                                             │
▼                                             ▼
LOCAL MACHINE                            REMOTE SERVER
┌───────────────┐                        ┌────────────────┐
│               │                        │                │
│ Terminal      │                        │    sshd        │
│     │         │                        │      │         │
│     ▼         │       SSH              │      ▼         │
│ SSH Client ───┼───────────────────────>│ Remote Shell   │
│               │       Port 22          │                │
└───────────────┘                        └────────────────┘
```

After authentication:

```text
Local Terminal
      │
      │
      │ SSH
      ↓
Remote Shell
      │
      ├── ls
      ├── cd
      ├── ps
      ├── systemctl
      ├── docker
      └── other commands
```

Those commands execute on the remote Linux machine.

---

# 38. SSH Key Authentication — Full Picture

```text
CLIENT                                      SERVER
┌─────────────────────┐                  ┌──────────────────────┐
│                     │                  │                      │
│ Private Key 🔒      │                  │ authorized_keys      │
│ id_ed25519          │                  │                      │
│                     │                  │ Public Key           │
│ Public Key          │                  │                      │
│ id_ed25519.pub ─────┼─────────────────>│                      │
│                     │                  │                      │
└──────────┬──────────┘                  └──────────┬───────────┘
           │                                        │
           │              SSH authentication        │
           └─────────────── proof ──────────────────┘
```

The critical rule:

```text
PRIVATE KEY
    ↓
Never share it.
```

```text
PUBLIC KEY
    ↓
Can be installed on the server.
```

---

# 39. Practical SSH Workflow

A common real-world workflow:

### Step 1 — Generate a key

```bash
ssh-keygen -t ed25519
```

### Step 2 — Copy the public key

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub amr@192.168.1.10
```

### Step 3 — Connect

```bash
ssh amr@192.168.1.10
```

### Step 4 — Optionally create a shortcut

Edit:

```text
~/.ssh/config
```

```text
Host myserver
    HostName 192.168.1.10
    User amr
    Port 22
```

Now:

```bash
ssh myserver
```

---

# 40. Remote Server Workflow

As a backend developer, you may eventually have:

```text
Your PC
   │
   │ SSH
   ↓
VPS
   │
   ├── Linux
   ├── Nginx
   ├── Docker
   ├── PostgreSQL
   └── Your .NET Application
```

You connect:

```bash
ssh myserver
```

Then manage the server:

```bash
docker ps
docker logs myapp
df -h
free -h
ps -ef
systemctl status nginx
```

You can transfer files:

```bash
scp ./app.zip myserver:/home/amr/
```

or synchronize a directory:

```bash
rsync -avz ./app/ myserver:/home/amr/app/
```

And test APIs:

```bash
curl http://localhost:5000/api/health
```

This is the beginning of real Linux server administration and DevOps work.

---

# Quick Review

```text
SSH
→ Secure remote access protocol

Port 22
→ Default SSH port

SSH Client
→ Starts the connection

SSH Server / sshd
→ Accepts SSH connections

ssh
→ Connect to remote machine

ssh host command
→ Execute one command remotely

~/.ssh/config
→ SSH client configuration and host shortcuts

ssh-keygen
→ Generate SSH key pair

Private Key
→ Keep secret

Public Key
→ Can be installed on server

authorized_keys
→ Public keys authorized to log in

ssh-copy-id
→ Copy public key to server

scp
→ Secure file copy

rsync
→ Synchronize files/directories

wget
→ Download resources

curl
→ Transfer data / test HTTP APIs

snap
→ Application packaging/distribution system

flatpak
→ Desktop application distribution/sandboxing system

AppImage
→ Portable/self-contained application image
```

# Core Mental Model

```text
                 REMOTE SERVER
                      │
                    sshd
                      │
                      │
             SSH Authentication
                      │
          ┌───────────┴───────────┐
          │                       │
       Password              Public Key
                                  │
                           Private Key
                                  │
                              Client
```

And for file/data operations:

```text
ssh
→ Remote shell

scp
→ Copy files

rsync
→ Synchronize files

curl
→ Communicate with APIs/services

wget
→ Download resources
```
