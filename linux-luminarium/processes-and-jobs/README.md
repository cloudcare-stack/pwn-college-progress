# Processes and Jobs

This module covers how Linux manages running programs as **processes** and how the shell manages them as **jobs**. It introduces viewing processes, terminating them, suspending and resuming jobs, moving jobs between the foreground and background, starting background processes, and checking process exit codes.

## Challenges

| #  | Challenge                       | Status      |
| -- | ------------------------------- | ----------- |
| 1  | Listing Processes               | ✅ Completed |
| 2  | Killing Processes               | ✅ Completed |
| 3  | Interrupting Processes          | ✅ Completed |
| 4  | Killing Misbehaving Processes   | ✅ Completed |
| 5  | Suspending Processes            | ✅ Completed |
| 6  | Resuming Processes              | ✅ Completed |
| 7  | Backgrounding Processes         | ✅ Completed |
| 8  | Foregrounding Processes         | ✅ Completed |
| 9  | Starting Backgrounded Processes | ✅ Completed |
| 10 | Process Exit Codes              | ✅ Completed |

---

## What I Learned

### 1. Listing Processes

**Status:** ✅ Completed

**Concepts:**

* A **process** is a currently running program.
* `ps` displays running processes.
* Every process has a unique **Process ID (PID)**.
* A process can also have a **Parent Process ID (PPID)** identifying the process that started it.
* `ps -ef` and `ps aux` provide more detailed process information.
* Process listings can reveal the full command or executable being run.
* `ww` can prevent long command lines from being truncated.

**Commands:**

```bash
ps
ps -ef
ps aux
ps -efww
ps auxww
```

**Useful fields:**

* `PID` — Process ID
* `PPID` — Parent Process ID
* `TTY` — associated terminal
* `TIME` — CPU time used
* `CMD` / `COMMAND` — command being executed

**Challenge technique:**

The challenge renamed `/challenge/run` to an unknown filename. Since listing `/challenge` was unavailable, I inspected the running processes to discover the executable's path and then ran it directly.

---

### 2. Killing Processes

**Status:** ✅ Completed

**Concepts:**

* Running processes can be terminated using `kill`.
* `kill` normally requires the process's PID.
* The PID can be found using `ps`.
* PIDs can change each time a program runs, so I should identify the current PID rather than memorize it.

**Commands:**

```bash
ps aux
kill <PID>
```

**Challenge workflow:**

```bash
ps aux
kill <PID-of-dont_run>
/challenge/run
```

I located the `/challenge/dont_run` process, killed it, and then successfully ran `/challenge/run`.

---

### 3. Interrupting Processes

**Status:** ✅ Completed

**Concepts:**

* `Ctrl-C` interrupts the process currently running in the foreground.
* It normally sends the process the `SIGINT` signal.
* This commonly causes the program to terminate and returns control to the shell.

**Example:**

```bash
/challenge/run
```

Then press:

```text
Ctrl-C
```

**Important:**

`Ctrl-C` is useful when a foreground program is waiting, stuck, or running longer than desired.

---

### 4. Killing Misbehaving Processes

**Status:** ✅ Completed

**Concepts:**

* A process can interfere with another process by accessing a shared resource.
* Process listings can be used to identify unwanted or malicious processes.
* Killing the interfering process allows the legitimate process to use the resource.
* Named pipes (FIFOs) may contain buffered data even after the writing process is killed.

**Challenge workflow:**

```bash
ps aux
kill <PID-of-decoy>
/challenge/run
```

The interfering process looked similar to:

```text
/challenge/decoy > /tmp/flag_fifo
```

Another terminal could read the FIFO with:

```bash
cat /tmp/flag_fifo
```

After killing the decoy, `/challenge/run` was able to write the real flag to the FIFO.

---

### 5. Suspending Processes

**Status:** ✅ Completed

**Concepts:**

* A process does not have to be terminated to regain control of the terminal.
* `Ctrl-Z` suspends the current foreground process.
* A suspended process still exists but temporarily stops executing.
* `Ctrl-Z` normally sends `SIGTSTP`.

**Example:**

```bash
/challenge/run
```

Then press:

```text
Ctrl-Z
```

Bash may display:

```text
[1]+  Stopped    /challenge/run
```

The process is now suspended rather than terminated.

---

### 6. Resuming Processes

**Status:** ✅ Completed

**Concepts:**

* `fg` resumes a suspended job in the **foreground**.
* The foreground job interacts directly with the terminal.
* `fg` operates on shell jobs rather than executable paths.

**Workflow:**

```bash
/challenge/run
```

Press:

```text
Ctrl-Z
```

Then:

```bash
fg
```

**Important:**

This is correct:

```bash
fg
```

This is not how `fg` is normally used:

```bash
fg /challenge/run
```

---

### 7. Backgrounding Processes

**Status:** ✅ Completed

**Concepts:**

* `bg` resumes a suspended job in the **background**.
* A background process continues running while the shell prompt remains available.
* A suspended process and a background process are not the same thing.

**Workflow:**

```bash
/challenge/run
```

Press:

```text
Ctrl-Z
```

Then:

```bash
bg
```

The relationship is:

```text
Ctrl-Z
   ↓
Suspended
   ↓
  bg
   ↓
Running in Background
```

This allowed me to start another `/challenge/run` from the same terminal while the first copy continued running.

---

### 8. Foregrounding Processes

**Status:** ✅ Completed

**Concepts:**

* `fg` can also take a process already running in the background and move it into the foreground.
* `bg` and `fg` allow jobs to move between background and foreground execution.

**Workflow:**

```bash
/challenge/run
```

Press:

```text
Ctrl-Z
```

Then:

```bash
bg
fg
```

The complete job-control relationship is:

```text
Foreground
    |
 Ctrl-Z
    ↓
Suspended
    |
   bg
    ↓
Background
    |
   fg
    ↓
Foreground
```

---

### 9. Starting Backgrounded Processes

**Status:** ✅ Completed

**Concepts:**

* A command can be launched directly in the background using `&`.
* This avoids having to start it in the foreground, suspend it, and then use `bg`.
* The shell immediately returns the prompt while the process continues running.

**Syntax:**

```bash
command &
```

**Challenge command:**

```bash
/challenge/run &
```

Bash may display something similar to:

```text
[1] 120
```

Where:

* `[1]` is the shell's **job number**.
* `120` is the process's **PID**.

**Comparison:**

Longer method:

```bash
/challenge/run
# Ctrl-Z
bg
```

Direct method:

```bash
/challenge/run &
```

---

### 10. Process Exit Codes

**Status:** ✅ Completed

**Concepts:**

* Every command returns an **exit code** when it finishes.
* Bash stores the exit code of the most recently completed command in `$?`.
* An exit code of `0` normally indicates success.
* A non-zero exit code normally indicates an error or another special result.
* `$?` should be checked immediately because running another command replaces its value.

**Syntax:**

```bash
command
echo $?
```

**Challenge workflow:**

```bash
/challenge/get-code
echo $?
/challenge/submit-code <exit-code>
```

For this challenge, the returned exit code was:

```text
167
```

So the successful submission was:

```bash
/challenge/submit-code 167
```

**Important distinction:**

This does **not** retrieve the exit code produced by `get-code`:

```bash
/challenge/get-code $?
```

Bash expands `$?` before `/challenge/get-code` executes, so that value belongs to the previous command.

Instead:

```bash
/challenge/get-code
echo $?
```

checks the exit code after the program finishes.

---

## Process and Job Control Cheat Sheet

| Command / Shortcut | Purpose                                         |
| ------------------ | ----------------------------------------------- |
| `ps`               | Show processes associated with the terminal     |
| `ps -ef`           | Show detailed information about all processes   |
| `ps aux`           | Show detailed BSD-style process information     |
| `ps -efww`         | Show processes without truncating long commands |
| `kill <PID>`       | Terminate a process by PID                      |
| `Ctrl-C`           | Interrupt the foreground process                |
| `Ctrl-Z`           | Suspend the foreground process                  |
| `fg`               | Resume/move a job into the foreground           |
| `bg`               | Resume a suspended job in the background        |
| `command &`        | Start a command directly in the background      |
| `echo $?`          | Display the previous command's exit code        |

## Key Differences

### `Ctrl-C` vs. `Ctrl-Z`

```text
Ctrl-C → Interrupt / usually terminate
Ctrl-Z → Suspend / pause
```

### `fg` vs. `bg`

```text
fg → run job in foreground
bg → run job in background
```

### Process vs. Job

A **process** is a running instance of a program managed by Linux.

A **job** is the shell's representation of one or more processes that it manages for foreground/background execution.

### PID vs. Job Number

```text
PID        → identifies a Linux process
Job Number → identifies a job within the current shell
```

## Module Summary

The Processes and Jobs module taught me how to inspect and control programs while they are running in Linux. I learned how to identify processes using `ps`, terminate them using `kill`, interrupt foreground processes with `Ctrl-C`, and suspend them with `Ctrl-Z`.

I also learned Linux shell job control. The `fg` and `bg` commands allow processes to move between foreground and background execution, while `&` allows a process to start directly in the background. Finally, I learned that every completed command returns an exit code and that Bash makes the most recent exit code available through `$?`.

These commands are important for Linux administration, shell scripting, debugging, and cybersecurity because they provide visibility and control over programs executing on a system.
