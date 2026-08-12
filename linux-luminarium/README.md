# Linux Luminarium

This folder documents my progress through the pwn.college Linux Luminarium.

## Goals

- Improve Linux command-line skills
- Understand files and directories
- Learn permissions and ownership
- Practice shell features
- Understand processes and environment variables
- Become more comfortable working entirely from the terminal

## Progress

| Module | Challenge | Status |
|---|---|---|
| Pondering Paths | The Root | ✅ Completed |
| Pondering Paths | Program and Absolute Paths | ✅ Completed |

## Notes

### Pondering Paths — The Root

**Status:** ✅ Completed

**Concepts:**

- The Linux filesystem begins at the root directory `/`.
- A path that begins with `/` is an absolute path.
- An absolute path identifies a file or program starting from the root directory, regardless of the current working directory.

**Command used:**

```bash
/pwn
```

**What I learned:**

The `pwn` program was located directly inside the root directory. Using `/pwn` invokes the program with its absolute path. I did not need to change directories first because an absolute path always starts from `/`.

### Pondering Paths — Program and Absolute Paths

**Status:** ✅ Completed

**Concepts:**

- Absolute paths begin at the root directory `/`.
- Directories can contain other files and programs.
- Directory names are separated by `/` in a Linux path.
- A program can be executed by specifying its complete absolute path.

**Command used:**

```bash
/challenge/run
```

**What I learned:**

The `run` program was stored inside the `/challenge` directory. The absolute path `/challenge/run` tells Linux to start at `/`, look inside the `challenge` directory, and execute the `run` program.

> No pwn.college flags are stored in this repository.