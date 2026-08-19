# Data Manipulation

This module covers Linux commands used to **translate, delete, extract, and sort data**. It also demonstrates how multiple commands can be connected with pipes (`|`) to process data step by step.

## Challenges

| # | Challenge                              | Status      |
| - | -------------------------------------- | ----------- |
| 1 | Translating Characters                 | ✅ Completed |
| 2 | Deleting Characters                    | ✅ Completed |
| 3 | Deleting Newlines                      | ✅ Completed |
| 4 | Extracting the First Lines with `head` | ✅ Completed |
| 5 | Extracting Specific Sections of Text   | ✅ Completed |
| 6 | Sorting Data                           | ✅ Completed |

---

## What I Learned

### 1. Translating Characters

**Status:** ✅ Completed

**Concepts:**

* `tr` translates characters from one set into another.
* Characters are translated based on their corresponding positions in each set.
* `A-Z` represents uppercase letters.
* `a-z` represents lowercase letters.
* Both sets can be combined to swap uppercase and lowercase characters.

**Command used:**

```bash
/challenge/run | tr 'A-Za-z' 'a-zA-Z'
```

**How it works:**

```text
A-Z → a-z
a-z → A-Z
```

The challenge swapped the case of every character in the flag. Using `tr` reversed the transformation.

**General syntax:**

```bash
tr 'SET1' 'SET2'
```

---

### 2. Deleting Characters

**Status:** ✅ Completed

**Concepts:**

* `tr` can also delete characters.
* The `-d` option means **delete**.
* Every occurrence of the specified characters is removed from the input.

**Command used:**

```bash
/challenge/run | tr -d '^%'
```

The challenge inserted `^` and `%` characters throughout the flag. The command removed both characters.

**Example:**

```bash
echo 'p^w%n' | tr -d '^%'
```

Output:

```text
pwn
```

**General syntax:**

```bash
tr -d 'CHARACTERS'
```

---

### 3. Deleting Newlines

**Status:** ✅ Completed

**Concepts:**

* `\n` represents a newline character.
* `tr -d '\n'` removes newline characters.
* Removing newlines can combine multiple lines into one continuous line.

**Command used:**

```bash
/challenge/run | tr -d '\n'
```

The challenge split the flag across many lines. Deleting the newline characters joined the pieces back together.

**Useful command:**

```bash
tr -d '\n'
```

This is useful when output needs to be converted from multiple lines into a single line.

---

### 4. Extracting the First Lines with `head`

**Status:** ✅ Completed

**Concepts:**

* `head` displays the beginning of input.
* By default, `head` displays the first **10 lines**.
* The `-n` option specifies exactly how many lines to keep.
* Multiple pipes can connect several commands together.

**Command used:**

```bash
/challenge/pwn | head -n 7 | /challenge/college
```

**How it works:**

```text
/challenge/pwn
      |
      v
 head -n 7
      |
      v
/challenge/college
```

1. `/challenge/pwn` generated the secret codes.
2. `head -n 7` selected only the first seven lines.
3. The second pipe sent those seven lines to `/challenge/college`.
4. `/challenge/college` verified the codes and returned the flag.

**General syntax:**

```bash
head -n NUMBER
```

Example:

```bash
head -n 5 file.txt
```

---

### 5. Extracting Specific Sections of Text

**Status:** ✅ Completed

**Concepts:**

* `cut` extracts specific fields or columns from text.
* `-d` specifies the **delimiter** separating the fields.
* `-f` specifies which **field** to extract.
* `cut` can be combined with other commands using pipes.

**Command used:**

```bash
/challenge/run | cut -d " " -f 2 | tr -d '\n'
```

**How it works:**

```text
/challenge/run
      |
      v
cut -d " " -f 2
      |
      v
 tr -d '\n'
      |
      v
 complete flag
```

The options mean:

```text
-d " "    Use a space as the delimiter
-f 2      Extract the second field
```

After extracting the flag character from each line, `tr -d '\n'` removed the newlines and joined the characters together.

**General syntax:**

```bash
cut -d "DELIMITER" -f FIELD_NUMBER
```

Example:

```bash
cut -d ":" -f 1 file.txt
```

This extracts the first field from colon-separated data.

---

### 6. Sorting Data

**Status:** ✅ Completed

**Concepts:**

* `sort` organizes lines of text.
* By default, lines are sorted alphabetically/lexicographically.
* `sort` can also sort numerically, reverse the order, remove duplicates, or randomize lines.

**Command used:**

```bash
sort /challenge/flags.txt
```

The challenge contained many fake flags and one real flag. Sorting the file alphabetically made the real flag identifiable among the generated fake flags.

**Useful `sort` options:**

```bash
sort file.txt
```

Sort alphabetically.

```bash
sort -r file.txt
```

Sort in reverse order.

```bash
sort -n file.txt
```

Sort numerically.

```bash
sort -u file.txt
```

Sort and remove duplicate lines.

```bash
sort -R file.txt
```

Randomize the order of the lines.

---

## Command Cheat Sheet

| Command            | Purpose                                      |
| ------------------ | -------------------------------------------- |
| `tr 'SET1' 'SET2'` | Translate characters                         |
| `tr -d 'CHARS'`    | Delete specified characters                  |
| `tr -d '\n'`       | Delete newlines                              |
| `head`             | Display the first 10 lines                   |
| `head -n N`        | Display the first `N` lines                  |
| `cut -d " " -f N`  | Extract field `N` using spaces as delimiters |
| `sort`             | Sort lines alphabetically                    |
| `sort -r`          | Reverse sort                                 |
| `sort -n`          | Numerical sort                               |
| `sort -u`          | Sort and remove duplicates                   |
| `sort -R`          | Randomize lines                              |
| `\|`               | Pipe output from one command into another    |

---

## Key Takeaways

* `tr` is useful for **character-level manipulation**.
* `tr -d` removes unwanted characters from data.
* `\n` represents a newline.
* `head` extracts lines from the beginning of input.
* `cut` extracts specific columns or fields.
* `sort` reorganizes lines of data.
* Pipes allow several commands to form a **data-processing pipeline**.
* Small Linux utilities become especially powerful when they are combined.

## Module Status

**Data Manipulation: ✅ Completed — 6/6 challenges**
