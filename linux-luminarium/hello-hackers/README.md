# Hello Hackers

Introduction to working with commands in the Linux shell.

## Challenges

| # | Challenge | Status |
|---|---|---|
| 1 | Intro to Commands | ✅ Completed |
| 2 | Intro to Arguments | ✅ Completed |
| 3 | Command History | ✅ Completed |

## What I Learned

### 1. Intro to Commands

**Status:** ✅ Completed

**Concepts:**

- A command tells the Linux shell to execute a program or action.
- Pressing Enter invokes the command.
- Linux commands are case-sensitive.
- After a command finishes, the shell displays the prompt again.

**Commands used:**

```bash
whoami
hello
```

**What I learned:**

- `whoami` displays the username of the current user.
- `hello` demonstrated how to invoke a command directly from the shell.
- Linux commands are case-sensitive, so `hello` and `HELLO` are different.

---

### 2. Intro to Arguments

**Status:** ✅ Completed

**Concepts:**

- Arguments are additional data passed to a command.
- The first word entered in the shell is typically the command.
- Words following the command are arguments.
- A command can accept one or multiple arguments.

**Example:**

```bash
echo Hello
```

In this example:

- `echo` is the command.
- `Hello` is the argument.

A command can also have multiple arguments:

```bash
echo Hello Hackers
```

**What I learned:**

Arguments provide additional information to a command and can change what the command does or what data it processes.

---

### 3. Command History

**Status:** ✅ Completed

**Concepts:**

- The shell keeps a history of previously executed commands.
- The ↑ key moves backward through previous commands.
- The ↓ key moves forward through command history.
- Command history saves time when reusing or modifying commands.

**Useful command:**

```bash
history
```

**What I learned:**

Instead of retyping a previous command, I can use the arrow keys to find it and then run or modify it.

The `history` command displays previously executed commands.

Command history can also contain sensitive information, so I should be careful about entering passwords, tokens, flags, or other secrets directly into commands.