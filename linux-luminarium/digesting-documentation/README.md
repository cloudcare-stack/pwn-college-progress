# Digesting Documentation

Learning how to find, read, and search Linux documentation to understand how commands and programs should be used.

## Challenges

| # | Challenge                   | Status      |
| - | --------------------------- | ----------- |
| 1 | Learning From Documentation | ✅ Completed |
| 2 | Learning Complex Usage      | ✅ Completed |
| 3 | Reading Manuals             | ✅ Completed |
| 4 | Searching Manuals           | ✅ Completed |
| 5 | Searching For Manuals       | ✅ Completed |
| 6 | Helpful Programs            | ✅ Completed |
| 7 | Help for Builtins           | ✅ Completed |

---

## What I Learned

### 1. Learning From Documentation

**Status:** ✅ Completed

**Concepts:**

* Documentation explains how a program should be used.
* Programs can accept command-line arguments that change their behavior.
* Arguments are placed after the command.
* Reading the provided documentation can reveal the exact argument required by a program.

**Command used:**

```bash
/challenge/challenge --giveflag
```

**Example structure:**

```text
/challenge/challenge --giveflag
        command        argument
```

**Key takeaway:**

Always read the available documentation carefully before guessing how to use a command.

---

### 2. Learning Complex Usage

**Status:** ✅ Completed

**Concepts:**

* Some command-line options require their own arguments.
* The value following an option provides additional information to that option.
* File paths are commonly passed as arguments to command-line options.

**Command used:**

```bash
/challenge/challenge --printfile /flag
```

**Structure:**

```text
/challenge/challenge --printfile /flag
        command          option   value
```

Here:

* `/challenge/challenge` is the program.
* `--printfile` tells the program to print a file.
* `/flag` specifies which file to print.

**Key takeaway:**

An option may require a value:

```text
command OPTION VALUE
```

---

### 3. Reading Manuals

**Status:** ✅ Completed

**Concepts:**

* `man` stands for **manual**.
* Linux manual pages provide documentation about commands.
* A man page can contain descriptions, syntax, options, and examples.
* Challenge-specific documentation can also be placed in the man-page database.

**Command used to read the manual:**

```bash
man challenge
```

The manual revealed the required option:

```text
--sxrinn NUM
    print the flag if NUM is 525
```

**Solution:**

```bash
/challenge/challenge --sxrinn 525
```

**Useful man-page controls:**

```text
↑ / ↓       Scroll
PgUp/PgDn   Move through pages
q           Quit
```

**Key takeaway:**

When you do not know how a command works, check:

```bash
man COMMAND
```

---

### 4. Searching Manuals

**Status:** ✅ Completed

**Concepts:**

* Long man pages do not need to be searched manually by scrolling.
* The `/` command searches forward through the current man page.
* `?` searches backward.
* `n` and `N` move between matching results.

**First command:**

```bash
man challenge
```

Inside the manual, search for useful terms:

```text
/flag
```

The challenge's manual revealed the hidden option.

**Solution used:**

```bash
/challenge/challenge --bbzvply
```

**Man-page searching controls:**

```text
/keyword    Search forward
?keyword    Search backward
n           Next match
N           Previous match
q           Quit
```

**Key takeaway:**

Instead of scrolling through a large manual, search for important words such as:

```text
/flag
/print
/file
/option
```

---

### 5. Searching For Manuals

**Status:** ✅ Completed

**Concepts:**

* Sometimes you do not know the name of the manual page you need.
* `man -k` searches the man-page database using a keyword.
* The challenge randomized the name of its manual page.
* After finding the correct page, it could be opened normally with `man`.

**Search command used:**

```bash
man -k challenge
```

This revealed the randomized manual-page name:

```text
ixvafnfgow (1) - print the flag!
```

Then the manual was opened:

```bash
man ixvafnfgow
```

The manual revealed:

```text
--ixvafn NUM
    print the flag if NUM is 812
```

**Solution:**

```bash
/challenge/challenge --ixvafn 812
```

**Important distinction:**

```bash
man -k keyword
```

searches the **man-page database**.

While inside a man page:

```text
/keyword
```

searches the **contents of the current manual**.

**Key takeaway:**

If you know what documentation you need but do not know its exact man-page name, use:

```bash
man -k KEYWORD
```

---

### 6. Helpful Programs

**Status:** ✅ Completed

**Concepts:**

* Not every program has a man page.
* Many programs provide their own documentation through `--help`.
* Some programs use `-h` as the short form of `--help`.
* Help output often lists available options and explains whether they require values.

**Command used:**

```bash
/challenge/challenge --help
```

The help output showed options including:

```text
-g GIVE_THE_FLAG, --give-the-flag GIVE_THE_FLAG
    get the flag, if given the correct value

-p, --print-value
    print the value that will cause the -g option to give you the flag
```

First, retrieve the required value:

```bash
/challenge/challenge --print-value
```

Then pass that value to:

```bash
/challenge/challenge --give-the-flag VALUE
```

The short options could also be used:

```bash
/challenge/challenge -p
/challenge/challenge -g VALUE
```

**Key takeaway:**

A useful first step when encountering an unfamiliar program is:

```bash
COMMAND --help
```

or sometimes:

```bash
COMMAND -h
```

---

### 7. Help for Builtins

**Status:** ✅ Completed

**Concepts:**

* Not every shell command is a separate executable program.
* Some commands are built directly into the shell.
* These commands are called **shell builtins**.
* Bash provides the `help` builtin for reading documentation about other builtins.
* `man` and `--help` are not always the correct documentation methods for shell builtins.

To view available shell builtins:

```bash
help
```

To get documentation for a particular builtin:

```bash
help COMMAND
```

For this challenge:

```bash
help challenge
```

The documentation revealed the required `--secret` option and its value.

**Solution used:**

```bash
challenge --secret E7e13EPkP
```

**Key takeaway:**

For Bash builtins, use:

```bash
help BUILTIN
```

---

## Documentation Cheat Sheet

When encountering an unfamiliar Linux command, try these methods:

```bash
# Read the program's built-in help
command --help

# Short help option used by some programs
command -h

# Open the command's manual
man command

# Search the man-page database
man -k keyword

# Get documentation for a shell builtin
help command
```

### Searching Inside `man`

```text
/keyword    Search forward
?keyword    Search backward
n           Next result
N           Previous result
q           Quit
```

---

## Summary

The **Digesting Documentation** module taught me that I do not need to memorize every Linux command or option. Instead, I can use the documentation available on the system to determine how unfamiliar programs work.

I learned how to:

* Read provided command documentation.
* Understand options that require additional arguments.
* Read Linux manual pages with `man`.
* Search inside manual pages.
* Search the man-page database with `man -k`.
* Use `--help` to inspect program options.
* Use Bash `help` to learn about shell builtins.

These skills are especially useful in Linux administration, programming, and cybersecurity because unfamiliar commands and tools can often be understood directly from their documentation.
