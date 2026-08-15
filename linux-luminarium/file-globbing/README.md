# File Globbing

File globbing allows the Bash shell to match filenames and paths using wildcard patterns. It is useful when working with groups of files without typing every filename individually.

## Challenge Progress

| #  | Challenge                           | Status      |
| -- | ----------------------------------- | ----------- |
| 1  | Matching with `*`                   | ✅ Completed |
| 2  | Matching with `?`                   | ✅ Completed |
| 3  | Matching with `[]`                  | ✅ Completed |
| 4  | Matching paths with `[]`            | ✅ Completed |
| 5  | Multiple globs                      | ✅ Completed |
| 6  | Mixing globs                        | ✅ Completed |
| 7  | Exclusionary globbing               | ✅ Completed |
| 8  | Tab completion                      | ✅ Completed |
| 9  | Multiple options for tab completion | ✅ Completed |
| 10 | Tab completion on commands          | ✅ Completed |

---

## 1. Matching with `*`

**Concepts:**

* `*` is a wildcard.
* It matches **zero or more characters**.
* Bash expands the glob before running the command.
* `*` normally does not cross `/` directory separators.
* It also does not normally match a leading `.` in hidden filenames.

**Challenge command:**

```bash
cd /c*
/challenge/run
```

The pattern:

```text
/c*
```

matches:

```text
/challenge
```

Bash effectively executes:

```bash
cd /challenge
```

**General examples:**

```bash
ls *.txt
ls file_*
ls /challenge/*
```

---

## 2. Matching with `?`

**Concepts:**

* `?` matches **exactly one character**.
* Unlike `*`, it cannot represent multiple characters.

**Challenge command:**

```bash
cd /?ha??enge
/challenge/run
```

The pattern:

```text
/?ha??enge
```

matches:

```text
/challenge
```

Each `?` represents exactly one character.

**Example:**

```bash
file_?
```

could match:

```text
file_a
file_b
file_1
```

but not:

```text
file_abc
```

---

## 3. Matching with `[]`

**Concepts:**

Square brackets allow us to specify which character may appear at a particular position.

For example:

```bash
file_[abc]
```

matches:

```text
file_a
file_b
file_c
```

`[abc]` means:

> Match exactly one character that is `a`, `b`, or `c`.

It does **not** mean "match the word `abc`."

**Challenge commands:**

```bash
cd /challenge/files
/challenge/run file_[bash]
```

The glob:

```text
file_[bash]
```

matches:

```text
file_b
file_a
file_s
file_h
```

---

## 4. Matching Paths with `[]`

Globbing can be used anywhere inside a path.

You do not have to `cd` into a directory before globbing its files.

**Challenge command:**

```bash
/challenge/run /challenge/files/file_[bash]
```

Bash expands the pattern into matching absolute paths such as:

```text
/challenge/files/file_a
/challenge/files/file_b
/challenge/files/file_h
/challenge/files/file_s
```

**Key lesson:**

Glob patterns can be part of:

```text
directory/filename
```

or even complete absolute paths.

---

## 5. Multiple Globs

A single word can contain multiple wildcard characters.

**Challenge commands:**

```bash
cd /challenge/files
/challenge/run *p*
```

The pattern:

```text
*p*
```

means:

```text
*    anything before
p    must contain p
*    anything after
```

Therefore, it matches filenames containing `p` anywhere in their name.

Examples include:

```text
happy
optimistic
pwning
splendid
uplifting
```

**Useful pattern:**

```bash
*word*
```

means:

> Match anything containing `word`.

---

## 6. Mixing Globs

Different glob types can be combined.

The challenge required matching only:

```text
challenging
educational
pwning
```

Their first letters are:

```text
c
e
p
```

**Challenge command:**

```bash
/challenge/run [cep]*
```

Breakdown:

```text
[cep]    first character must be c, e, or p
*        anything may follow
```

This combines a bracket glob with `*`.

### Important Lesson

A pattern such as:

```bash
[ing]
```

does **not** mean the string:

```text
ing
```

It means:

> One character that is `i`, `n`, or `g`.

---

## 7. Exclusionary Globbing

Bracket globs can also specify characters that should **not** match.

Traditional syntax:

```bash
[!abc]
```

Newer Bash also supports:

```bash
[^abc]
```

These mean:

> Match one character that is NOT `a`, `b`, or `c`.

The challenge required every file that did **not** start with:

```text
p
w
n
```

**Challenge command:**

```bash
/challenge/run [!pwn]*
```

Breakdown:

```text
[!pwn]    first character cannot be p, w, or n
*         anything may follow
```

### Position Matters

This:

```bash
[!pwn]*
```

tests the **first character**.

This:

```bash
*[!pwn]
```

tests the **last character**.

---

## 8. Tab Completion

Tab completion lets Bash automatically complete filenames and paths.

Instead of relying on a broad wildcard such as:

```bash
*
```

you can begin typing the specific filename and press **Tab**.

**Challenge technique:**

```text
cat /challenge/pwn<TAB>
```

Bash completed the unusual filename, allowing the file containing the flag to be read.

### Why Tab Completion Is Useful

It can:

* reduce typing
* avoid spelling mistakes
* complete long filenames
* complete unusual filenames
* help avoid overly broad glob patterns

---

## 9. Multiple Options for Tab Completion

Sometimes several filenames share the same prefix.

For example:

```text
pwncollege-family
pwncollege-flag
pwncollege-flyswatter
pwncollege-hacking
```

Starting with:

```text
cat /challenge/files/pwn<TAB>
```

may not uniquely identify one file.

Bash completes as much as it safely can.

Pressing **Tab again** can display the possible matches.

Then type additional characters:

```text
cat /challenge/files/pwncollege-fl<TAB>
```

Bash can recognize the unique filename and complete:

```text
pwncollege-flag
```

### Workflow

```text
Type prefix
    ↓
Press TAB
    ↓
Multiple possibilities?
    ↓
Press TAB again
    ↓
View choices
    ↓
Type more characters
    ↓
Press TAB
    ↓
Unique filename completed
```

---

## 10. Tab Completion on Commands

Tab completion works for more than filenames.

Bash can also tab-complete **command names**.

**Challenge technique:**

```text
pwncollege<TAB>
```

Bash completed the command to the challenge-specific executable, such as:

```text
pwncollege-31167
```

Running the completed command produced the flag.

Commands can be completed because Bash searches for available commands, including executables available through directories in `$PATH`.

---

# Globbing Cheat Sheet

| Pattern  | Meaning                               | Example      |
| -------- | ------------------------------------- | ------------ |
| `*`      | Zero or more characters               | `*.txt`      |
| `?`      | Exactly one character                 | `file_?`     |
| `[abc]`  | One character: `a`, `b`, or `c`       | `file_[abc]` |
| `[!abc]` | One character except `a`, `b`, or `c` | `[!abc]*`    |
| `[^abc]` | Bash alternative for exclusion        | `[^abc]*`    |
| `*p*`    | Filename containing `p`               | `*p*`        |
| `[abc]*` | Starts with `a`, `b`, or `c`          | `[abc]*`     |
| `TAB`    | Auto-complete filename/path/command   | `pwn<TAB>`   |

---

# What I Learned

* Bash performs glob expansion before executing a command.
* `*` matches zero or more characters.
* `?` matches exactly one character.
* `[]` matches one character from a specified set.
* `[!...]` excludes specified characters.
* Glob patterns can be used inside full paths.
* Multiple glob patterns can be combined.
* The position of a glob matters.
* Tab completion is safer when trying to identify one specific file.
* Pressing Tab multiple times helps when several possible completions exist.
* Tab completion can complete both filenames and commands.

## Module Status

**Linux Luminarium — File Globbing: ✅ Completed**
