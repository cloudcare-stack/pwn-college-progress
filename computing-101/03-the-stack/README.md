# The Stack

Introduction to the **stack** in x86-64 assembly, including accessing stack memory, using offsets, retrieving command-line arguments, dereferencing pointers, and popping values from the stack.

## Challenges

| # | Challenge                      | Status      |
| - | ------------------------------ | ----------- |
| 1 | The Stack                      | ✅ Completed |
| 2 | Stack Offsets                  | ✅ Completed |
| 3 | Program Arguments on the Stack | ✅ Completed |
| 4 | Popping From the Stack         | ✅ Completed |

## What I Learned

### 1. The Stack

**Status:** ✅ Completed

**Concepts:**

* The stack is a region of memory used by a running program.
* The `rsp` register is the **stack pointer**.
* At program startup, `rsp` points to the top of the stack.
* The value at `[rsp]` contains `argc`, the number of command-line arguments.
* Square brackets dereference a memory address.

**Example:**

```asm
mov rdi, [rsp]
```

This means:

```text
rdi = value stored at the address in rsp
```

The argument count can be used as the program's exit code:

```asm
.intel_syntax noprefix
.global _start
_start:
    mov rdi, [rsp]
    mov rax, 60
    syscall
```

**Important distinction:**

```asm
mov rdi, rsp       # copy the address itself
mov rdi, [rsp]     # read the value at that address
```

---

### 2. Stack Offsets

**Status:** ✅ Completed

**Concepts:**

* Values farther into the stack can be accessed using offsets from `rsp`.
* The syntax `[rsp+N]` accesses memory `N` bytes after the address in `rsp`.
* On x86-64, many stack values such as addresses are 8 bytes wide.
* Consecutive 8-byte values therefore appear at offsets of `8`, `16`, `24`, etc.

**Example stack layout:**

```text
[rsp]       value 0
[rsp+8]     value 1
[rsp+16]    value 2
[rsp+24]    value 3
...
[rsp+128]   value 16
```

Since:

```text
128 / 8 = 16
```

`[rsp+128]` accesses the value 16 eight-byte slots after `[rsp]`.

**Solution concept:**

```asm
mov rdi, [rsp+128]
mov rax, 60
syscall
```

---

### 3. Program Arguments on the Stack

**Status:** ✅ Completed

**Concepts:**

* Linux places information about command-line arguments on the initial stack.
* `[rsp]` contains `argc`.
* `[rsp+8]` contains a pointer to `argv[0]`, the program name.
* `[rsp+16]` contains a pointer to `argv[1]`, the first user-supplied argument.
* The stack contains **pointers to argument strings**, not the strings directly.
* Reading the actual argument therefore requires another dereference.

**Simplified stack layout:**

```text
[rsp]       argc
[rsp+8]     pointer -> argv[0]
[rsp+16]    pointer -> argv[1]
[rsp+24]    pointer -> argv[2]
...
```

To retrieve the first argument:

```asm
mov rdi, [rsp+16]
```

Now `rdi` contains the address of `argv[1]`.

Then:

```asm
mov rdi, [rdi]
```

dereferences that pointer and reads the argument data.

**Example:**

```asm
.intel_syntax noprefix
.global _start
_start:
    mov rdi, [rsp+16]
    mov rdi, [rdi]
    mov rax, 60
    syscall
```

Testing with a one-character argument demonstrates ASCII values:

```bash
./args-on-the-stack h
echo $?
```

Result:

```text
104
```

because:

```text
'h' = ASCII 104
```

Similarly:

```text
'p' = 112
'A' = 65
```

**Pointer chain:**

```text
[rsp+16]
    |
    v
address of argv[1]
    |
    v
[argv[1]]
    |
    v
argument data
```

---

### 4. Popping From the Stack

**Status:** ✅ Completed

**Concepts:**

* The `pop` instruction removes the current logical top value from the stack.
* `pop` takes only one operand.
* `pop rdi` reads `[rsp]` into `rdi`.
* It then automatically increases `rsp` by 8 bytes on x86-64.
* The old data is not necessarily erased from memory; `rsp` simply moves past it.

**Instruction:**

```asm
pop rdi
```

Conceptually, this performs:

```asm
mov rdi, [rsp]
add rsp, 8
```

Before:

```text
rsp
 |
 v
+----------+
|   argc   |
+----------+
| argv[0]  |
+----------+
```

After:

```text
             rsp
              |
              v
+----------+
|   argc   |   <- old data may still exist
+----------+
| argv[0]  |
+----------+
```

The challenge used:

```asm
.intel_syntax noprefix
.global _start
_start:
    pop rdi
    mov rax, 60
    syscall
```

This pops `argc` into `rdi` and uses it as the exit code.

## Key Stack Syntax

```asm
rsp                 # stack pointer/address

[rsp]               # value at top of stack

[rsp+8]             # value 8 bytes after rsp

[rsp+16]            # value 16 bytes after rsp

mov rdi, [rsp]      # read top stack value

mov rdi, [rsp+16]   # read value at an offset

mov rdi, [rdi]      # dereference address stored in rdi

pop rdi             # rdi = [rsp], then rsp += 8
```

## Initial Linux Stack Layout

A simplified view of the stack when a program begins:

```text
Higher addresses

+-----------------------+
| ...                   |
+-----------------------+
| argv strings          |
+-----------------------+
| ...                   |
+-----------------------+
| NULL                  |
+-----------------------+
| argv[2] pointer       |  [rsp+24]
+-----------------------+
| argv[1] pointer       |  [rsp+16]
+-----------------------+
| argv[0] pointer       |  [rsp+8]
+-----------------------+
| argc                  |  [rsp]  <- rsp
+-----------------------+

Lower addresses
```

## Key Takeaways

* `rsp` stores the address of the current top of the stack.
* `[rsp]` dereferences `rsp` and reads the value stored there.
* Stack offsets allow access to nearby values.
* 64-bit values are commonly separated by 8 bytes.
* Program arguments are represented by pointers on the initial stack.
* Following a pointer requires another memory dereference.
* `pop` reads the top stack value and automatically advances `rsp`.
* Understanding the stack is fundamental for assembly, function calls, debugging, and binary exploitation.

## Module Status

**The Stack — ✅ Completed**
