# Managing Linux Processes

A **process** is a running instance of a program.

For example, when you execute:

```bash
./app
```

the program starts running, and the operating system creates a **process** to execute it.

A process can run in:

* **Foreground**
* **Background**

---

# 1. What Is a Process?

A process is a running instance of a program together with the resources and execution state needed by the operating system to manage it.

For example:

```bash
sleep 10
```

When this command is running, Linux creates a process for `sleep`.

The operating system keeps information about the process such as:

* Process ID (PID)
* Parent Process ID (PPID)
* Current state
* User that owns the process
* CPU usage
* Memory usage
* Open files and other resources

---

# 2. Program vs Process vs Command

These terms are related but are not the same.

## Program

A program is a set of executable instructions stored on disk.

Example:

```text
/usr/bin/sleep
```

---

## Process

A process is a **running instance** of a program.

For example:

```bash
sleep 100
```

creates a running `sleep` process.

You can even run multiple instances:

```bash
sleep 100 &
sleep 100 &
sleep 100 &
```

Now there are multiple processes running the same program.

---

## Command

A command is what you ask the shell to execute.

For example:

```bash
ls -la
```

The shell interprets this command and may start an external program.

However, not every command corresponds directly to a separate process.

Some commands are **shell builtins**, such as:

```bash
cd
export
echo
```

The shell can execute these internally.

So:

```text
Program
   ↓
can be executed
   ↓
Process
```

while:

```text
Command
   ↓
interpreted by the shell
   ↓
may execute a builtin or start a process
```

---

# 3. Common Sources of Processes

Processes can come from different types of programs.

### 1. Compiled Programs

For example, a C program:

```c
#include <stdio.h>

int main()
{
    while (1) {}
}
```

After compiling and executing it, Linux creates a process for the running program.

---

### 2. Shell Scripts

A shell script can contain commands:

```bash
#!/bin/bash

echo "Hello"
sleep 10
```

When the script is executed, a process is created for the script/interpreter, and commands inside it may create additional processes when they are external programs.

---

### 3. Other Executable Programs

Any executable program can be started as a process.

Examples:

```text
nginx
postgres
python
dotnet
ssh
```

---

# 4. User Processes vs System Processes

Processes can broadly be thought of as:

### User Processes

Processes started for user applications or user tasks.

Examples:

```text
bash
vim
python
dotnet
```

### System Processes

Processes involved in operating and managing the system.

Examples can include:

```text
system services
background daemons
kernel-related processes
```

The exact distinction between "user" and "system" process is contextual; Linux does not have only two technically defined process categories.

---

# 5. PID — Process ID

Every running process has a **Process ID (PID)**.

For example:

```text
PID
2447
```

The PID uniquely identifies that process while it exists.

You can use the PID to inspect or control a process.

For example:

```bash
kill 2447
```

---

# 6. PPID — Parent Process ID

Processes normally have a parent process.

The **PPID** is the Process ID of the process that created/started the current process.

For example:

```text
bash
 │
 ├── ls
 │
 └── sleep
```

If:

```text
bash PID = 1000
sleep PID = 2000
```

then:

```text
sleep
PID  = 2000
PPID = 1000
```

So:

```text
PID
→ Who am I?

PPID
→ Who is my parent?
```

---

# 7. The Process Tree

Linux processes form a hierarchy.

A simplified example:

```text
systemd (PID 1)
│
├── sshd
│   └── bash
│       ├── vim
│       └── python
│
└── other services
```

The parent-child relationship is useful when understanding:

* Process creation
* Process termination
* Services
* Shells
* Containers

---

# 8. PID 1

On a modern Linux system, the first userspace process normally has:

```text
PID = 1
```

On systems using systemd, it is usually:

```text
systemd
```

Historically and on other systems, PID 1 may be another `init` implementation.

Conceptually:

```text
Kernel
   ↓
PID 1
   ↓
Other processes
```

PID 1 has a special role in the system, including participating in the management and reaping of orphaned processes.

---

# 9. Viewing Processes with `ps`

The `ps` command displays information about processes.

Basic:

```bash
ps
```

This normally shows processes associated with the current terminal/session according to the selected `ps` personality/options.

A commonly used command is:

```bash
ps -e
```

which displays all processes.

---

# 10. Useful `ps` Options

## `-e`

Show all processes:

```bash
ps -e
```

---

## `-f`

Show a fuller listing:

```bash
ps -f
```

This provides additional information such as:

```text
UID
PID
PPID
C
STIME
TTY
TIME
CMD
```

---

## `-l`

Show a long-format process listing:

```bash
ps -l
```

It provides additional process information compared with the basic output.

---

## `ps -ef`

A very common command:

```bash
ps -ef
```

It combines:

```text
-e → all processes
-f → full-format listing
```

You will frequently see this command when troubleshooting Linux servers.

Example:

```bash
ps -ef
```

---

# 11. Understanding `ps -ef`

A simplified output may look like:

```text
UID   PID   PPID  C  STIME  TTY   TIME     CMD
root    1      0  0  10:00  ?     00:00:02 systemd
amr  1200   1100  0  10:15  pts/0 00:00:00 bash
amr  1300   1200  0  10:20  pts/0 00:00:00 sleep 100
```

Important columns:

```text
UID
→ user that owns the process

PID
→ process ID

PPID
→ parent process ID

TTY
→ terminal associated with the process

CMD
→ command used to start the process
```

---

# 12. `pstree`

The `pstree` command displays processes as a tree.

Example:

```bash
pstree
```

Conceptually:

```text
systemd
├─sshd
│ └─bash
│   ├─vim
│   └─python
└─other-service
```

This is useful when you want to understand:

```text
Who started this process?
```

and:

```text
What processes were started by this process?
```

---

# 13. `ps` vs `top`

`ps` gives you a **snapshot** of processes.

For example:

```bash
ps -ef
```

runs, prints the current process information, and exits.

It does not continuously update.

For continuously monitoring processes, use:

```bash
top
```

---

# 14. `top`

`top` provides a live, continuously updating view of processes.

It can show information such as:

* CPU usage
* Memory usage
* PID
* User
* Process state
* Running processes

You can use it similarly to Task Manager on Windows for basic process monitoring.

To quit:

```text
q
```

---

# 15. `htop`

`htop` is an interactive process viewer and is often easier to read than `top`.

Example:

```bash
htop
```

It provides an interactive view of:

* Processes
* CPU usage
* Memory usage
* Process hierarchy
* Process IDs

To exit:

```text
q
```

`htop` may not be installed by default on every Linux distribution.

---

# 16. Foreground Processes

When you execute:

```bash
sleep 10
```

the process normally runs in the **foreground**.

Your shell waits for the command to finish.

You cannot normally use that same shell to enter another command until it finishes.

Example:

```text
$ sleep 10
```

The shell waits.

After 10 seconds:

```text
$
```

appears again.

---

# 17. Background Processes

You can start a command in the background by adding:

```bash
&
```

Example:

```bash
sleep 10 &
```

You may see:

```text
[1] 2447
```

Here:

```text
[1]    → shell Job ID
2447   → process PID
```

These are **not the same thing**.

The Job ID is used by the shell's job-control system.

The PID identifies the actual Linux process.

---

# 18. Why Run a Process in the Background?

Suppose you execute:

```bash
sleep 100
```

Your current shell is occupied waiting for it.

Instead:

```bash
sleep 100 &
```

the process runs in the background and your shell becomes available immediately.

You can continue:

```bash
ls
pwd
echo "Hello"
```

without opening another terminal.

This is one form of multitasking.

---

# 19. `jobs`

The `jobs` command shows jobs managed by the current shell.

Example:

```bash
sleep 100 &
sleep 200 &
```

Then:

```bash
jobs
```

may show:

```text
[1]-  Running    sleep 100 &
[2]+  Running    sleep 200 &
```

These numbers:

```text
[1]
[2]
```

are **Job IDs**, not PIDs.

---

# 20. `fg`

If a process is running in the background and you want to bring it back to the foreground:

```bash
fg
```

or specify a job:

```bash
fg %1
```

For example:

```bash
sleep 100 &
```

Then:

```bash
fg %1
```

brings job 1 back to the foreground.

---

# 21. `Ctrl+C`

When a foreground process is running, pressing:

```text
Ctrl+C
```

normally causes the terminal to send:

```text
SIGINT
```

to the foreground process group.

It is commonly used to interrupt a running command.

For example:

```bash
sleep 100
```

Press:

```text
Ctrl+C
```

and the command normally terminates.

Important:

```text
Ctrl+C
```

does not literally mean "kill the process."

It causes a `SIGINT` signal to be sent, and the process decides how to handle that signal.

---

# 22. Process Signals

Linux uses **signals** to notify processes that an event or request has occurred.

You can send a signal using:

```bash
kill
```

For example:

```bash
kill PID
```

By default:

```text
kill PID
```

sends:

```text
SIGTERM
```

---

# 23. `kill` Does Not Always Mean "Kill Immediately"

The name `kill` can be misleading.

The command:

```bash
kill
```

actually means:

```text
send a signal to a process
```

For example:

```bash
kill -TERM 2447
```

means:

```text
Send SIGTERM to PID 2447.
```

---

# 24. Common Signals

Some important signals are:

```text
SIGHUP
SIGINT
SIGTERM
SIGKILL
```

---

# 25. SIGHUP

```text
SIGHUP
```

Historically means **Hang Up**.

It is commonly associated with a terminal/session ending.

Many daemon/service programs also use SIGHUP as a request to:

```text
reload configuration
```

However, this behavior is application-specific.

Example:

```bash
kill -HUP 2447
```

or:

```bash
kill -1 2447
```

---

# 26. SIGINT

```text
SIGINT
```

means **Interrupt**.

It is commonly generated by:

```text
Ctrl+C
```

Example:

```bash
sleep 100
```

Press:

```text
Ctrl+C
```

The foreground process normally receives:

```text
SIGINT
```

A program can handle SIGINT and perform cleanup before exiting.

---

# 27. SIGTERM

```text
SIGTERM
```

means **Terminate**.

It is the standard, polite request to terminate a process.

Example:

```bash
kill 2447
```

is normally equivalent to:

```bash
kill -TERM 2447
```

The process can catch SIGTERM and perform cleanup.

For example, a server may:

```text
receive SIGTERM
      ↓
stop accepting new requests
      ↓
finish important work
      ↓
close resources
      ↓
exit
```

This is why SIGTERM is normally preferred before using SIGKILL.

---

# 28. SIGKILL

```text
SIGKILL
```

forces the kernel to terminate the process.

Example:

```bash
kill -KILL 2447
```

or:

```bash
kill -9 2447
```

Unlike SIGTERM:

```text
SIGKILL cannot be caught or ignored by the process.
```

Therefore the process does not get an opportunity to perform normal cleanup.

Use it when a process refuses to terminate normally or is otherwise stuck.

A good general sequence is:

```text
SIGTERM
   ↓
wait
   ↓
if necessary
   ↓
SIGKILL
```

Do not use `kill -9` as your default way of stopping applications.

---

# 29. Signal Comparison

| Signal    | Common Meaning                       | Can Process Handle It? |
| --------- | ------------------------------------ | ---------------------- |
| `SIGHUP`  | Hangup / application-specific reload | Yes                    |
| `SIGINT`  | Interrupt                            | Yes                    |
| `SIGTERM` | Request graceful termination         | Yes                    |
| `SIGKILL` | Force termination                    | No                     |

---

# 30. Killing a Process

Suppose:

```text
PID = 2447
```

First try:

```bash
kill 2447
```

This sends:

```text
SIGTERM
```

If the process does not terminate and you have a valid reason to force it:

```bash
kill -9 2447
```

which sends:

```text
SIGKILL
```

You can also use signal names explicitly:

```bash
kill -TERM 2447
kill -KILL 2447
```

---

# 31. Finding a Process

You can combine process commands with filters.

For example:

```bash
ps -ef | grep nginx
```

This takes:

```text
ps -ef
```

and sends its output through a pipe to:

```text
grep nginx
```

This is a practical example of the Unix philosophy:

```text
small programs
      +
pipes
      +
filters
```

You can also use tools such as:

```bash
pgrep nginx
```

to find PIDs matching a process name.

---

# 32. Daemons

A **daemon** is a background process that provides a service or performs a system/application task, usually without direct interaction with a user terminal.

Examples include:

```text
sshd
cron
system services
web servers
database servers
```

The naming convention is why many daemon processes historically have names ending with:

```text
d
```

For example:

```text
sshd
```

However:

> A process ending in `d` is not automatically a daemon, and a background process does not have to end in `d`.

So:

```text
background process
≠
daemon
```

A daemon is a particular type of long-running background service/process.

---

# 33. Process Tree and Shells

When you execute commands from a shell, the shell is often the parent of processes it starts.

For example:

```text
bash
│
├── sleep
├── python
└── ./app
```

This parent-child relationship is important when troubleshooting processes.

You can inspect it with:

```bash
pstree
```

or:

```bash
ps -ef
```

---

# 34. Processes Inside Containers

A useful concept when working with Docker:

Inside a container, the main application process is commonly:

```text
PID 1
```

For example:

```text
Container
│
└── PID 1
    └── bash
```

if the container was started with Bash as its main process.

Or:

```text
Container
│
└── PID 1
    └── dotnet MyApp.dll
```

if the container runs a .NET application directly.

This is important because PID 1 has special responsibilities in Linux, especially around signal handling and reaping child processes.

Therefore, in containers:

```text
What is PID 1?
```

is an important troubleshooting question.

---

# 35. Process State

Processes can exist in different states.

You may see states such as:

```text
R → Running / Runnable
S → Interruptible sleep
D → Uninterruptible sleep
T → Stopped
Z → Zombie
```

For example, `ps` may show a process state in its output.

These states become especially useful when diagnosing processes that are:

* Consuming too much CPU
* Sleeping
* Stuck waiting for I/O
* Stopped
* Zombie processes

This topic becomes more important when studying advanced process management.

---

# 36. Useful Commands

| Command       | Purpose                                  |
| ------------- | ---------------------------------------- |
| `ps`          | Show a snapshot of processes             |
| `ps -e`       | Show all processes                       |
| `ps -ef`      | Show all processes in full format        |
| `pstree`      | Show process hierarchy                   |
| `top`         | Live process monitoring                  |
| `htop`        | Interactive live process monitoring      |
| `jobs`        | Show shell-managed background jobs       |
| `fg`          | Bring a job to the foreground            |
| `bg`          | Continue a stopped job in the background |
| `kill PID`    | Send SIGTERM by default                  |
| `kill -9 PID` | Send SIGKILL                             |
| `pgrep name`  | Find process IDs by name                 |

---

# 37. Process vs Job

This distinction is very important.

A **process** is an operating-system concept.

A **job** is mainly a shell job-control concept.

For example:

```bash
sleep 100 &
```

may produce:

```text
[1] 2447
```

Here:

```text
1
→ Job ID

2447
→ PID
```

The shell uses the job ID:

```bash
fg %1
```

while Linux process management uses the PID:

```bash
kill 2447
```

So:

```text
Job ID
→ shell's job control

PID
→ operating system's process identification
```

---

# 38. Foreground vs Background

```text
Foreground
    ↓
Shell waits for the command
    ↓
You normally interact with that process
```

while:

```text
Background
    ↓
Shell remains available
    ↓
Process continues running
```

Example:

```bash
sleep 10
```

Foreground.

```bash
sleep 10 &
```

Background.

---

# 39. A Practical Example

Start a process:

```bash
sleep 100 &
```

You might get:

```text
[1] 2447
```

Now:

```bash
jobs
```

shows the shell job.

Find the process:

```bash
ps -ef | grep sleep
```

or:

```bash
pgrep sleep
```

You can inspect the process:

```bash
ps -p 2447 -f
```

Bring the job to the foreground:

```bash
fg %1
```

Then interrupt it with:

```text
Ctrl+C
```

Alternatively, terminate it using:

```bash
kill 2447
```

The normal sequence is:

```text
Start
  ↓
Process
  ↓
PID
  ↓
Monitor
  ↓
SIGTERM
  ↓
Graceful termination
```

---

# 40. Process Management Mental Model

The overall picture:

```text
                    Linux System
                         │
                         ↓
                    PID 1
                         │
             ┌───────────┴───────────┐
             ↓                       ↓
           Service                  Shell
                                     │
                         ┌───────────┼───────────┐
                         ↓           ↓           ↓
                       process     process     process
                         │
                        PID
                         │
                        PPID
```

To inspect processes:

```text
ps
pstree
top
htop
```

To control them:

```text
kill
```

To control shell jobs:

```text
jobs
fg
bg
```

---

# 41. Final Review

### Process

```text
Running instance of a program.
```

### PID

```text
Process ID
```

### PPID

```text
Parent Process ID
```

### `ps`

```text
Process snapshot
```

### `top` / `htop`

```text
Live process monitoring
```

### `pstree`

```text
Process hierarchy
```

### `&`

```text
Run a command in the background
```

### `jobs`

```text
Show shell-managed jobs
```

### `fg`

```text
Bring a background job to the foreground
```

### `kill`

```text
Send a signal to a process
```

### SIGTERM

```text
Request graceful termination
```

### SIGKILL

```text
Force termination; cannot be caught or ignored
```

### Daemon

```text
Long-running background service/process
```

---

# Core Mental Model

Remember these relationships:

```text
Program
   ↓
Command
   ↓
Shell interprets command
   ↓
Process is started
   ↓
PID identifies process
   ↓
PPID identifies its parent
   ↓
Process becomes part of process tree
```

And for controlling a process:

```text
Process
   │
   ├── inspect → ps / top / htop
   │
   ├── hierarchy → pstree
   │
   ├── shell job → jobs / fg / bg
   │
   └── control → signals → kill
```

The most important idea is:

> **A command is what you ask the shell to execute; a process is the running instance that the operating system manages.**
