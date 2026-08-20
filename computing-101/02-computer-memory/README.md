# Computer Memory

Introduction to accessing computer memory with x86-64 assembly. This module covers memory addresses, pointers, dereferencing, offsets, stored addresses, and multiple levels of indirection.

## Challenges

| # | Challenge                  | Status      |
| - | -------------------------- | ----------- |
| 1 | Loading From Memory        | ✅ Completed |
| 2 | More Loading Practice      | ✅ Completed |
| 3 | Dereferencing Pointers     | ✅ Completed |
| 4 | Dereferencing Yourself     | ✅ Completed |
| 5 | Dereferencing with Offsets | ✅ Completed |
| 6 | Stored Addresses           | ✅ Completed |
| 7 | Double Dereference         | ✅ Completed |

---

## What I Learned

### 1. Loading From Memory

**Status:** ✅ Completed

**Concepts:**

* Memory is organized into addresses.
* Each memory address can contain data.
* Square brackets `[]` tell the CPU to access the contents of memory.
* A number without brackets is treated as a value.
* A number inside brackets is treated as a memory address.

**Key difference:**

```asm
mov rdi, 133700
```

This stores the number `133700` directly in `rdi`.

```asm
mov rdi, [133700]
```

This accesses memory address `133700` and loads the value stored there into `rdi`.

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [133700]
    mov rax, 60
    syscall
```

---

### 2. More Loading Practice

**Status:** ✅ Completed

**Concepts:**

* Practiced loading data directly from another memory address.
* The same dereferencing syntax works regardless of the specific address.
* The address identifies where the data is located.

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [123400]
    mov rax, 60
    syscall
```

**Important pattern:**

```asm
mov register, [address]
```

This means:

> Load the value stored at `address` into `register`.

---

### 3. Dereferencing Pointers

**Status:** ✅ Completed

**Concepts:**

* Memory addresses can be stored inside registers.
* A register containing a memory address can act as a **pointer**.
* Dereferencing a pointer means accessing the memory location to which it points.
* Registers are general-purpose and can hold either ordinary values or addresses.

If `rax` contains an address:

```asm
mov rdi, rax
```

copies the address itself.

But:

```asm
mov rdi, [rax]
```

uses the value in `rax` as an address and loads the data stored there.

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [rax]
    mov rax, 60
    syscall
```

---

### 4. Dereferencing Yourself

**Status:** ✅ Completed

**Concepts:**

* A register can be both the pointer and the destination of a memory operation.
* Dereferencing a register can replace the address stored in that register with the value found at that address.

**Key instruction:**

```asm
mov rdi, [rdi]
```

Before:

```text
rdi = address of secret
```

After:

```text
rdi = secret value
```

The pointer stored in `rdi` is replaced by the value it pointed to.

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [rdi]
    mov rax, 60
    syscall
```

---

### 5. Dereferencing with Offsets

**Status:** ✅ Completed

**Concepts:**

* A pointer does not always point directly to the exact piece of data needed.
* An **offset** can be added to a pointer before dereferencing it.
* Memory addresses refer to bytes.
* `[register + offset]` accesses memory a certain number of bytes away from the address stored in the register.

**Key instruction:**

```asm
mov rdi, [rdi+8]
```

This means:

1. Take the address stored in `rdi`.
2. Add `8` bytes.
3. Access memory at the resulting address.
4. Load that value into `rdi`.

Conceptually:

```text
rdi ──► base address
         +0
         +1
         +2
         ...
         +8 ──► desired value
```

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [rdi+8]
    mov rax, 60
    syscall
```

---

### 6. Stored Addresses

**Status:** ✅ Completed

**Concepts:**

* Memory can contain ordinary data or another memory address.
* An address stored in memory can be loaded and then used as a pointer.
* This introduces another level of indirection.

Conceptually:

```text
567800
   │
   ▼
stored address
   │
   ▼
secret value
```

First:

```asm
mov rdi, [567800]
```

loads the address stored at memory location `567800`.

Then:

```asm
mov rdi, [rdi]
```

dereferences that address to retrieve the secret value.

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [567800]
    mov rdi, [rdi]
    mov rax, 60
    syscall
```

---

### 7. Double Dereference

**Status:** ✅ Completed

**Concepts:**

* Pointers can form chains.
* One memory location can contain the address of another memory location.
* Multiple dereferences can be required to reach the final data.
* This is similar to working with pointers to pointers.

The challenge starts with `rax` containing an address.

Conceptually:

```text
rax
 │
 ▼
SECRET_LOCATION_2
 │
 ▼
SECRET_LOCATION_1
 │
 ▼
SECRET_VALUE
```

The first dereference:

```asm
mov rdi, [rax]
```

retrieves the next address.

The second dereference:

```asm
mov rdi, [rdi]
```

retrieves the actual secret value.

**Solution pattern:**

```asm
.intel_syntax noprefix
.global _start

_start:
    mov rdi, [rax]
    mov rdi, [rdi]
    mov rax, 60
    syscall
```

---

## Important Memory Syntax

### Immediate Value

```asm
mov rdi, 133700
```

Means:

```text
rdi = 133700
```

### Direct Memory Access

```asm
mov rdi, [133700]
```

Means:

```text
rdi = value stored at address 133700
```

### Pointer Dereference

```asm
mov rdi, [rax]
```

Means:

```text
rdi = value stored at the address contained in rax
```

### Self Dereference

```asm
mov rdi, [rdi]
```

Means:

```text
rdi = value stored at the address contained in rdi
```

### Dereference with Offset

```asm
mov rdi, [rdi+8]
```

Means:

```text
rdi = value stored 8 bytes after the address contained in rdi
```

### Double Dereference

```asm
mov rdi, [rax]
mov rdi, [rdi]
```

Means:

```text
rax → address → address → value
```

---

## Assembly Workflow

Assembly source files use the `.s` extension:

```text
program.s
```

Assemble the source code:

```bash
as -o program.o program.s
```

This creates an object file:

```text
program.o
```

Link the object file:

```bash
ld -o program program.o
```

This creates the executable:

```text
program
```

For these challenges, pass the **executable**, not the `.s` source file, to the checker:

```bash
/challenge/check ./program
```

---

## Key Takeaways

* `[]` means **access memory**.
* A pointer is a value that represents a memory address.
* Dereferencing means following a pointer to retrieve the data stored there.
* `mov rdi, rax` copies a value.
* `mov rdi, [rax]` follows an address.
* Registers can contain either ordinary values or pointers.
* `[register+offset]` accesses data relative to a pointer.
* Memory itself can store pointers.
* Following multiple stored addresses requires multiple dereferences.
* Understanding pointers and memory addressing is fundamental to assembly, arrays, structures, the stack, and binary exploitation.

## Module Status

**Computer Memory — ✅ Completed**
