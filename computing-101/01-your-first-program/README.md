# Your First Program

Introduction to writing, running, and building basic **x86-64 assembly programs** on Linux.

## Challenges

| # | Challenge                | Status      |
| - | ------------------------ | ----------- |
| 1 | Your First Register      | ✅ Completed |
| 2 | Your First Syscall       | ✅ Completed |
| 3 | Exit Codes               | ✅ Completed |
| 4 | Building Executables     | ✅ Completed |
| 5 | Moving Between Registers | ✅ Completed |

---

## What I Learned

### 1. Your First Register

**Status:** ✅ Completed

**Concepts:**

* CPU registers are small, fast storage locations inside the processor.
* `rax` is one of the general-purpose x86-64 registers.
* The `mov` instruction copies a value into a register.
* pwn.college uses **Intel syntax**, where the destination comes before the source.

**Example:**

```asm
mov rax, 60
```

This stores the value `60` in `rax`.

**Syntax:**

```text
mov destination, source
```

---

### 2. Your First Syscall

**Status:** ✅ Completed

**Concepts:**

* Programs interact with the Linux operating system through **system calls (syscalls)**.
* The syscall number is placed in `rax`.
* On x86-64 Linux, syscall `60` is `exit`.
* The `syscall` instruction asks the operating system to perform the requested operation.

**Assembly:**

```asm
mov rax, 60
syscall
```

**Meaning:**

```text
rax = 60 → select exit syscall
syscall  → ask Linux to execute it
```

---

### 3. Exit Codes

**Status:** ✅ Completed

**Concepts:**

* Programs return an **exit code** when they terminate.
* For Linux x86-64 syscalls, the first argument is passed through `rdi`.
* For the `exit` syscall:

  * `rax` contains syscall number `60`.
  * `rdi` contains the desired exit code.
* `$?` in Bash contains the exit code of the previously executed command.

**Assembly:**

```asm
mov rax, 60
mov rdi, 42
syscall
```

**Checking the exit code:**

```bash
./exit-code-challenge.s
echo $?
```

Expected result:

```text
42
```

---

### 4. Building Executables

**Status:** ✅ Completed

**Concepts:**

* Assembly source code must be converted into machine code before the CPU can execute it.
* `.intel_syntax noprefix` tells the GNU assembler to use Intel assembly syntax.
* `as` assembles source code into an **object file**.
* `ld` links an object file into an **executable**.
* An object file contains machine code but is not yet the final executable.

**Assembly source:**

```asm
.intel_syntax noprefix
mov rdi, 42
mov rax, 60
syscall
```

**Assemble:**

```bash
as -o program.o program.s
```

This converts:

```text
program.s → program.o
```

**Link:**

```bash
ld -o program program.o
```

This converts:

```text
program.o → program
```

**Complete build process:**

```text
Assembly Source        Object File        Executable
program.s
    │
    │ as
    ▼
program.o
    │
    │ ld
    ▼
program
```

Or simply:

```text
.s → as → .o → ld → executable
```

**Run and inspect the exit code:**

```bash
./program
echo $?
```

---

### 5. Moving Between Registers

**Status:** ✅ Completed

**Concepts:**

* `mov` can copy data from one register to another.
* Moving data does **not** remove it from the original register.
* Register values can be supplied by the environment before the program begins executing.
* A program does not need to know a value beforehand to copy it between registers.

**Example:**

```asm
mov rdi, rsi
```

If:

```text
rsi = secret
```

then after the instruction:

```text
rsi = secret
rdi = secret
```

For the challenge, the secret value was supplied in `rsi` and copied into `rdi` so it became the argument to `exit`.

**Assembly:**

```asm
.intel_syntax noprefix
.global _start
_start:
mov rdi, rsi
mov rax, 60
syscall
```

The flow is:

```text
rsi = secret
      │
      │ mov rdi, rsi
      ▼
rdi = secret

rax = 60
      │
      ▼
syscall → exit(secret)
```

---

## Important Registers Learned

| Register | Purpose Learned                                                              |
| -------- | ---------------------------------------------------------------------------- |
| `rax`    | Holds the syscall number                                                     |
| `rdi`    | Holds the first syscall argument                                             |
| `rsi`    | General-purpose register; also used for passing additional syscall arguments |

For the `exit` syscall:

```text
rax = 60          → syscall number
rdi = exit_code   → first argument
syscall           → execute syscall
```

---

## Important Commands

```bash
# Assemble source into an object file
as -o program.o program.s

# Link object file into an executable
ld -o program program.o

# Run executable
./program

# Display previous command's exit code
echo $?

# Identify file types
file program.s program.o program

# Submit assembly source when requested
/challenge/check program.s

# Submit executable when requested
/challenge/check program
```

---

## Key Takeaways

* Registers provide fast storage directly inside the CPU.
* `mov` copies data into registers or between registers.
* Linux programs request operating-system services using syscalls.
* `rax` identifies which syscall to execute.
* `rdi` supplies the first syscall argument.
* `syscall` transfers the request to the operating system.
* Exit codes can be inspected with `echo $?`.
* Assembly source must normally be **assembled** and **linked** before becoming an executable.
* The fundamental assembly build process is:

```text
Source Code → Assembler → Object File → Linker → Executable
```

This module established the foundation for writing x86-64 assembly and understanding how low-level programs communicate with Linux.
