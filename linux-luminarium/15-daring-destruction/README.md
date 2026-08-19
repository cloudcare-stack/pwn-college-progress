# Daring Destruction

This module explores destructive Linux operations and demonstrates how processes, disk space, and filesystems can be exhausted or destroyed. It also shows how Bash builtins can remain usable even after important external commands have been deleted.

> **Warning:** The destructive commands in this module were executed inside disposable pwn.college challenge environments. Commands such as fork bombs and `rm -rf --no-preserve-root /` should never be run on a system containing important data.

## Challenges

| # | Challenge                        | Status      |
| - | -------------------------------- | ----------- |
| 1 | The Fork Bomb                    | ✅ Completed |
| 2 | Disk-Space Doomsday              | ✅ Completed |
| 3 | `rm -rf /`                       | ✅ Completed |
| 4 | Life after `rm -rf /`            | ✅ Completed |
| 5 | Finding meaning after `rm -rf /` | ✅ Completed |

---

## What I Learned

### 1. The Fork Bomb

**Status:** ✅ Completed

**Concepts:**

* Linux creates a new process whenever a program is launched.
* A process can create additional child processes.
* Background processes can be launched using `&`.
* A fork bomb repeatedly creates new processes until the system reaches its process limit.
* Process exhaustion can prevent Linux from launching additional programs.
* The error `Resource temporarily unavailable` can indicate that the process limit has been exhausted.

**Example script:**

```bash
#!/bin/bash

/home/hacker/bomb.sh &
/home/hacker/bomb.sh &
wait
```

Make the script executable:

```bash
chmod +x ~/bomb.sh
```

Run the challenge checker first in another terminal:

```bash
/challenge/check
```

Then launch the script:

```bash
/home/hacker/bomb.sh
```

A successful process exhaustion eventually produces errors similar to:

```text
fork: retry: Resource temporarily unavailable
```

**Key takeaway:**

A small recursive script can cause exponential process growth and exhaust the operating system's ability to create new processes.

---

### 2. Disk-Space Doomsday

**Status:** ✅ Completed

**Concepts:**

* The `yes` command continuously produces output.
* Output redirection with `>` can write command output into a file.
* An endlessly growing file can consume all available disk space.
* Full disks can prevent programs from creating new files.
* Removing the large file frees the consumed disk space.

**Command used:**

```bash
yes > ~/junk
```

The file continues growing until the filesystem runs out of space:

```text
No space left on device
```

Verify the disk is full:

```bash
/challenge/check
```

Then remove the large file:

```bash
rm ~/junk
```

Run the checker again:

```bash
/challenge/check
```

**Key takeaway:**

Disk space is a finite system resource. Uncontrolled output redirected to a file can eventually exhaust it.

---

### 3. `rm -rf /`

**Status:** ✅ Completed

**Concepts:**

* `rm` removes files.
* `-r` recursively removes directories and their contents.
* `-f` forces removal without prompting for confirmation.
* `/` represents the root of the filesystem.
* GNU `rm` contains a safety mechanism that normally prevents recursive deletion of `/`.
* `--no-preserve-root` disables that protection.

A normal attempt:

```bash
rm -rf /
```

is blocked with a warning similar to:

```text
rm: it is dangerous to operate recursively on '/'
```

Inside the disposable challenge environment, the protection was explicitly disabled with:

```bash
rm -rf --no-preserve-root /
```

The checker was started before destroying the filesystem:

```bash
/challenge/check
```

**Key takeaway:**

Recursive deletion from `/` can destroy nearly an entire Linux filesystem. Modern `rm` therefore contains safeguards designed to prevent this type of accidental destruction.

---

### 4. Life after `rm -rf /`

**Status:** ✅ Completed

**Concepts:**

* Many common commands such as `cat` are external executable programs.
* Deleting the filesystem can remove those executables.
* Some commands are built directly into Bash.
* An already-running Bash shell can continue executing its builtins even when external programs have disappeared.
* `<` redirects a file into standard input (`stdin`).
* `read` can store input inside a shell variable.

After destroying the filesystem, `cat` was no longer available:

```bash
cat /flag
```

Instead, the flag was read using the Bash builtin `read`:

```bash
read flag < /flag
```

Then displayed with:

```bash
echo "$flag"
```

The data flow is:

```text
/flag
  │
  │ <
  ▼
stdin
  │
  ▼
read flag
  │
  ▼
$flag
```

**Redirection reminder:**

```bash
command < file
```

means:

```text
file → stdin → command
```

while:

```bash
command > file
```

means:

```text
command → stdout → file
```

**Key takeaway:**

Shell builtins are part of the running shell itself and can remain available even when external command executables have been deleted.

---

### 5. Finding meaning after `rm -rf /`

**Status:** ✅ Completed

**Concepts:**

* After destroying the filesystem, `ls` may no longer exist.
* Bash performs filename globbing itself.
* `*` matches filenames.
* `echo /*` can therefore reveal entries in the root directory without using `ls`.
* Input redirection requires a single input file.
* Globbing and Bash builtins can be combined to locate and read a file without external utilities.

The checker created the flag under a random filename.

Since `ls` was unavailable, Bash globbing was used:

```bash
echo /*
```

Example output:

```text
/random_file /dev /etc /home /nix /proc /run /sys /usr
```

The random file could then be read:

```bash
read flag < /random_file
echo "$flag"
```

An important mistake encountered was:

```bash
read flag < /*
```

which resulted in:

```text
ambiguous redirect
```

This happened because `/*` expanded to multiple paths, while `<` requires exactly one file.

The correct approach was:

```bash
echo /*
```

identify the unusual filename, and then:

```bash
read flag < /random_file
echo "$flag"
```

**Key takeaway:**

Bash globbing can substitute for basic file discovery when utilities such as `ls` are unavailable, while `read` and input redirection can substitute for `cat`.

---

## Important Commands

```bash
# Run a process in the background
command &

# Make a script executable
chmod +x script.sh

# Generate continuous output
yes

# Redirect stdout into a file
command > file

# Redirect a file into stdin
command < file

# Remove a file
rm file

# Recursively and forcibly remove files
rm -rf directory

# Disable rm's root-directory safeguard
rm -rf --no-preserve-root /

# Read stdin into a Bash variable
read variable

# Read a file into a Bash variable
read variable < file

# Display a variable
echo "$variable"

# Use Bash globbing to show root-directory entries
echo /*
```

---

## Shell Builtins vs. External Commands

One of the most important lessons from this module was understanding the difference between shell builtins and external programs.

Examples used in these challenges:

| Command | Type             | After filesystem destruction |
| ------- | ---------------- | ---------------------------- |
| `cat`   | External program | ❌ May disappear              |
| `ls`    | External program | ❌ May disappear              |
| `sed`   | External program | ❌ May disappear              |
| `read`  | Bash builtin     | ✅ Still available            |
| `echo`  | Bash builtin     | ✅ Still available            |

Bash also performs operations such as:

```bash
/*
```

filename expansion and:

```bash
< file
```

input redirection itself.

This means an already-running Bash shell can still perform surprisingly useful operations even after many external programs have been deleted.

---

## Module Summary

The **Daring Destruction** module demonstrated several ways Linux systems can become unusable through resource exhaustion or destructive filesystem operations.

The major lessons were:

* A fork bomb can exhaust the available process space.
* Unlimited output can exhaust available disk space.
* Recursive deletion can destroy a Linux filesystem.
* GNU `rm` includes safeguards against accidentally deleting `/`.
* Bash builtins can continue functioning after external executables are deleted.
* `<` redirects a file into standard input.
* `read` can replace `cat` for simple file reading.
* Bash globbing can help discover files when `ls` is unavailable.
* Destructive commands should only be practiced in disposable, isolated environments.

**Module Status:** ✅ **Completed**
