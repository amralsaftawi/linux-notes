# Shell Expansion

**Shell Expansion** is the process by which the Shell transforms certain expressions into values, filenames, or other text **before executing a command**.

For example:

```bash
echo $HOME
```

The Shell expands `$HOME` into the value of the `HOME` variable before `echo` runs.

Shell expansion is one of the most important concepts for understanding Bash and command-line automation.

---

# 1. Brace Expansion

**Brace Expansion** allows us to generate multiple strings from a single expression.

The syntax is:

```bash
{start..end}
```

## Example

Instead of:

```bash
touch file1 file2 file3 file4 file5 file6 file7 file8 file9 file10
```

we can write:

```bash
touch file{1..10}
```

This produces:

```text
file1
file2
file3
...
file10
```

You can verify them with:

```bash
ls file{1..10}
```

The Shell expands:

```bash
file{1..10}
```

into:

```text
file1 file2 file3 file4 file5 file6 file7 file8 file9 file10
```

before `ls` is executed.

---

## Brace Expansion with Letters

Brace expansion is not limited to numbers.

```bash
echo {a..e}
```

produces:

```text
a b c d e
```

You can also generate strings:

```bash
echo file{A..C}.txt
```

Result:

```text
fileA.txt fileB.txt fileC.txt
```

---

## Multiple Brace Expansions

You can combine multiple brace expansions:

```bash
echo {1..3}{a..c}
```

Result:

```text
1a 1b 1c 2a 2b 2c 3a 3b 3c
```

---

# 2. Filename Expansion / Globbing

**Globbing** (also called **Pathname Expansion**) allows the Shell to match filenames using special wildcard characters.

The most important wildcard characters are:

```text
*       → zero or more characters
?       → exactly one character
[...]   → one character from a specified set/range
```

> **Important:** Globbing is not the same thing as Regular Expressions (Regex). They may look similar, but they are used for different purposes.

---

# 3. `*` — Match Zero or More Characters

The `*` wildcard matches **zero or more characters**.

Suppose we have:

```text
file1
file2
file10
file.txt
fileABC
```

Then:

```bash
ls file*
```

can match all of these because they all start with `file`.

Another example:

```bash
ls *.txt
```

matches files ending with `.txt`.

For example:

```text
notes.txt
config.txt
data.txt
```

### Important

`*` does not simply mean "all".

It means:

> **Match zero or more characters in a filename/path.**

For example:

```bash
ls file*
```

does not necessarily match every file in the directory.

---

# 4. `?` — Match Exactly One Character

The `?` wildcard matches **exactly one character**.

Suppose we have:

```text
file1
file2
file10
fileA
```

Then:

```bash
ls file?
```

matches:

```text
file1
file2
fileA
```

but not:

```text
file10
```

because `file10` has two characters after `file`.

Another example:

```bash
ls ?.txt
```

matches filenames containing exactly one character before `.txt`.

Example:

```text
a.txt
b.txt
```

but not:

```text
abc.txt
```

---

# 5. `[...]` — Character Matching

Square brackets allow us to specify a set or range of characters.

For example:

```bash
ls file[1-3]
```

matches:

```text
file1
file2
file3
```

It does **not** mean "numbers from 1 to 3 anywhere in the filename."

It means:

> Match exactly one character that is either `1`, `2`, or `3`.

---

## Character Sets

You can specify individual characters:

```bash
ls file[123]
```

This matches:

```text
file1
file2
file3
```

You can also combine characters and ranges:

```bash
ls file[1-3a-c]
```

This can match:

```text
file1
file2
file3
filea
fileb
filec
```

---

# 6. Negating a Character Class

You can use `!` as the first character inside `[]` to mean:

> Match any character **except** the specified characters.

Example:

```bash
ls file[!2]
```

This matches files such as:

```text
file1
file3
fileA
```

but not:

```text
file2
```

> **Note:** In Bash, `[^2]` can also be used for negation in a bracket expression.

---

# 7. Brace Expansion vs Globbing

These two concepts are very important to distinguish.

### Brace Expansion

Generates strings.

```bash
echo file{1..3}
```

produces:

```text
file1 file2 file3
```

It does **not require those files to exist**.

For example:

```bash
echo file{1..3}
```

will still print:

```text
file1 file2 file3
```

even if the files don't exist.

---

### Globbing

Matches existing filesystem paths.

```bash
ls file*
```

The `*` is expanded according to filenames that actually exist.

### Comparison

```text
Brace Expansion
→ Generates text

Globbing
→ Matches filesystem paths
```

This distinction is extremely important.

---

# 8. Shell Variables

A **Shell Variable** stores a value that can be used later in the current Shell session.

The basic syntax is:

```bash
variable=value
```

### Important

There must be **no spaces** around `=`.

Correct:

```bash
var1=10
```

Incorrect:

```bash
var1 = 10
```

The second form is interpreted differently by the Shell and will not assign the variable as intended.

---

# 9. Using Variables

To access the value of a variable, use `$` before the variable name:

```bash
echo $var1
```

If:

```bash
var1=10
```

then:

```bash
echo $var1
```

outputs:

```text
10
```

---

## `${variable}` Syntax

You can also use braces:

```bash
echo ${var1}
```

This becomes especially useful when the variable name is next to other characters.

Example:

```bash
name="Amr"

echo "${name}123"
```

Result:

```text
Amr123
```

Without braces, the Shell may interpret the characters after the variable name as part of the variable name.

---

# 10. Variables Can Store Command Output

You can store the output of a command inside a variable using **Command Substitution**:

```bash
var4=$(date)
```

Now:

```bash
echo "$var4"
```

prints the output generated by `date`.

Example:

```bash
current_date=$(date)
echo "$current_date"
```

This is called **Command Substitution**.

The older syntax:

```bash
`date`
```

also exists, but `$(date)` is preferred because it is easier to read and nest.

---

# 11. Common Environment Variables

Linux systems provide many commonly used environment variables.

### `PATH`

```bash
echo $PATH
```

`PATH` contains directories where the Shell searches for executable commands.

For example, when you type:

```bash
ls
```

the Shell searches locations listed in `PATH` to find the executable, unless the command is handled another way (such as an alias or shell builtin).

---

### `HOME`

```bash
echo $HOME
```

Contains the current user's home directory.

Example:

```text
/home/amr
```

---

### `USER`

```bash
echo $USER
```

Usually contains the current username.

---

### `HOSTNAME`

```bash
echo $HOSTNAME
```

Contains the hostname of the machine in typical Bash/Linux environments.

---

### `SHELL`

```bash
echo $SHELL
```

Usually contains the user's default login shell.

Example:

```text
/bin/bash
```

> **Important:** `$SHELL` usually represents the user's configured/login shell. It does not necessarily tell you which shell process is currently running in every situation.

---

# 12. `;` — Separating Commands

The semicolon `;` allows multiple commands to be written on the same line.

Example:

```bash
pwd; ls; whoami
```

The Shell executes the commands sequentially.

You can think of it as:

```text
pwd
 ↓
ls
 ↓
whoami
```

However, `;` does not care whether the previous command succeeded.

For conditional execution, Bash also provides:

```bash
command1 && command2
```

and:

```bash
command1 || command2
```

These will become important when learning Bash scripting.

---

# 13. Viewing Variables

The `set` command can display Shell variables, functions, and other Shell state.

```bash
set
```

However, if your goal is specifically to view **environment variables**, use:

```bash
printenv
```

or:

```bash
env
```

For example:

```bash
printenv PATH
```

---

# 14. Shell Variables vs Environment Variables

This is one of the most important concepts in Shell programming.

Suppose you create:

```bash
myvar="Hello"
```

This is a **Shell Variable**.

It exists in the current Shell.

If the Shell starts another process, that process does not automatically receive every Shell variable.

---

# 15. `export` — Create an Environment Variable

To make a variable available to child processes, use:

```bash
export myvar
```

or:

```bash
export myvar="Hello"
```

Now `myvar` becomes an **Environment Variable** for processes launched from this Shell.

For example:

```bash
myvar="Hello"
export myvar

bash
echo $myvar
```

The new Bash process can access:

```text
Hello
```

---

# 16. Parent and Child Processes

This is easier to understand with a process model:

```text
        Parent Shell
             │
      myvar="Hello"
             │
          export
             │
             ▼
       Child Process
             │
       $myvar = Hello
```

Without `export`:

```text
        Parent Shell
             │
      myvar="Hello"
             │
             ▼
       Child Process
             │
       $myvar not exported
```

The important idea is:

> **Environment variables are inherited by child processes.**

They are not automatically shared with unrelated processes.

---

# 17. Variables and Terminal Sessions

Shell variables normally exist only in the Shell process/session in which they were created.

For example:

```bash
myvar="Hello"
```

If you open another independent terminal session, that new Shell normally will not have this variable.

Similarly, when the Shell process exits, its Shell variables disappear.

---

# 18. Making Variables Persistent

If you want a variable to be available automatically in future Shell sessions, you can define/export it in a Shell startup/configuration file.

For Bash, common files include:

```text
~/.bashrc
~/.profile
```

For example:

```bash
export MY_APP_ENV="development"
```

After starting a new appropriate Bash session, the variable can be available automatically.

> **Important:** Which startup file should be used depends on how the Shell is started (interactive, login, etc.). This distinction becomes important when learning Bash configuration.

---

# 19. `set` vs `env` vs `printenv`

These commands are related but not identical.

### `set`

Shows Shell variables, functions, and other Shell state.

```bash
set
```

### `env`

Displays environment variables and can also run a command with a modified environment.

```bash
env
```

### `printenv`

Specifically displays environment variables.

```bash
printenv
```

You can also inspect a specific variable:

```bash
printenv PATH
```

---

# 20. A Practical Example

Let's combine several concepts:

```bash
name="Amr"
number=10

touch file{1..$number}
```

> **Important:** Brace expansion does not perform normal variable expansion inside `{1..$number}` in the way beginners often expect. In Bash, brace expansion happens before parameter expansion, so this will not generate `file1` through `file10` as intended.

A practical alternative is:

```bash
for i in $(seq 1 "$number"); do
    touch "file$i"
done
```

This example introduces a concept that will become important later:

**Shell scripting and loops.**

---

# Shell Expansion — Quick Review

```text
Brace Expansion
→ {1..10}
→ Generates strings

Globbing / Pathname Expansion
→ *
→ ?
→ [...]
→ Matches filenames/paths

Variables
→ variable=value

Access variable
→ $variable
→ ${variable}

Command Substitution
→ $(command)

Environment Variable
→ export VARIABLE=value

View environment variables
→ env
→ printenv

Shell state
→ set

Command separator
→ ;

Home directory
→ $HOME

Executable search path
→ $PATH
```

## The Most Important Distinction

```text
Brace Expansion
        ↓
   Generate text

Globbing
        ↓
   Match file paths

Variable Expansion
        ↓
   Replace $VARIABLE with its value

Command Substitution
        ↓
   Replace $(command) with command output
```

These are all different forms of **Shell Expansion**.
