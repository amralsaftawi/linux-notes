# Pipes, Filters & Redirection

One of the most important ideas in Unix/Linux is that programs communicate through **streams of data**.

A program can:

* Receive input
* Produce output
* Produce error messages

These streams can be redirected to files or connected to other programs.

---

# 1. Data Streams

A **data stream** is a continuous flow of data between a program and another component.

In Unix/Linux, standard streams are represented by file descriptors.

There are three standard streams:

| Stream                     | File Descriptor | Purpose                      |
| -------------------------- | --------------: | ---------------------------- |
| Standard Input (`stdin`)   |             `0` | Input to a program           |
| Standard Output (`stdout`) |             `1` | Normal output from a program |
| Standard Error (`stderr`)  |             `2` | Error/diagnostic output      |

---

# 2. Standard Input — `stdin`

`stdin` is the standard input stream of a program.

Its file descriptor is:

```text
0
```

Normally, when you run an interactive command, `stdin` is connected to your terminal.

Conceptually:

```text
Terminal
   │
   │ input
   ▼
stdin (0)
   │
   ▼
Program
```

For example, when you type:

```bash
cat
```

`cat` reads from standard input.

You can type:

```text
Hello
Linux
```

and `cat` will receive that data through `stdin`.

Press:

```text
Ctrl + D
```

to indicate **end-of-file (EOF)** when using terminal input interactively.

---

# 3. Standard Output — `stdout`

`stdout` is the standard output stream of a program.

Its file descriptor is:

```text
1
```

Normally, standard output is displayed in the terminal.

Conceptually:

```text
Program
   │
   │ stdout (1)
   ▼
Terminal
```

Example:

```bash
ls
```

The list of files is normally written to `stdout`, so you see it in the terminal.

---

# 4. Standard Error — `stderr`

`stderr` is used for error messages and diagnostic information.

Its file descriptor is:

```text
2
```

Conceptually:

```text
Program
   ├── stdout (1) ──→ Terminal
   │
   └── stderr (2) ──→ Terminal
```

Both `stdout` and `stderr` normally appear on the terminal, but they are **separate streams**.

This distinction becomes extremely important when using redirection.

---

# 5. Important Correction: Input Is Not "Tokens"

When you type something in the terminal, the Shell reads your input.

For example:

```bash
ls -la /home
```

The Shell parses this command into different components, such as:

```text
ls
-la
/home
```

These are commonly called **words** or **tokens** during parsing.

But the entire command line is not itself called "standard input."

`stdin` is a **stream associated with the process**.

This distinction becomes important when learning Shell parsing and scripting.

---

# 6. Redirection

By default:

```text
stdout → Terminal
stderr → Terminal
stdin  ← Terminal
```

But Linux allows us to **redirect** these streams.

For example, instead of displaying the output in the terminal, we can send it to a file.

---

# 7. Redirect Standard Output — `>`

The `>` operator redirects standard output to a file.

Example:

```bash
ls -l > output
```

Conceptually:

```text
ls
 │
 │ stdout
 ▼
output file
```

Instead of seeing the output in the terminal, it is written to:

```text
output
```

---

# 8. `>` Overwrites the File

If the file already exists:

```bash
ls -l > output
```

will **overwrite its contents**.

For example:

```bash
echo "Hello" > output
echo "Linux" > output
```

The final file contains:

```text
Linux
```

---

# 9. Append with `>>`

The `>>` operator appends output to the end of a file instead of overwriting it.

Example:

```bash
echo "Hello" > output
echo "Linux" >> output
```

The file becomes:

```text
Hello
Linux
```

### Difference

```text
>   → overwrite
>>  → append
```

---

# 10. File Descriptors and Redirection

Because:

```text
stdin  = 0
stdout = 1
stderr = 2
```

we can explicitly specify which stream we want to redirect.

For standard output:

```bash
ls -l 1> output
```

This is equivalent to:

```bash
ls -l > output
```

because `stdout` (`1`) is the default stream for `>`.

For standard error:

```bash
command 2> error
```

This redirects `stderr` to the file:

```text
error
```

---

# 11. Redirecting stdout and stderr Separately

Suppose we run:

```bash
cat /etc/shadow /etc/hosts
```

Depending on the system and permissions:

* Reading `/etc/shadow` may produce an error.
* Reading `/etc/hosts` normally produces content.

These outputs can belong to different streams:

```text
/etc/hosts
    ↓
stdout (1)

Permission error for /etc/shadow
    ↓
stderr (2)
```

If we run:

```bash
cat /etc/shadow /etc/hosts > output
```

only `stdout` is redirected.

So:

```text
stdout → output
stderr → terminal
```

The error message can still appear on the terminal.

---

# 12. Redirect stdout and stderr to Different Files

We can separate them:

```bash
cat /etc/shadow /etc/hosts 1> success 2> error
```

Now:

```text
stdout → success
stderr → error
```

This is useful when you need to keep normal output and errors separate.

---

# 13. Redirect Both stdout and stderr

Sometimes we want both streams in the same file.

A common Bash syntax is:

```bash
command > output 2>&1
```

This means:

```text
stdout → output
stderr → wherever stdout is currently going
```

So both streams end up in `output`.

Bash also provides the shorter form:

```bash
command &> output
```

For example:

```bash
cat /etc/shadow /etc/hosts > output 2>&1
```

---

# 14. `/dev/null`

Linux provides a special device:

```text
/dev/null
```

Anything written to `/dev/null` is discarded.

For example:

```bash
command > /dev/null
```

This discards standard output.

To discard errors:

```bash
command 2> /dev/null
```

To discard both:

```bash
command > /dev/null 2>&1
```

This is commonly useful when you don't care about the output.

---

# 15. Redirect Standard Input — `<`

We can also redirect `stdin` from a file.

Example:

```bash
cat < file.txt
```

Conceptually:

```text
file.txt
   │
   │ stdin (0)
   ▼
  cat
```

This is equivalent in effect to:

```bash
cat file.txt
```

for this particular example.

But the `<` operator becomes more useful when working with commands that expect input from `stdin`.

---

# 16. Pipes — `|`

A **pipe** connects the standard output of one program to the standard input of another program.

Syntax:

```bash
command1 | command2
```

Conceptually:

```text
command1
   │
   │ stdout
   ▼
  PIPE
   │
   │ stdin
   ▼
command2
```

This is one of the fundamental ideas of Unix/Linux.

---

# 17. Example of a Pipe

Suppose we want to search the output of `ls`.

```bash
ls | grep ".txt"
```

The flow is:

```text
ls
 │
 │ stdout
 ▼
pipe
 │
 │ stdin
 ▼
grep
 │
 ▼
Terminal
```

`ls` does not send its output directly to the terminal.

Instead, its `stdout` becomes the `stdin` of `grep`.

---

# 18. Filters

A **filter** is a program that reads input, processes it, and produces output.

Common Linux filters include:

```text
grep
head
tail
sort
uniq
wc
cut
tr
```

Filters are especially powerful because they can be chained together using pipes.

Example:

```bash
command1 | command2 | command3
```

---

# 19. `grep` — Search Text

`grep` searches input for lines matching a pattern.

Example:

```bash
grep "localhost" /etc/hosts
```

This searches `/etc/hosts` and displays lines containing:

```text
localhost
```

---

# 20. Useful `grep` Options

### `-n` — Show Line Numbers

```bash
grep -n "localhost" /etc/hosts
```

Shows the line number of each matching line.

---

### `-c` — Count Matching Lines

```bash
grep -c "localhost" /etc/hosts
```

Displays the number of matching lines.

---

### `-l` — Show Matching File Names

This option is especially useful when searching multiple files.

```bash
grep -l "error" *.log
```

It displays the names of files containing a match.

> **Important:** `-l` does **not** mean "file path" in general. It means **list files with matches**.

---

### `-r` — Recursive Search

Search recursively through directories:

```bash
grep -r "TODO" project/
```

This searches files inside `project/` and its subdirectories.

---

# 21. `head`

`head` displays the beginning of a file or input stream.

Example:

```bash
head file.txt
```

By default, it displays the first **10 lines**.

You can specify the number of lines:

```bash
head -n 5 file.txt
```

This displays the first 5 lines.

---

# 22. `tail`

`tail` displays the end of a file or input stream.

Example:

```bash
tail file.txt
```

By default, it displays the last **10 lines**.

You can specify the number:

```bash
tail -n 5 file.txt
```

This displays the last 5 lines.

---

## `tail -f`

One particularly useful option is:

```bash
tail -f app.log
```

It follows the file and displays new lines as they are added.

This is extremely useful for monitoring logs.

For example:

```bash
tail -f /var/log/some-app.log
```

This concept will become useful later when dealing with **application logs and production debugging**.

---

# 23. `wc` — Word Count / Line Count

`wc` counts lines, words, bytes, and characters.

### Count Lines

```bash
wc -l file.txt
```

### Count Words

```bash
wc -w file.txt
```

### Count Bytes

```bash
wc -c file.txt
```

Example:

```bash
cat file.txt | wc -l
```

This counts the number of lines received through `stdin`.

However, when working with a file directly, this is simpler:

```bash
wc -l file.txt
```

---

# 24. Combining Filters with Pipes

This is where the Unix philosophy becomes powerful.

Example:

```bash
cat file.txt | grep "error" | wc -l
```

The flow is:

```text
file.txt
   │
   ▼
  cat
   │
   │ matching input stream
   ▼
 grep "error"
   │
   ▼
  wc -l
   │
   ▼
 number of matching lines
```

This can be read as:

> Read the file → find lines containing `"error"` → count those lines.

---

# 25. `tr` — Translate or Delete Characters

`tr` is used to **translate, replace, or delete characters** from input.

### Translate Characters

```bash
echo "hello" | tr 'a-z' 'A-Z'
```

Result:

```text
HELLO
```

It translates lowercase letters to uppercase.

---

## Delete Characters — `-d`

```bash
echo "hello123" | tr -d '0-9'
```

Result:

```text
hello
```

The `-d` option means **delete** characters.

---

# 26. `cut` — Extract Parts of Input

`cut` is used to extract portions of lines.

Two useful options are:

```text
-c → characters
-b → bytes
```

### Characters

For example:

```bash
echo "Linux" | cut -c 1-3
```

Result:

```text
Lin
```

### Bytes

```bash
echo "Linux" | cut -b 1-3
```

This extracts bytes 1 through 3.

For ASCII text, bytes and characters usually correspond one-to-one.

> **Important:** With Unicode/multibyte text, `-b` and `-c` can behave differently because a character may occupy multiple bytes.

---

# 27. Pipes vs Redirection

This distinction is extremely important.

### Redirection

Sends a stream to/from a file or another destination.

```bash
ls > output
```

```text
ls
 │
 ▼
file
```

### Pipe

Connects one program to another program.

```bash
ls | grep ".txt"
```

```text
ls
 │
 ▼
grep
```

So:

```text
>   → redirect to a file
<   → read input from a file
|   → connect programs
```

---

# 28. Complete Mental Model

A typical Linux command can be thought of as:

```text
             stdin (0)
                │
                ▼
           ┌─────────┐
           │ Program │
           └────┬────┘
                │
         ┌──────┴──────┐
         │             │
     stdout (1)     stderr (2)
         │             │
         ▼             ▼
      Terminal       Terminal
```

With a pipe:

```text
Program A
   │
   │ stdout
   ▼
  PIPE
   │
   │ stdin
   ▼
Program B
   │
   │ stdout
   ▼
Terminal
```

With redirection:

```text
Program
   │
   │ stdout
   ▼
  File
```

---

# 29. Practical Examples

### Save command output

```bash
ls -la > files.txt
```

### Append output

```bash
ls -la >> files.txt
```

### Save errors

```bash
command 2> errors.txt
```

### Separate output and errors

```bash
command 1> output.txt 2> errors.txt
```

### Discard output

```bash
command > /dev/null
```

### Search output

```bash
ls -la | grep ".txt"
```

### Count matching lines

```bash
cat app.log | grep "ERROR" | wc -l
```

### Show the first 20 lines

```bash
head -n 20 app.log
```

### Show the latest 20 lines

```bash
tail -n 20 app.log
```

### Monitor a log

```bash
tail -f app.log
```

---

# Key Takeaways

```text
stdin  → 0 → standard input
stdout → 1 → standard output
stderr → 2 → standard error
```

```text
>       → redirect stdout (overwrite)
>>      → redirect stdout (append)
1>      → redirect stdout
2>      → redirect stderr
<       → redirect stdin
|       → pipe stdout → stdin
```

Common filters:

```text
grep    → search
head    → beginning of input
tail    → end of input
wc      → count
tr      → translate/delete characters
cut     → extract parts
```

The central Unix/Linux idea is:

```text
┌──────────┐      ┌──────────┐      ┌──────────┐
│ Program  │ ───► │  Filter  │ ───► │  Filter  │
└──────────┘      └──────────┘      └──────────┘
      │                 │                 │
      └─────────────────┴─────────────────┘
                     Pipe
```

> **Unix Philosophy in Practice:** Build small programs that do one thing well, then combine them using pipes, redirection, and standard streams.
