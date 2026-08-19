# Shell Variables

Learn how to create, access, export, and use variables in the Linux shell.

## Challenges

| # | Challenge                   | Status      |
| - | --------------------------- | ----------- |
| 1 | Printing Variables          | ✅ Completed |
| 2 | Setting Variables           | ✅ Completed |
| 3 | Multi-word Variables        | ✅ Completed |
| 4 | Exporting Variables         | ✅ Completed |
| 5 | Printing Exported Variables | ✅ Completed |
| 6 | Storing Command Output      | ✅ Completed |
| 7 | Reading Input               | ✅ Completed |
| 8 | Reading Files               | ✅ Completed |

## What I Learned

### 1. Printing Variables

**Status:** ✅ Completed

**Concepts:**

* Shell variables store values.
* `$` is used to access or expand a variable.
* `echo` can print the value stored in a variable.

**Command used:**

```bash
echo $FLAG
```

**Example:**

```bash
NAME="Linux"
echo $NAME
```

Output:

```text
Linux
```

---

### 2. Setting Variables

**Status:** ✅ Completed

**Concepts:**

* Variables are assigned using `=`.
* There must be no spaces around `=`.
* `$` is used to access a variable, not when assigning it.
* Variable names and values are case-sensitive.

**Command used:**

```bash
PWN=COLLEGE
```

**Syntax:**

```bash
VARIABLE=value
```

Correct:

```bash
PWN=COLLEGE
```

Incorrect:

```bash
PWN = COLLEGE
```

---

### 3. Multi-word Variables

**Status:** ✅ Completed

**Concepts:**

* Spaces normally separate words in the shell.
* Quotes allow spaces to be included inside one variable value.
* Multi-word values should be surrounded by quotes.

**Command used:**

```bash
PWN="COLLEGE YEAH"
```

**Example:**

```bash
MESSAGE="Hello World"
echo "$MESSAGE"
```

Output:

```text
Hello World
```

---

### 4. Exporting Variables

**Status:** ✅ Completed

**Concepts:**

* A normal shell variable belongs to the current shell.
* Child processes do not automatically inherit normal shell variables.
* `export` makes a variable available to child processes.
* An exported shell variable becomes part of the environment.

**Commands used:**

```bash
export PWN=COLLEGE
COLLEGE=PWN
/challenge/run
```

`PWN` was exported, so `/challenge/run` could access it.

`COLLEGE` was not exported, so it remained local to the current shell.

**Example:**

```bash
NAME=Linux
export NAME
```

This can also be written as:

```bash
export NAME=Linux
```

---

### 5. Printing Exported Variables

**Status:** ✅ Completed

**Concepts:**

* `env` displays exported environment variables.
* Non-exported shell variables are not displayed by `env`.
* Environment variables are available to child processes.

**Command used:**

```bash
env
```

Example output:

```text
HOME=/home/hacker
PATH=...
FLAG=pwn.college{...}
```

A useful way to search environment variables is:

```bash
env | grep FLAG
```

---

### 6. Storing Command Output

**Status:** ✅ Completed

**Concepts:**

* `$(command)` performs **command substitution**.
* The shell executes the command inside `$()`.
* The command's output can be stored directly in a variable.

**Command used:**

```bash
PWN=$(/challenge/run)
```

**Syntax:**

```bash
VARIABLE=$(command)
```

**Example:**

```bash
USERNAME=$(whoami)
echo "$USERNAME"
```

Command substitution works like this:

```text
command
   ↓
produces output
   ↓
$(command)
   ↓
VARIABLE receives output
```

---

### 7. Reading Input

**Status:** ✅ Completed

**Concepts:**

* `read` is a Bash builtin.
* It reads data from standard input.
* The input is stored inside the specified variable.
* The argument to `read` is the variable name.

**Command used:**

```bash
read PWN
```

Then entered:

```text
COLLEGE
```

This stores `COLLEGE` inside `PWN`.

**Syntax:**

```bash
read VARIABLE
```

**Example:**

```bash
read NAME
Colin
echo "$NAME"
```

Output:

```text
Colin
```

A prompt can also be displayed:

```bash
read -p "Enter your name: " NAME
```

---

### 8. Reading Files

**Status:** ✅ Completed

**Concepts:**

* `read` normally reads from standard input.
* `<` redirects a file into a command's standard input.
* Combining `read` and `<` allows a file's contents to be stored directly in a variable.
* This can avoid an unnecessary use of `cat`.

**Command used:**

```bash
read PWN < /challenge/read_me
```

The data flow is:

```text
/challenge/read_me
        ↓
        <
        ↓
      stdin
        ↓
    read PWN
        ↓
       PWN
```

Instead of:

```bash
VAR=$(cat some_file)
```

a simple single-line file can be read with:

```bash
read VAR < some_file
```

---

## Shell Variables Cheat Sheet

### Set a Variable

```bash
VARIABLE=value
```

### Set a Multi-word Variable

```bash
VARIABLE="multiple words"
```

### Access a Variable

```bash
echo "$VARIABLE"
```

### Export a Variable

```bash
export VARIABLE=value
```

### Export an Existing Variable

```bash
export VARIABLE
```

### Display Exported Variables

```bash
env
```

### Store Command Output

```bash
VARIABLE=$(command)
```

### Read User Input

```bash
read VARIABLE
```

### Read a File into a Variable

```bash
read VARIABLE < file
```

## Key Takeaways

The most important commands and syntax from this module are:

```bash
VARIABLE=value
echo "$VARIABLE"
export VARIABLE
env
VARIABLE=$(command)
read VARIABLE
read VARIABLE < file
```

Shell variables provide a way to store and reuse information in Bash. This module also demonstrates how variables interact with **quoting, environment variables, child processes, standard input, input redirection, and command substitution**.

## Module Progress

**Shell Variables: 8/8 challenges completed ✅**
