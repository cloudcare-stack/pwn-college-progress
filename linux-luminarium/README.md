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
| Pondering Paths | Position thy self | ✅ Completed |
| Pondering Paths | Position elsewhere | ✅ Completed |
| Pondering Paths | implicit relative paths, from / | ✅ Completed |
| Pondering Paths | explicit relative paths, from / | ✅ Completed |

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

### Pondering Paths — Position thy self

**Status:** ✅ Completed

**Concepts:**

- `cd` changes the shell's current working directory.
- `/` is the root directory.
- A program can require execution while the shell is positioned in a specific directory.
- `pwd` can be used to verify the current working directory.

**Commands used:**

```bash
cd /
/challenge/run
```

**What I learned:**

The challenge required the shell's current working directory to be `/`. I used `cd /` to move to the root directory and then invoked `/challenge/run` using its absolute path.

### Pondering Paths — Position elsewhere

**Status:** ✅ Completed

**Concepts:**

- `cd` can move directly to different directories using absolute paths.
- The current working directory changes each time `cd` is used.
- `/challenge/run` can still be invoked by absolute path regardless of the current directory.
- A challenge can require several directory changes in sequence.

**Commands used:**

```bash
cd /etc
/challenge/run
cd /var/log
/challenge/run
cd /tmp
/challenge/run
cd /usr/share/build-essential
/challenge/run
cd /usr/include
/challenge/run
```

**What I learned:**

I followed the challenge through five levels. At each level, `/challenge/run` gave me the next required directory. I changed to that directory with `cd` and ran the challenge again. This reinforced how the shell's current working directory changes while an absolute program path such as `/challenge/run` remains the same.

### Pondering Paths — implicit relative paths, from /

**Status:** ✅ Completed

**Concepts:**

- A relative path does not begin with `/`.
- Relative paths are interpreted from the current working directory.
- When the current working directory is `/`, `challenge/run` refers to `/challenge/run`.
- The same file can be referenced with either an absolute or relative path depending on the current working directory.

**Commands used:**

```bash
cd /
challenge/run
```

**What I learned:**

After changing the current working directory to `/`, I invoked the challenge using `challenge/run` instead of `/challenge/run`. Because `challenge/run` does not start with `/`, Linux interprets it relative to my current directory. From `/`, that resolves to `/challenge/run`.

### Pondering Paths — explicit relative paths, from /

**Status:** ✅ Completed

**Concepts:**

- `.` represents the current working directory.
- A path beginning with `./` is an explicit relative path.
- Relative paths are resolved from the current working directory.
- From `/`, `./challenge/run` resolves to `/challenge/run`.

**Commands used:**

```bash
cd /
./challenge/run
```

**What I learned:**

I first changed to the root directory with `cd /`. I then used `./challenge/run`, where `.` explicitly means the current directory. Since my current directory was `/`, the relative path `./challenge/run` referred to the same program as the absolute path `/challenge/run`.

> No pwn.college flags are stored in this repository.