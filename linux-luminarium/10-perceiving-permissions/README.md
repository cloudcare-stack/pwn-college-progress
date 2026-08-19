# Perceiving Permissions

This module covers Linux file ownership, groups, access permissions, executable permissions, and the SUID bit. These mechanisms determine **who can read, modify, or execute files** on a Linux system.

## Challenges

| # | Challenge                    | Status      |
| - | ---------------------------- | ----------- |
| 1 | Changing File Ownership      | ✅ Completed |
| 2 | Groups and Files             | ✅ Completed |
| 3 | Fun With Groups Names        | ✅ Completed |
| 4 | Changing Permissions         | ✅ Completed |
| 5 | Executable Files             | ✅ Completed |
| 6 | Permission Tweaking Practice | ✅ Completed |
| 7 | Permissions Setting Practice | ✅ Completed |
| 8 | The SUID Bit                 | ✅ Completed |

---

# What I Learned

## 1. Changing File Ownership

**Status:** ✅ Completed

Every Linux file has an owning **user** and **group**.

File ownership can be viewed with:

```bash
ls -l /flag
```

Example:

```text
-r-------- 1 root root 60 /flag
             │    │
             │    └── group
             └─────── owner
```

The `chown` command changes the user that owns a file.

### Syntax

```bash
chown USER FILE
```

### Challenge command

```bash
chown hacker /flag
cat /flag
```

### Key concept

Changing `/flag` from being owned by `root` to being owned by `hacker` allowed the `hacker` user to use the owner's permissions.

---

## 2. Groups and Files

**Status:** ✅ Completed

Linux files also have a **group owner**.

The group can be changed with `chgrp`.

### Syntax

```bash
chgrp GROUP FILE
```

### Challenge command

```bash
chgrp hacker /flag
cat /flag
```

If the `hacker` user belongs to the `hacker` group and the group has read permission, changing `/flag` to that group allows the user to read it.

### Ownership vs. group ownership

```bash
chown hacker file
```

Changes the **user owner**.

```bash
chgrp hacker file
```

Changes the **group owner**.

---

## 3. Fun With Groups Names

**Status:** ✅ Completed

A user's primary group does not necessarily have the same name as the username.

The `id` command displays information about the current user and their groups.

```bash
id
```

Example from the challenge:

```text
uid=1000(hacker) gid=1000(grp19235) groups=1000(grp19235)
```

This showed:

```text
User  = hacker
Group = grp19235
```

The randomized group could then be used with:

```bash
chgrp grp19235 /flag
cat /flag
```

### Key concept

Never assume the username and group name are identical.

Check with:

```bash
id
```

---

## 4. Changing Permissions

**Status:** ✅ Completed

Linux permissions determine what users can do with files.

The three basic permissions are:

| Permission | Symbol | Meaning                |
| ---------- | ------ | ---------------------- |
| Read       | `r`    | Read file contents     |
| Write      | `w`    | Modify file contents   |
| Execute    | `x`    | Execute a file/program |

Permissions are divided into three categories:

| Symbol | Category     |
| ------ | ------------ |
| `u`    | User / owner |
| `g`    | Group        |
| `o`    | Others       |
| `a`    | All          |

For example:

```text
-rwxr-xr--
```

can be divided into:

```text
-  rwx  r-x  r--
   │     │     │
   │     │     └── Others
   │     └──────── Group
   └────────────── Owner
```

The first character describes the file type.

The next nine characters describe permissions.

### Changing permissions

The `chmod` command changes file permissions.

```bash
chmod MODE FILE
```

To **add** a permission:

```bash
chmod o+r /flag
```

This gives other users read access.

To **remove** a permission:

```bash
chmod o-r /flag
```

### Important syntax

```text
+ = add permission
- = remove permission
```

Examples:

```bash
chmod u+r file
chmod g+w file
chmod o+x file
chmod a+r file
```

---

## 5. Executable Files

**Status:** ✅ Completed

A program needs the `x` permission before the appropriate user can execute it.

The challenge file was owned by `hacker`, so the owner's execute permission needed to be enabled.

```bash
chmod u+x /challenge/run
```

Then:

```bash
/challenge/run
```

### Key concept

```text
x = execute
```

For example:

```bash
chmod u+x program
```

means:

> Give the file's owner permission to execute the program.

Other examples:

```bash
chmod g+x program
chmod o+x program
chmod a+x program
```

---

## 6. Permission Tweaking Practice

**Status:** ✅ Completed

This challenge required changing the permissions of:

```text
/challenge/pwn
```

through eight rounds.

Each round displayed:

```text
Current permissions
Needed permissions
```

The goal was to modify the permissions until they exactly matched what the challenge requested.

### Examples

Remove read and write from everyone:

```bash
chmod a-rw /challenge/pwn
```

Add write permission to the group:

```bash
chmod g+w /challenge/pwn
```

Give everyone read, write, and execute:

```bash
chmod a+rwx /challenge/pwn
```

After completing all eight rounds, ownership of `/flag` was changed so that `hacker` could modify its permissions.

The flag was made readable with:

```bash
chmod u+r /flag
cat /flag
```

### Key lesson

Symbolic permissions can be combined:

```text
u+r
g+w
o-x
a+rwx
```

The general format is:

```text
WHO  OPERATOR  PERMISSION
 │       │          │
u/g/o/a  +/-       r/w/x
```

---

## 7. Permissions Setting Practice

**Status:** ✅ Completed

This challenge introduced the `=` operator.

There is an important difference between:

```bash
chmod u+r file
```

and:

```bash
chmod u=r file
```

`+` adds a permission while preserving existing permissions.

`=` replaces the existing permissions with exactly what is specified.

### Example

```bash
chmod u=rw file
```

means:

> Set the owner's permissions to exactly read and write.

If the owner previously had:

```text
rwx
```

it becomes:

```text
rw-
```

because execute permission was not included.

Multiple permission categories can be set simultaneously:

```bash
chmod u=rw,g=r,o= /challenge/pwn
```

This produces:

```text
rw-r-----
```

Breaking it down:

```text
u=rw    owner = read + write
g=r     group = read
o=      others = no permissions
```

### Setting no permissions

An empty value after `=` removes all permissions for that category:

```bash
chmod o= file
```

### Operators

| Operator | Purpose               |
| -------- | --------------------- |
| `+`      | Add permissions       |
| `-`      | Remove permissions    |
| `=`      | Set exact permissions |

After completing the eight rounds, `/flag` was made readable and displayed with:

```bash
chmod u=r /flag
cat /flag
```

---

## 8. The SUID Bit

**Status:** ✅ Completed

SUID stands for:

```text
Set User ID
```

Normally, a program executes using the privileges of the user who launched it.

An executable with SUID can instead execute using the privileges of the **file's owner**.

This is especially important when the executable is owned by:

```text
root
```

The SUID bit can be added with:

```bash
chmod u+s PROGRAM
```

### Challenge command

```bash
chmod u+s /challenge/getroot
```

Then:

```bash
/challenge/getroot
```

Because the executable was owned by `root`, the SUID configuration caused it to run with elevated privileges as intended by the challenge.

The shell changed from the regular `hacker` user to a root shell.

The flag could then be read:

```bash
cat /flag
```

### Recognizing SUID

A normal executable might look like:

```text
-rwxr-xr-x
```

A SUID executable can look like:

```text
-rwsr-xr-x
```

Notice:

```text
rws
  ^
  SUID
```

### Security importance

SUID programs must be designed carefully.

A vulnerable SUID program owned by `root` can potentially provide a path for **privilege escalation**, because the program executes with elevated privileges.

---

# Permission Cheat Sheet

## Permission Types

```text
r = read
w = write
x = execute
```

## Permission Categories

```text
u = user / owner
g = group
o = others
a = all
```

## Permission Operators

```text
+ = add
- = remove
= = set exactly
```

## Common Commands

View permissions:

```bash
ls -l file
```

View current user and groups:

```bash
id
```

Change file owner:

```bash
chown USER FILE
```

Change file group:

```bash
chgrp GROUP FILE
```

Give owner read permission:

```bash
chmod u+r file
```

Give owner execute permission:

```bash
chmod u+x file
```

Give everyone execute permission:

```bash
chmod a+x file
```

Remove all permissions from others:

```bash
chmod o-rwx file
```

Set owner to read/write only:

```bash
chmod u=rw file
```

Set different permissions for owner, group, and others:

```bash
chmod u=rw,g=r,o= file
```

Set SUID:

```bash
chmod u+s program
```

---

# Reading `ls -l`

Example:

```text
-rwxr-xr-- 1 hacker hacker 100 program
```

Breakdown:

```text
- | rwx | r-x | r--
    │      │      │
    │      │      └── Others
    │      └───────── Group
    └──────────────── Owner
```

Therefore:

```text
Owner:  read + write + execute
Group:  read + execute
Others: read
```

---

# Main Takeaways

* Every Linux file has an owning **user** and **group**.
* `chown` changes user ownership.
* `chgrp` changes group ownership.
* `id` displays the current user's UID, GID, and group memberships.
* `ls -l` displays ownership and permissions.
* Linux permissions are divided into **user**, **group**, and **others**.
* `r` means read.
* `w` means write.
* `x` means execute.
* `chmod` modifies file permissions.
* `+` adds permissions.
* `-` removes permissions.
* `=` sets exact permissions.
* Execute permission is required to directly execute a program.
* SUID allows an executable to run with the privileges of its owner.
* Root-owned SUID programs are security-sensitive because vulnerabilities in them can potentially lead to privilege escalation.

## Module Status

**Perceiving Permissions — ✅ Completed**
