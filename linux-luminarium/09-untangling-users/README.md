# Untangling Users

This module covers Linux users, switching between user accounts, password hashes, password cracking, and executing commands with elevated privileges.

## Challenges

| # | Challenge               | Status      |
| - | ----------------------- | ----------- |
| 1 | Becoming root with `su` | ✅ Completed |
| 2 | Other users with `su`   | ✅ Completed |
| 3 | Cracking Passwords      | ✅ Completed |
| 4 | Using `sudo`            | ✅ Completed |

---

## What I Learned

### 1. Becoming root with `su`

**Status:** ✅ Completed

**Concepts:**

* `su` stands for **substitute user**.
* Running `su` without specifying a username attempts to switch to the `root` user.
* `root` is the administrative/superuser account in Linux.
* `su` authenticates using the password of the user being switched to.
* Password input is hidden in the terminal while typing.
* A root shell commonly uses `#` instead of `$` in the prompt.

**Commands used:**

```bash
su
cat /flag
```

**General syntax:**

```bash
su [username]
```

**Lesson:**

Running `su` and entering the root password starts a shell with root privileges.

---

### 2. Other Users with `su`

**Status:** ✅ Completed

**Concepts:**

* `su` is not limited to switching to `root`.
* A username can be supplied as an argument.
* The target user's password is required for authentication.
* After successful authentication, commands execute as that user.

**Commands used:**

```bash
su zardus
/challenge/run
```

**General syntax:**

```bash
su <username>
```

**Comparison:**

```text
su             → switch to root
su zardus      → switch to zardus
```

**Lesson:**

Linux allows switching between user accounts when the appropriate credentials are available.

---

### 3. Cracking Passwords

**Status:** ✅ Completed

**Concepts:**

* Linux password information was historically stored in `/etc/passwd`.
* Modern Linux systems normally store password hashes in `/etc/shadow`.
* `/etc/shadow` is protected so ordinary users cannot normally read it.
* Passwords are not normally stored as plaintext.
* Instead, Linux stores a cryptographic hash representing the password.
* A leaked shadow file can allow an attacker to perform offline password cracking.
* **John the Ripper** (`john`) can attempt to recover weak passwords from hashes.

I first examined the leaked shadow file:

```bash
cat /challenge/shadow-leak
```

The `zardus` entry contained a password hash rather than the actual password.

I then used John the Ripper:

```bash
john /challenge/shadow-leak
```

To display passwords that John successfully cracked:

```bash
john --show /challenge/shadow-leak
```

After recovering the plaintext password, I switched users:

```bash
su zardus
```

Then ran:

```bash
/challenge/run
```

**Important distinction:**

```text
Password hash      ≠ password
Cracked plaintext  = password used with su
```

**Lesson:**

Protecting `/etc/shadow` is important because obtaining password hashes can allow attackers to attempt offline password cracking.

---

### 4. Using `sudo`

**Status:** ✅ Completed

**Concepts:**

* `sudo` allows an authorized user to execute commands with elevated privileges.
* By default, `sudo` generally executes the specified command as `root`.
* Unlike `su`, `sudo` does not necessarily require switching into a separate root shell.
* Authorization for `sudo` is controlled through system policy, traditionally associated with `/etc/sudoers`.
* Running `sudo` without a command only displays its usage information.

Running the challenge normally did not provide sufficient privileges:

```bash
/challenge/run
```

The challenge required root privileges, so I used:

```bash
sudo /challenge/run
```

I could also read the protected flag using:

```bash
sudo cat /flag
```

**General syntax:**

```bash
sudo <command>
```

For example:

```bash
sudo whoami
```

may produce:

```text
root
```

**Lesson:**

`sudo` is useful when only a particular command needs elevated privileges rather than switching the entire shell to the root account.

---

## `su` vs `sudo`

| Command          | Purpose                                |
| ---------------- | -------------------------------------- |
| `su`             | Switch to the root user                |
| `su <username>`  | Switch to a specific user              |
| `sudo <command>` | Run a command with elevated privileges |
| `sudo cat /flag` | Read `/flag` with root privileges      |

Example:

```bash
su
```

starts a different user shell, while:

```bash
sudo cat /flag
```

runs only `cat /flag` with elevated privileges.

---

## Important Commands

```bash
# Switch to root
su

# Switch to another user
su <username>

# Examine a leaked shadow file
cat /challenge/shadow-leak

# Crack password hashes
john /challenge/shadow-leak

# Display cracked passwords
john --show /challenge/shadow-leak

# Execute a command with elevated privileges
sudo <command>

# Check which user is executing a command
whoami

# Check as root through sudo
sudo whoami

# Read a root-protected file
sudo cat /flag
```

---

## Security Takeaways

* Linux is a multi-user operating system with different privileges for different accounts.
* The `root` account has extremely powerful system privileges.
* Password hashes must be protected even though they are not plaintext passwords.
* Weak passwords may be recoverable if their hashes are leaked.
* `/etc/shadow` protects sensitive authentication information.
* `su` changes the user context of a shell.
* `sudo` provides controlled privilege elevation for individual commands.
* Following the principle of **least privilege** is safer than performing every task as `root`.

## Module Completed

✅ **Untangling Users — 4/4 challenges completed**
