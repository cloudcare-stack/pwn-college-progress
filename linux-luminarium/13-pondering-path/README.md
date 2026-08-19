# PATH

This module covers how Linux uses the `PATH` environment variable to locate executable programs. It also demonstrates how modifying `PATH` can change which programs are executed and how insecure `PATH` usage can lead to command hijacking.

## Challenges

| # | Challenge | Status |
|---|---|---|
| 1 | The PATH Variable | ✅ Completed |
| 2 | Setting PATH | ✅ Completed |
| 3 | Adding Commands | ✅ Completed |
| 4 | Hijacking Commands | ✅ Completed |

---

## What I Learned

### 1. The PATH Variable

**Status:** ✅ Completed

**Concepts:**

- `PATH` is an environment variable containing directories where the shell searches for executable programs.
- Directories in `PATH` are separated by colons (`:`).
- When a command such as `ls`, `cat`, or `rm` is entered without an absolute path, the shell searches the directories in `PATH`.
- The search occurs from left to right.
- If a program cannot be found in `PATH`, the shell cannot execute it by command name.

**View the current PATH:**

```bash
echo $PATH
```

**Example PATH:**

```text
/usr/local/bin:/usr/bin:/bin
```

The shell searches:

1. `/usr/local/bin`
2. `/usr/bin`
3. `/bin`

---

### 2. Setting PATH

**Status:** ✅ Completed

**Concepts:**

- The `PATH` variable can be changed like other shell variables.
- Changing `PATH` controls which directories Linux searches for commands.
- Removing standard directories from `PATH` can prevent normal commands from being found.

**Example:**

```bash
PATH=""
```

After clearing `PATH`, commands such as:

```bash
ls
cat
rm
```

may no longer work by name.

However, an executable can still be run using its **absolute path**:

```bash
/bin/ls
/bin/cat
/bin/rm
```

**Important lesson:**

`PATH` affects command lookup, not whether the executable itself exists.

---

### 3. Adding Commands

**Status:** ✅ Completed

**Concepts:**

- Custom executable programs can be placed inside a directory included in `PATH`.
- Once the directory is in `PATH`, the program can be executed by its filename instead of its full path.
- Scripts must have executable permission before they can be executed directly.

**Create a custom command:**

```bash
nano ~/win
```

Example script:

```bash
#!/bin/bash
/challenge/run
```

Make it executable:

```bash
chmod +x ~/win
```

Add the directory containing the command to `PATH`:

```bash
PATH=/home/hacker:$PATH
```

Now the shell can locate:

```bash
win
```

without requiring:

```bash
/home/hacker/win
```

**Important lesson:**

A filename can effectively become a shell command when:

1. The file is executable.
2. Its directory is included in `PATH`.

---

### 4. Hijacking Commands

**Status:** ✅ Completed

**Concepts:**

- Programs sometimes execute other commands by their names instead of their absolute paths.
- Linux searches `PATH` to determine which executable should run.
- If an attacker controls an earlier directory in `PATH`, they can create a malicious executable with the same name as the expected command.
- This technique is called **PATH hijacking** or **command hijacking**.

In the challenge, `/challenge/run` attempted to execute:

```bash
rm
```

Instead of allowing the real `rm` command to execute, a custom `rm` program was created.

Create the fake command:

```bash
nano ~/rm
```

Make it executable:

```bash
chmod +x ~/rm
```

Change `PATH` so the shell finds the custom program:

```bash
PATH=/home/hacker
```

Run the challenge:

```bash
/challenge/run
```

When the challenge attempted to execute:

```bash
rm
```

Linux searched `PATH` and found:

```text
/home/hacker/rm
```

instead of the normal:

```text
/usr/bin/rm
```

The custom command therefore executed in place of the intended `rm`.

---

## How PATH Command Lookup Works

When a command is entered:

```bash
rm /flag
```

the shell does not necessarily know where `rm` is located.

It searches each directory listed in:

```bash
$PATH
```

For example:

```text
/home/hacker:/usr/local/bin:/usr/bin:/bin
```

Linux searches:

```text
/home/hacker/rm
/usr/local/bin/rm
/usr/bin/rm
/bin/rm
```

The **first matching executable** is used.

This means:

```bash
PATH=/home/hacker:/usr/bin:/bin
```

can cause:

```text
/home/hacker/rm
```

to execute before:

```text
/usr/bin/rm
```

---

## Useful Commands

| Command | Purpose |
|---|---|
| `echo $PATH` | Display the current PATH |
| `which command` | Show the executable found for a command |
| `type command` | Show how the shell resolves a command |
| `PATH=...` | Temporarily change PATH |
| `export PATH=...` | Export PATH to child processes |
| `chmod +x file` | Make a file executable |
| `/absolute/path/program` | Run a program without relying on PATH |

---

## Security Takeaways

The `PATH` variable is important not only for convenience but also for system security.

A program that executes:

```bash
rm
```

depends on `PATH` to locate the command.

A program that executes:

```bash
/usr/bin/rm
```

explicitly identifies the intended executable.

This module demonstrated why privileged programs should avoid trusting an unsafe or user-controlled `PATH`.

A malicious directory placed before trusted system directories can potentially cause an unintended program to execute.

---

## Key Takeaways

- `PATH` tells Linux where to search for executable commands.
- PATH directories are searched from **left to right**.
- The first matching executable is normally selected.
- `PATH` can be modified using shell variable assignment.
- Commands can still be executed through absolute paths even when they are not available through `PATH`.
- Custom scripts can become commands when their directory is added to `PATH`.
- `chmod +x` makes a script executable.
- Controlling `PATH` can allow one executable to replace another during command lookup.
- This behavior can create a **PATH hijacking vulnerability**.
- Security-sensitive programs should use trusted paths and avoid relying on attacker-controlled command lookup.

---

## Module Completed

✅ **PATH module completed**

The most important security concept from this module is that executing a command by name implicitly trusts the `PATH` environment variable. If an attacker can influence that variable, they may be able to control which executable actually runs.