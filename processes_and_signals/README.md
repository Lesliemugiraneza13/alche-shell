# Processes and Signals

A collection of Bash scripts exploring Linux process management and Unix signals.

## Learning Objectives

- Understand what a process is and how to identify its PID
- List, inspect, and filter running processes
- Send signals to processes using `kill`, `pkill`, and `pgrep`
- Use `trap` to handle signals within a script
- Write a basic process manager with start/stop/restart functionality

## Scripts

| File | Description |
|---|---|
| `0-what-is-my-pid` | Prints the PID of the currently running script using `$$` |
| `1-list_your_processes` | Lists all running processes in user-friendly tree format using `ps auxf` |
| `2-show_your_bash_pid` | Filters the process list to show only lines containing `bash` |
| `3-show_your_bash_pid_made_easy` | Uses `pgrep -l` to display PID and name of all bash processes |
| `4-to_infinity_and_beyond` | Runs an infinite loop, printing a message every 2 seconds |
| `5-dont_stop_me_now` | Stops the `4-to_infinity_and_beyond` process using `kill` |
| `6-stop_me_if_you_can` | Stops `4-to_infinity_and_beyond` using `pkill` (no `kill` or `killall` allowed) |
| `7-highlander` | Infinite loop that catches `SIGTERM` and prints a message instead of stopping |
| `67-stop_me_if_you_can` | Stops the `7-highlander` process using `pkill` |
| `8-beheaded_process` | Force-kills `7-highlander` using `SIGKILL` (signal 9), which cannot be caught |
| `10-process_and_pid_file` | Creates a PID file and handles `SIGTERM`, `SIGQUIT`, and `SIGINT` differently |
| `manage_my_process` | Background process that writes `I am alive!` to `/tmp/my_process` every 2 seconds |
| `11-manage_my_process` | Init-style manager for `manage_my_process` — supports `start`, `stop`, and `restart` |

## Key Concepts

**PID (Process ID)** — every running process has a unique numeric identifier. `$$` gives you the PID of the current script; `$!` gives you the PID of the last background process.

**Signals** — messages sent to processes to trigger specific behaviors. Common signals:

| Signal | Number | Default behavior |
|---|---|---|
| `SIGTERM` | 15 | Graceful termination (can be caught) |
| `SIGINT` | 2 | Interrupt from keyboard (Ctrl+C) |
| `SIGQUIT` | 3 | Quit with core dump |
| `SIGKILL` | 9 | Immediate kill (cannot be caught or ignored) |

**`trap`** — a Bash built-in that intercepts signals and runs custom code instead of the default action. Example:

```bash
trap "echo 'Caught SIGTERM'" SIGTERM
```

**PID files** — files (usually in `/var/run/`) that store a process's PID so other scripts can find and manage it later.

## Usage

Make all scripts executable and run them:

```bash
chmod u+x *

# Run the infinite loop in the background
./4-to_infinity_and_beyond &

# Then stop it
./5-dont_stop_me_now

# Manage a background process
./11-manage_my_process start
./11-manage_my_process restart
./11-manage_my_process stop
```

## Requirements

- Ubuntu / Linux environment
- Bash shell
- Scripts must be executable (`chmod u+x`)
- All scripts start with `#!/usr/bin/env bash`

## Setup Commands

```bash
cd /alche-shell
mkdir processes_and_signals
cd processes_and_signals
```

### Create each file

```bash
vi 0-what-is-my-pid          # echo "$$"
vi 1-list_your_processes     # ps auxf
vi 2-show_your_bash_pid      # ps auxf | grep bash  (+ shellcheck disable=SC2009)
vi 3-show_your_bash_pid_made_easy  # pgrep -l bash
vi 4-to_infinity_and_beyond  # while true; do echo "To infinity and beyond"; sleep 2; done
vi 5-dont_stop_me_now        # kill "$(pgrep -f 4-to_infinity_and_beyond)"
vi 6-stop_me_if_you_can      # pkill -f 4-to_infinity_and_beyond
vi 7-highlander              # trap "echo 'I am invincible!!!'" SIGTERM + while loop
vi 67-stop_me_if_you_can     # pkill -f 7-highlander
vi 8-beheaded_process        # kill -9 "$(pgrep -f 7-highlander)"
vi 10-process_and_pid_file   # echo "$$" > /var/run/myscript.pid + trap SIGTERM/SIGINT/SIGQUIT
vi manage_my_process         # while true; do echo "I am alive!"; sleep 2; done >> /tmp/my_process
vi 11-manage_my_process      # case start|stop|restart with PID file management
```

