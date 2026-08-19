# Terminal Multiplexing

Introduction to terminal multiplexers in Linux using **GNU Screen** and **tmux**. Terminal multiplexers allow multiple terminal sessions or windows to run inside a single terminal and allow sessions to continue running after detaching.

## Challenges

| # | Challenge                      | Status      |
| - | ------------------------------ | ----------- |
| 1 | Launching Screen               | ✅ Completed |
| 2 | Detaching and Attaching        | ✅ Completed |
| 3 | Finding Sessions               | ✅ Completed |
| 4 | Switching Windows              | ✅ Completed |
| 5 | Detaching and Attaching (tmux) | ✅ Completed |
| 6 | Switching Windows (tmux)       | ✅ Completed |

---

## What I Learned

### 1. Launching Screen

**Status:** ✅ Completed

**Concepts:**

* `screen` is a terminal multiplexer.
* It creates a virtual terminal inside the current terminal.
* A screen session can continue running independently of the original shell.
* `exit` or `Ctrl-D` can be used to exit the shell inside a screen session.

**Command used:**

```bash
screen
```

**Exit the session:**

```bash
exit
```

---

### 2. Detaching and Attaching

**Status:** ✅ Completed

**Concepts:**

* A screen session can be **detached** without terminating it.
* Programs inside the detached session continue running.
* A detached session can later be **reattached**.
* Screen uses `Ctrl-A` as its command prefix.
* Screen shortcuts are entered sequentially.

**Start screen:**

```bash
screen
```

**Detach:**

```text
Ctrl-A
d
```

This means:

1. Hold `Ctrl` and press `A`.
2. Release the keys.
3. Press `d`.

**Reattach:**

```bash
screen -r
```

**Challenge workflow:**

```bash
screen
# Ctrl-A, then d
/challenge/run
screen -r
```

---

### 3. Finding Sessions

**Status:** ✅ Completed

**Concepts:**

* Multiple screen sessions can exist at the same time.
* `screen -ls` lists available screen sessions.
* Each session has an identifier containing a PID and session name.
* A particular session can be selected when reattaching.

**List sessions:**

```bash
screen -ls
```

Example:

```text
There are screens on:
    23847.mysession    (Detached)
    23851.goodwork     (Detached)
    23855.morework     (Detached)
```

**Attach using a session name:**

```bash
screen -r goodwork
```

**Attach using a PID:**

```bash
screen -r 23851
```

If the wrong session is opened, detach:

```text
Ctrl-A
d
```

Then attach to another session.

---

### 4. Switching Windows

**Status:** ✅ Completed

**Concepts:**

* One screen session can contain multiple **windows**.
* Windows are similar to tabs inside the same terminal session.
* Screen window commands begin with `Ctrl-A`.

**Important shortcuts:**

| Shortcut        | Action                             |
| --------------- | ---------------------------------- |
| `Ctrl-A`, `c`   | Create a new window                |
| `Ctrl-A`, `n`   | Next window                        |
| `Ctrl-A`, `p`   | Previous window                    |
| `Ctrl-A`, `0-9` | Jump directly to a numbered window |
| `Ctrl-A`, `"`   | Display the window selection menu  |

**Example:**

Attach to the session:

```bash
screen -r
```

Jump directly to Window 0:

```text
Ctrl-A
0
```

**Important distinction:**

* **Session** = the entire screen environment.
* **Window** = an individual terminal running inside that session.

---

### 5. Detaching and Attaching (tmux)

**Status:** ✅ Completed

**Concepts:**

* `tmux` is another terminal multiplexer.
* It provides functionality similar to GNU Screen.
* The default tmux command prefix is `Ctrl-B`.
* Detaching leaves the tmux session running in the background.
* The session can later be reattached.

**Start tmux:**

```bash
tmux
```

**Detach:**

```text
Ctrl-B
d
```

**List tmux sessions:**

```bash
tmux ls
```

**Reattach:**

```bash
tmux attach
```

The shorter version is:

```bash
tmux a
```

**Challenge workflow:**

```bash
tmux
# Ctrl-B, then d
/challenge/run
tmux attach
```

---

### 6. Switching Windows (tmux)

**Status:** ✅ Completed

**Concepts:**

* tmux sessions can contain multiple windows.
* tmux displays its windows in a status bar.
* An asterisk (`*`) identifies the currently active window.
* tmux window shortcuts begin with `Ctrl-B`.

**Important shortcuts:**

| Shortcut        | Action                             |
| --------------- | ---------------------------------- |
| `Ctrl-B`, `c`   | Create a new window                |
| `Ctrl-B`, `n`   | Next window                        |
| `Ctrl-B`, `p`   | Previous window                    |
| `Ctrl-B`, `0-9` | Jump directly to a numbered window |
| `Ctrl-B`, `w`   | Open the window picker             |

**Example:**

Attach to tmux:

```bash
tmux attach
```

Jump directly to Window 0:

```text
Ctrl-B
0
```

---

## Screen vs. tmux

| Task            | GNU Screen      | tmux            |
| --------------- | --------------- | --------------- |
| Start           | `screen`        | `tmux`          |
| Command prefix  | `Ctrl-A`        | `Ctrl-B`        |
| Detach          | `Ctrl-A`, `d`   | `Ctrl-B`, `d`   |
| List sessions   | `screen -ls`    | `tmux ls`       |
| Reattach        | `screen -r`     | `tmux attach`   |
| Short reattach  | —               | `tmux a`        |
| Create window   | `Ctrl-A`, `c`   | `Ctrl-B`, `c`   |
| Next window     | `Ctrl-A`, `n`   | `Ctrl-B`, `n`   |
| Previous window | `Ctrl-A`, `p`   | `Ctrl-B`, `p`   |
| Jump to window  | `Ctrl-A`, `0-9` | `Ctrl-B`, `0-9` |
| Window picker   | `Ctrl-A`, `"`   | `Ctrl-B`, `w`   |

---

## Quick Reference

### GNU Screen

```bash
screen              # Start screen
screen -ls          # List sessions
screen -r            # Reattach
screen -r NAME       # Reattach to specific session
```

```text
Ctrl-A d             # Detach
Ctrl-A c             # Create window
Ctrl-A n             # Next window
Ctrl-A p             # Previous window
Ctrl-A 0-9           # Select numbered window
Ctrl-A "             # Window selection menu
```

### tmux

```bash
tmux                 # Start tmux
tmux ls              # List sessions
tmux attach          # Reattach
tmux a               # Short form of attach
```

```text
Ctrl-B d             # Detach
Ctrl-B c             # Create window
Ctrl-B n             # Next window
Ctrl-B p             # Previous window
Ctrl-B 0-9           # Select numbered window
Ctrl-B w             # Window picker
```

---

## Key Takeaways

* Terminal multiplexers allow terminal sessions to stay alive in the background.
* **Detaching is not the same as exiting**: detaching keeps the session and its processes running.
* `screen` uses **Ctrl-A** as its default prefix.
* `tmux` uses **Ctrl-B** as its default prefix.
* A multiplexer **session** can contain multiple **windows**.
* `screen -ls` and `tmux ls` are useful for finding existing sessions.
* `screen -r` and `tmux attach` allow detached sessions to be resumed.
* Number shortcuts make it easy to move directly between terminal windows.

## Module Complete

✅ **Terminal Multiplexing completed — 6/6 challenges.**
