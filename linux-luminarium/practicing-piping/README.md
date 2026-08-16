# Piping

Linux Luminarium module covering input/output redirection, pipes, filtering, process substitution, and named pipes.

## Challenges

| # | Challenge | Status |
|---|---|---|
| 1 | Redirecting Output | ✅ Completed |
| 2 | Redirecting More Output | ✅ Completed |
| 3 | Appending Output | ✅ Completed |
| 4 | Redirecting Errors | ✅ Completed |
| 5 | Redirecting Input | ✅ Completed |
| 6 | Grepping Stored Results | ✅ Completed |
| 7 | Grepping Live Output | ✅ Completed |
| 8 | Grepping Errors | ✅ Completed |
| 9 | Filtering with `grep -v` | ✅ Completed |
| 10 | Filtering with `sed` | ✅ Completed |
| 11 | Duplicating Piped Data with `tee` | ✅ Completed |
| 12 | Process Substitution for Input | ✅ Completed |
| 13 | Writing to Multiple Programs | ✅ Completed |
| 14 | Split-Piping `stderr` and `stdout` | ✅ Completed |
| 15 | Named Pipes | ✅ Completed |

---

## What I Learned

### 1. Redirecting Output

**Status:** ✅ Completed

**Concepts:**

- Standard output (`stdout`) normally appears in the terminal.
- The `>` operator redirects stdout to a file.
- `>` creates the file if it does not exist.
- If the file already exists, `>` overwrites it.
- Standard output uses file descriptor `1`.

**Syntax:**

```bash
command > file
```

**Example:**

```bash
echo "hello" > message
cat message
```

---

### 2. Redirecting More Output

**Status:** ✅ Completed

**Concepts:**

- Output from any command can be redirected into a file.
- This is useful when a program produces output that needs to be saved or examined later.

**Example:**

```bash
/challenge/run > myflag
cat myflag
```

---

### 3. Appending Output

**Status:** ✅ Completed

**Concepts:**

- `>` overwrites existing file contents.
- `>>` adds new output to the end of an existing file.

**Example:**

```bash
echo "first" > file
echo "second" >> file
cat file
```

**Remember:**

```text
>     overwrite
>>    append
```

---

### 4. Redirecting Errors

**Status:** ✅ Completed

**Concepts:**

Linux programs normally use three standard file descriptors:

| FD | Stream | Purpose |
|---|---|---|
| `0` | stdin | Standard input |
| `1` | stdout | Standard output |
| `2` | stderr | Standard error |

Standard error can be redirected with:

```bash
2>
```

**Example:**

```bash
/challenge/run > myflag 2> instructions
```

This sends:

```text
stdout → myflag
stderr → instructions
```

**Lesson Learned:**

The destination filename can matter. If the challenge requires `instructions`, redirecting stderr to a different filename will not satisfy the challenge.

---

### 5. Redirecting Input

**Status:** ✅ Completed

**Concepts:**

- `<` redirects a file into a program's standard input.
- It is essentially the opposite direction of `>`.

**Syntax:**

```bash
command < file
```

**Challenge Example:**

```bash
echo COLLEGE > PWN
/challenge/run < PWN
```

**Remember:**

```text
>    program → file
<    file → program
```

---

### 6. Grepping Stored Results

**Status:** ✅ Completed

**Concepts:**

- `grep` searches text for matching patterns.
- Large command output can first be stored in a file.
- `grep` can then search the saved output.

**Challenge Pattern:**

```bash
/challenge/run > /tmp/data.txt
grep pwn.college /tmp/data.txt
```

**General Syntax:**

```bash
grep PATTERN FILE
```

---

### 7. Grepping Live Output

**Status:** ✅ Completed

**Concepts:**

- `|` is the pipe operator.
- A pipe connects the stdout of the command on the left to the stdin of the command on the right.
- An intermediate file is not required.

**Challenge Pattern:**

```bash
/challenge/run | grep pwn.college
```

**Data Flow:**

```text
/challenge/run → stdout → grep → terminal
```

---

### 8. Grepping Errors

**Status:** ✅ Completed

**Concepts:**

- A normal pipe only sends stdout.
- Error messages are normally sent through stderr.
- `2>&1` redirects stderr to the same destination as stdout.

**Challenge Pattern:**

```bash
/challenge/run 2>&1 | grep pwn.college
```

**Meaning:**

```text
2>&1
```

means:

```text
stderr (FD 2) → stdout (FD 1)
```

Then both streams can pass through the pipe.

---

### 9. Filtering with `grep -v`

**Status:** ✅ Completed

**Concepts:**

- Normal `grep` keeps lines matching a pattern.
- `grep -v` performs an inverted match.
- It removes matching lines and keeps everything else.

**Challenge Pattern:**

```bash
/challenge/run | grep -v DECOY
```

**Remember:**

```text
grep pattern       → keep matching lines
grep -v pattern    → remove matching lines
```

---

### 10. Filtering with `sed`

**Status:** ✅ Completed

**Concepts:**

- `sed` is a stream editor.
- It can search for text and replace it.
- Replacing text with nothing effectively removes it.

**Syntax:**

```bash
sed 's/old/new/g'
```

Where:

```text
s    = substitute
old  = text to find
new  = replacement
g    = replace every occurrence
```

**Challenge Pattern:**

```bash
/challenge/run | sed 's/FAKEFLAG//g'
```

This removes every occurrence of `FAKEFLAG`.

---

### 11. Duplicating Piped Data with `tee`

**Status:** ✅ Completed

**Concepts:**

- `tee` duplicates a data stream.
- It reads from stdin.
- It writes the data to one or more files.
- It also keeps sending the data through stdout.
- This is useful for debugging pipelines.

**Example:**

```bash
echo hi | tee pwn college
```

**Data Flow:**

```text
             ┌──> pwn
echo hi ──> tee ──> college
             └──> stdout
```

---

### 12. Process Substitution for Input

**Status:** ✅ Completed

**Concepts:**

Process substitution allows the output of a command to behave like a temporary input file.

**Syntax:**

```bash
<(command)
```

**Example:**

```bash
cat <(echo hello)
```

Bash may internally represent this as something similar to:

```text
/dev/fd/63
```

Process substitution is especially useful with programs such as `diff`.

Instead of:

```bash
command1 > file1
command2 > file2
diff file1 file2
```

we can use:

```bash
diff <(command1) <(command2)
```

**Challenge Pattern:**

```bash
diff <(/challenge/print_decoys) <(/challenge/print_decoys_and_flag)
```

This compares the two command outputs without manually creating temporary files.

---

### 13. Writing to Multiple Programs

**Status:** ✅ Completed

**Concepts:**

Output process substitution uses:

```bash
>(command)
```

This allows another program to write directly into the stdin of a command.

Combined with `tee`, one stream can be duplicated to multiple programs.

**Challenge Pattern:**

```bash
/challenge/hack | tee >(/challenge/the) >(/challenge/planet)
```

**Data Flow:**

```text
                  ┌──> /challenge/the
/challenge/hack ─> tee
                  └──> /challenge/planet
```

**Important:**

This is different from:

```bash
tee /challenge/the /challenge/planet
```

Without process substitution, `tee` treats those paths as files instead of launching the programs.

---

### 14. Split-Piping stderr and stdout

**Status:** ✅ Completed

**Concepts:**

- stdout and stderr can be sent to completely different programs.
- `>` redirects stdout.
- `2>` redirects stderr.
- `>(command)` allows the destination to be another program.

**Challenge Pattern:**

```bash
/challenge/hack > >(/challenge/planet) 2> >(/challenge/the)
```

**Data Flow:**

```text
                   stdout ──> /challenge/planet
/challenge/hack
                   stderr ──> /challenge/the
```

This keeps the two streams separate instead of combining them.

---

### 15. Named Pipes

**Status:** ✅ Completed

**Concepts:**

- A named pipe is also called a FIFO.
- FIFO stands for **First In, First Out**.
- A FIFO allows processes to communicate through a filesystem path.
- `mkfifo` creates a named pipe.
- Readers and writers can block while waiting for the other side of the FIFO.

**Create a FIFO:**

```bash
mkfifo /tmp/flag_fifo
```

**Verify It:**

```bash
ls -l /tmp/flag_fifo
```

A FIFO begins with:

```text
p
```

Example:

```text
prw-r--r--
```

A normal file begins with:

```text
-
```

Example:

```text
-rw-r--r--
```

**Problem Encountered:**

`/tmp/flag_fifo` initially existed as a regular file.

Because of that:

```bash
cat /tmp/flag_fifo
```

immediately returned instead of waiting for data.

**Fix:**

```bash
rm /tmp/flag_fifo
mkfifo /tmp/flag_fifo
```

Then verify:

```bash
ls -l /tmp/flag_fifo
```

**Terminal 1 — Reader:**

```bash
cat /tmp/flag_fifo
```

This waits for a writer.

**Terminal 2 — Writer:**

```bash
/challenge/run > /tmp/flag_fifo
```

**Data Flow:**

```text
/challenge/run
      |
    stdout
      |
      v
/tmp/flag_fifo
      |
      v
     cat
      |
      v
    terminal
```

---

# Piping Cheat Sheet

| Syntax | Purpose |
|---|---|
| `>` | Redirect stdout and overwrite file |
| `>>` | Redirect stdout and append |
| `<` | Redirect file to stdin |
| `2>` | Redirect stderr |
| `2>&1` | Redirect stderr to stdout |
| `\|` | Pipe stdout to another command |
| `grep` | Keep matching lines |
| `grep -v` | Remove matching lines |
| `sed` | Edit/filter streamed text |
| `tee` | Duplicate a stream |
| `<(command)` | Use command output like an input file |
| `>(command)` | Send data into another command |
| `mkfifo` | Create a named pipe |

## Useful Commands

```bash
# Redirect stdout
command > file

# Append stdout
command >> file

# Redirect stdin
command < file

# Redirect stderr
command 2> errors

# Redirect stdout and stderr separately
command > output 2> errors

# Combine stderr and stdout
command 2>&1

# Pipe stdout
command1 | command2

# Search output
command | grep pattern

# Remove matching lines
command | grep -v pattern

# Remove text with sed
command | sed 's/text//g'

# Duplicate a stream
command | tee file

# Compare outputs directly
diff <(command1) <(command2)

# Send one stream to multiple programs
command | tee >(program1) >(program2)

# Split stdout and stderr
command > >(program1) 2> >(program2)

# Create a FIFO
mkfifo /tmp/my_fifo

# Read from FIFO
cat /tmp/my_fifo

# Write to FIFO
command > /tmp/my_fifo
```

---

## Key Takeaways

1. Linux treats input and output as streams.
2. `stdin`, `stdout`, and `stderr` are represented by file descriptors `0`, `1`, and `2`.
3. Redirection changes where those streams come from or go.
4. Pipes connect programs directly without intermediate files.
5. `grep`, `grep -v`, and `sed` are powerful stream-filtering tools.
6. `tee` allows one stream to be duplicated.
7. Process substitution lets command input/output behave like files.
8. Named pipes provide persistent filesystem paths for communication between processes.
9. Understanding data flow makes complex shell commands much easier to read and debug.

## Module Status

**Linux Luminarium — Piping: ✅ Completed**