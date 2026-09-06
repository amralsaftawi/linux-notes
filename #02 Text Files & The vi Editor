# Text Files & The vi Editor

## 1. The `vi` Editor

`vi` is a terminal-based text editor available on Unix and Linux systems.

It can be used to create, open, edit, and save text files.

To open a file:

```bash
vi filename
```

Example:

```bash
vi notes.txt
```

If the file does not exist, `vi` will create it when you save it.

> **Note:** On many modern Linux systems, `vi` may actually be provided by `vim` (Vi IMproved).

---

# 2. vi Modes

The `vi` editor is **modal**, meaning that different keys perform different operations depending on the current mode.

The two fundamental modes are:

## 2.1 Command Mode (Normal Mode)

This is the default mode when you open a file.

In this mode, you can:

* Navigate through the file
* Delete text
* Copy text
* Paste text
* Search
* Execute editor commands
* Perform other editing operations

Press:

```text
Esc
```

to return to command/normal mode.

> **Important:** In modern `vim` terminology, this is usually called **Normal Mode**.

---

## 2.2 Insert Mode

Insert mode is used to actually type and insert text into the file.

Press:

```text
i
```

to enter Insert Mode.

For example:

```text
i
```

Then type:

```text
Hello Linux
```

Press:

```text
Esc
```

to return to Normal Mode.

### Basic Mode Workflow

```text
Normal Mode
     │
     │ i
     ▼
Insert Mode
     │
     │ Esc
     ▼
Normal Mode
```

> **Important:** Most `vi` commands such as `dd`, `yy`, `dw`, and `p` work in Normal Mode, not Insert Mode.

---

# 3. Navigating in vi

You can use the arrow keys to move around the file.

You can also use the traditional `vi` navigation keys:

```text
h → left
j → down
k → up
l → right
```

A useful way to remember them:

```text
      k
      ↑
h ←       → l
      ↓
      j
```

These commands are executed in Normal Mode.

---

# 4. Deleting Text

## Delete a Word

```text
dw
```

`dw` means **delete word**.

## Delete a Line

```text
dd
```

Deletes the current line.

Example:

```text
dd
```

---

# 5. Copying and Pasting

## Copy a Line

```text
yy
```

Copies the current line.

## Paste

```text
p
```

Pastes the copied/deleted text after the current position.

Example:

```text
yy
p
```

This copies the current line and pastes a copy below it.

> `vi` uses its own internal registers for many copy, delete, and paste operations.

---

# 6. Saving a File

Changes made while editing are not automatically written to the file.

To save the changes:

```text
:w
```

`w` stands for **write**.

The `:` starts an **Ex command**.

Example:

```text
:w
```

---

# 7. Quitting vi

To quit:

```text
:q
```

However, `:q` only works if there are no unsaved changes.

---

# 8. Save and Quit

You can save and quit using:

```text
:wq
```

Another commonly used command is:

```text
ZZ
```

`ZZ` saves the file and exits, when there are changes to save.

---

# 9. Quit Without Saving

To discard the changes and exit:

```text
:q!
```

`!` forces the operation.

Example:

```text
:q!
```

This exits without saving the changes made since the last save.

---

# 10. Useful vi Commands

| Command | Action                |
| ------- | --------------------- |
| `i`     | Enter Insert Mode     |
| `Esc`   | Return to Normal Mode |
| `h`     | Move left             |
| `j`     | Move down             |
| `k`     | Move up               |
| `l`     | Move right            |
| `dw`    | Delete a word         |
| `dd`    | Delete a line         |
| `yy`    | Copy a line           |
| `p`     | Paste                 |
| `:w`    | Save                  |
| `:q`    | Quit                  |
| `:wq`   | Save and quit         |
| `:q!`   | Quit without saving   |

---

# 11. `vi` and the Shell

`vi` provides commands that allow you to interact with the underlying shell.

The general form is:

```text
:!command
```

For example:

```text
:!ls
```

This temporarily runs the `ls` command from inside `vi`.

After the command finishes, you can return to the editor.

> **Important:** `:!` is different from `:.!`.
>
> * `:!command` → execute a shell command.
> * `:.!command` → replace the current line with the output of the command.

Example:

```text
:.!date
```

This executes `date` and replaces the current line with its output.

---

# 12. vi and Buffers

When you edit a file, `vi` keeps the working text in memory while you are editing.

You can think of the editor's buffer as the working area where your changes are made before being written to disk.

For example:

```text
File on Disk
     │
     │ open
     ▼
vi Buffer
     │
     │ edit
     ▼
Modified Buffer
     │
     │ :w
     ▼
File on Disk
```

> **Important:** It is more accurate to say that your changes are made in the editor's in-memory buffer and then written to the file when you use `:w`. The buffer is not simply "all commands stored in memory."

---

# 13. Unix Philosophy

The Unix philosophy is a set of principles that influenced the design of Unix and Linux systems.

A commonly cited formulation consists of **nine major tenets**:

## 13.1 Small Is Beautiful

Prefer small, simple programs over large, complicated ones.

Small programs are generally easier to:

* Understand
* Test
* Maintain
* Reuse

---

## 13.2 Each Program Does One Thing Well

A program should focus on doing one task well instead of trying to do everything.

This allows programs to be combined together.

For example:

```bash
cat file.txt | grep "Linux"
```

Here:

* `cat` reads the file.
* `grep` searches for matching text.

---

## 13.3 Prototype as Soon as Possible

Build a simple working version early.

Instead of spending a long time designing a perfect solution before testing it, create a prototype and learn from the results.

---

## 13.4 Choose Portability Over Efficiency

Prefer solutions that can work across different Unix/Linux environments when practical, rather than relying unnecessarily on system-specific behavior.

Portability can make software easier to reuse and maintain.

---

## 13.5 Store Data in Flat Text Files

Unix systems traditionally favor storing configuration and other information in simple text files.

Advantages include:

* Easy to inspect
* Easy to edit
* Easy to process with standard tools
* Easy to version-control

Examples:

```text
/etc/hosts
/etc/fstab
```

---

## 13.6 Use Software Leverage

Take advantage of existing tools instead of unnecessarily reinventing functionality.

Unix provides many small utilities that can be combined to accomplish more complex tasks.

---

## 13.7 Use Shell Scripts to Increase Leverage and Portability

Automate repetitive tasks using shell scripts.

For example:

```bash
#!/bin/bash

echo "Starting application..."
```

Shell scripts allow multiple commands to be combined into a reusable workflow.

---

## 13.8 Avoid Captive User Interfaces

Programs should not unnecessarily force users into a specialized interface when standard Unix tools and interfaces can accomplish the task.

The Unix philosophy generally favors composable command-line tools.

---

## 13.9 Make Every Program a Filter

Programs should ideally be able to receive input, process it, and produce output so that they can be combined with other programs.

This is one of the ideas behind the Unix pipe:

```bash
command1 | command2
```

Example:

```bash
cat file.txt | grep "error"
```

The output of `cat` becomes the input of `grep`.

---

# Key Takeaways

```text
vi filename   → open/create a file

i             → Insert Mode
Esc           → Normal Mode

h             → left
j             → down
k             → up
l             → right

dw            → delete word
dd            → delete line
yy            → copy line
p             → paste

:w            → save
:q            → quit
:wq           → save + quit
:q!           → quit without saving

:!command     → execute shell command
:.!command    → replace current line with command output
```

## Unix Philosophy — Quick Review

```text
1. Small is Beautiful
2. Each Program Does One Thing Well
3. Prototype as Soon as Possible
4. Choose Portability Over Efficiency
5. Store Data in Flat Text Files
6. Use Software Leverage
7. Use Shell Scripts to Increase Leverage and Portability
8. Avoid Captive User Interfaces
9. Make Every Program a Filter
```

> **Practical mindset:** Linux becomes much easier when you understand how small tools can be combined together rather than trying to memorize hundreds of commands individually.
