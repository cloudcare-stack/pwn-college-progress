# Linux Luminarium — Comprehending Commands

**Status:** ✅ Completed
**Total Challenges Completed:** 14

---

## 1. cat: not the pet, but the command!

**Status:** ✅ Completed

### Concept

The `cat` command reads the contents of a file and prints them to the terminal.

### Syntax

```bash
cat FILE
```

### Challenge

The flag was stored in a file named `flag` in the home directory.

### Command Used

```bash
cat flag
```

### What I Learned

* `cat` displays the contents of files.
* A filename can be passed to a command as an argument.
* `cat` can also display multiple files:

```bash
cat file1 file2
```

---

## 2. catting absolute paths

**Status:** ✅ Completed

### Concept

`cat` can read a file by using its absolute path.

An absolute path starts at the filesystem root `/`.

### Command Used

```bash
cat /flag
```

### What I Learned

* `/flag` is an absolute path.
* `/` represents the root of the Linux filesystem.
* I do not need to be in the same directory as a file if I know its absolute path.

---

## 3. more catting practice

**Status:** ✅ Completed

### Concept

Files located anywhere in the filesystem can be accessed directly with their absolute paths.

The challenge did not allow using `cd`.

### Command Used

```bash
cat /lib/python3.12/concurrent/flag
```

### What I Learned

* I don't need to change directories to access a file.
* Commands can operate directly on files using absolute paths.

For example:

```bash
cat /directory/subdirectory/file
```

---

## 4. grepping for a needle in a haystack

**Status:** ✅ Completed

### Concept

`grep` searches files for lines containing a particular string or pattern.

### Syntax

```bash
grep SEARCH_STRING FILE
```

### First Attempt

```bash
grep flag /challenge/data.txt
```

This produced many matches because many lines contained `flag`.

### Better Search

Since pwn.college flags begin with `pwn.college`, I used:

```bash
grep pwn.college /challenge/data.txt
```

### What I Learned

* `grep` searches through text.
* More specific search strings produce more useful results.
* `grep` is useful for searching large files, logs, configuration files, and command output.

---

## 5. comparing files

**Status:** ✅ Completed

### Concept

`diff` compares two files line by line.

### Syntax

```bash
diff FILE1 FILE2
```

### Command Used

```bash
diff /challenge/decoys_only.txt /challenge/decoys_and_real.txt
```

The first file contained fake flags.

The second contained the same fake flags plus the real flag.

### Example Output

```text
33a34
> pwn.college{...}
```

The `>` indicated something that appeared in the second file but not the first.

### What I Learned

* `diff` finds differences between files.
* It is much faster than manually comparing large files.
* `diff` is useful for source code, configurations, logs, and different versions of files.

---

## 6. listing files

**Status:** ✅ Completed

### Concept

`ls` lists the contents of directories.

### Syntax

```bash
ls
```

List another directory:

```bash
ls /path/to/directory
```

### Command Used

```bash
ls /challenge
```

This revealed the randomly renamed challenge program.

I then executed it using its absolute path:

```bash
/challenge/15917-renamed-run-26901
```

### Important Difference

This:

```bash
cat /challenge/15917-renamed-run-26901
```

displays the program's contents.

This:

```bash
/challenge/15917-renamed-run-26901
```

executes the program.

### What I Learned

* `ls` lets me inspect directories.
* Programs can be executed by specifying their paths.
* Reading a script with `cat` is different from executing it.

---

## 7. touching files

**Status:** ✅ Completed

### Concept

`touch` can create empty files.

### Syntax

```bash
touch FILE
```

Multiple files can be created at once:

```bash
touch FILE1 FILE2
```

### Command Used

```bash
touch /tmp/pwn /tmp/college
/challenge/run
```

### What I Learned

* `touch` creates a file if it doesn't already exist.
* Multiple filenames can be supplied as arguments.
* If a file already exists, `touch` normally updates its timestamps rather than deleting its contents.

---

## 8. removing files

**Status:** ✅ Completed

### Concept

`rm` removes files.

### Syntax

```bash
rm FILE
```

### Commands Used

```bash
ls
rm delete_me
ls
/challenge/check
```

### What I Learned

* `rm` deletes files.
* `ls` can be used before and after `rm` to verify the deletion.
* Linux normally does not move files deleted with `rm` to a Trash or Recycle Bin.

**Be careful when using `rm`.**

---

## 9. moving files

**Status:** ✅ Completed

### Concept

`mv` moves files from one location to another.

### Syntax

```bash
mv SOURCE DESTINATION
```

### Command Used

```bash
mv /flag /tmp/hack-the-planet
/challenge/check
```

### What I Learned

`mv` can both move and rename files.

Move:

```bash
mv file.txt /tmp/
```

Rename:

```bash
mv old.txt new.txt
```

The first argument is the source and the second is the destination.

---

## 10. hidden files

**Status:** ✅ Completed

### Concept

Linux files beginning with `.` are hidden from normal `ls` output.

### Normal Listing

```bash
ls
```

### Show All Files

```bash
ls -a
```

The `-a` option means **all**.

### Commands Used

```bash
cd /
ls -a
cat .flag-46142796310346
```

### What I Learned

Hidden files usually begin with:

```text
.
```

Examples:

```text
.bashrc
.ssh
.config
.git
```

`ls -a` also displays:

```text
.
..
```

Where:

* `.` = current directory
* `..` = parent directory

---

## 11. An Epic Filesystem Quest

**Status:** ✅ Completed

### Concept

This challenge combined several previously learned commands to navigate through the filesystem and follow clues.

### Main Commands Used

```bash
ls
cat CLUE_FILE
cd /next/directory
```

### General Process

```text
Start
  ↓
ls
  ↓
Find clue
  ↓
cat clue
  ↓
Read next location
  ↓
cd or access location directly
  ↓
Repeat
  ↓
Find flag
```

### What I Learned

This challenge reinforced:

* `ls`
* `cat`
* `cd`
* absolute paths
* hidden files
* filesystem navigation
* reading instructions carefully

Some clues specifically warned against entering certain directories, so sometimes the correct approach was to read a file using its path instead of using `cd`.

---

## 12. making directories

**Status:** ✅ Completed

### Concept

`mkdir` creates directories.

### Syntax

```bash
mkdir DIRECTORY
```

### Commands Used

```bash
mkdir /tmp/pwn
touch /tmp/pwn/college
/challenge/run
```

This created:

```text
/tmp/
└── pwn/
    └── college
```

### What I Learned

* `mkdir` creates directories.
* `touch` can create files inside those directories.
* I can use complete paths instead of changing into the directory first.

---

## 13. finding files

**Status:** ✅ Completed

### Concept

`find` searches through directories for files and directories matching specific criteria.

### Basic Syntax

```bash
find LOCATION -name NAME
```

### Command Used

```bash
find / -name flag
```

This searched the entire filesystem starting at `/`.

### Other Examples

Search the current directory:

```bash
find . -name flag
```

Search `/tmp`:

```bash
find /tmp -name flag
```

Search the entire filesystem:

```bash
find / -name flag
```

### Permission Errors

Searching `/` can produce:

```text
Permission denied
```

because a normal user cannot access every directory.

### What I Learned

* `find` recursively searches directories.
* `-name` searches by filename.
* The first path tells `find` where to begin searching.
* Multiple files can have the same name, so I may need to inspect the results.

---

## 14. linking files

**Status:** ✅ Completed

### Concept

Linux supports links between files.

This challenge focused on **symbolic links**, also called **soft links**.

A symbolic link is a special file that points to another file.

### Syntax

```bash
ln -s TARGET LINK_NAME
```

The `-s` option creates a symbolic link.

### Command Used

```bash
ln -s /flag /home/hacker/not-the-flag
```

Then:

```bash
/challenge/catflag
```

The result worked like:

```text
/home/hacker/not-the-flag
          ↓
     symbolic link
          ↓
        /flag
```

The challenge program attempted to read:

```text
/home/hacker/not-the-flag
```

but Linux followed the symbolic link to `/flag`.

### Checking a Symbolic Link

The course slides show that symbolic links can be identified with commands such as:

```bash
ls -l
```

or:

```bash
file LINK_NAME
```

A symbolic link appears with an `l` file type and typically shows its target with `->`.

### Important Symbolic Link Detail

Relative symbolic-link targets are interpreted relative to the directory containing the symbolic link. Using an absolute target can avoid this problem.

### Hard Links

The course slides also introduce hard links:

```bash
ln TARGET LINK_NAME
```

Unlike symbolic links, hard links directly reference the same underlying file data.

---

# Command Cheat Sheet

| Command | Purpose                      | Example                     |
| ------- | ---------------------------- | --------------------------- |
| `cat`   | Display file contents        | `cat file.txt`              |
| `grep`  | Search text                  | `grep pwn.college file.txt` |
| `diff`  | Compare files                | `diff file1 file2`          |
| `ls`    | List directory contents      | `ls /challenge`             |
| `ls -a` | Include hidden files         | `ls -a`                     |
| `touch` | Create an empty file         | `touch file.txt`            |
| `rm`    | Remove a file                | `rm file.txt`               |
| `mv`    | Move or rename a file        | `mv old new`                |
| `mkdir` | Create a directory           | `mkdir test`                |
| `find`  | Search for files/directories | `find / -name flag`         |
| `ln -s` | Create a symbolic link       | `ln -s /target link`        |
| `cd`    | Change directories           | `cd /tmp`                   |

---

# Key Takeaways

After completing **Comprehending Commands**, I can:

* Read files with `cat`.
* Search large files with `grep`.
* Compare files with `diff`.
* List directory contents with `ls`.
* Reveal hidden files with `ls -a`.
* Create files with `touch`.
* Delete files with `rm`.
* Move and rename files with `mv`.
* Create directories with `mkdir`.
* Search the filesystem recursively with `find`.
* Navigate directories with `cd`.
* Work with absolute paths.
* Recognize hidden dotfiles.
* Create symbolic links with `ln -s`.
* Understand the basic difference between symbolic and hard links.
* Combine multiple Linux commands to explore an unfamiliar filesystem and locate information.

**Module Status: ✅ Comprehending Commands Completed**
