# ssh-mosh-time-logger# 
# SSH & Mosh Session Time Logger

A simple script designed to automatically log the duration of your SSH and Mosh sessions. It integrates seamlessly into your workflow by aliasing the standard `ssh` and `mosh` commands. Compatible with Bash and Zsh on macOS and Linux environments. **Now also prints the session duration to the terminal upon exit.**

## Features

-   **Automatic Logging:** Tracks the time spent in each `ssh` and `mosh` session.
-   **Terminal Output:** Prints the duration of the completed session directly to your terminal.
-   **Transparent Operation:** Works behind the scenes without changing how you use `ssh` or `mosh`.
-   **Simple Data Format:** Stores logs in a straightforward CSV file (`~/.ssh_mosh_session_log`) for easy viewing and analysis.
-   **Compatibility:** Works with both Bash and Zsh shells.

## Requirements

-   Bash or Zsh shell
-   Standard Unix utilities (`date`, `command`, `awk`)

## Installation

You need to add a function and aliases to your shell's configuration file. Choose the instructions based on the shell you use.

### For Zsh (Common on macOS)

1.  Open your Zsh configuration file:
    ```bash
    nano ~/.zshrc
    ```
2.  Add the following lines to the file:
    ```zsh
    # SSH/Mosh Time Logger
    LOGFILE=~/.ssh_mosh_session_log
    ssh_mosh_logger() {
        local cmd="$1"
        shift # Remove the command name (ssh/mosh) from arguments
        local start_time=$(date +%s)
        # Execute the original command (ssh or mosh) with its arguments
        command "$cmd" "$@"
        local end_time=$(date +%s)
        local duration=$((end_time - start_time))
        # Log: Timestamp, Full Command, Duration (seconds)
        echo "$(date '+%Y-%m-%d %H:%M:%S'),${cmd} $*,${duration}" >> "$LOGFILE"
        # --- Added Line Below ---
        # Output the duration to the terminal
        echo "Session finished. Duration: ${duration} seconds."
    }
    # Alias ssh and mosh to use the logger function
    alias ssh='ssh_mosh_logger ssh'
    alias mosh='ssh_mosh_logger mosh'
    ```
3.  Save the file (Ctrl+O, Enter) and exit nano (Ctrl+X).
4.  Apply the changes to your current session:
    ```bash
    source ~/.zshrc
    ```

### For Bash (Common on Linux)

1.  Open your Bash configuration file:
    ```bash
    nano ~/.bashrc
    ```
2.  Add the following lines to the file:
    ```bash
    # SSH/Mosh Time Logger
    LOGFILE=~/.ssh_mosh_session_log
    ssh_mosh_logger() {
        local cmd="$1"
        shift # Remove the command name (ssh/mosh) from arguments
        local start_time=$(date +%s)
        # Execute the original command (ssh or mosh) with its arguments
        command "$cmd" "$@"
        local end_time=$(date +%s)
        local duration=$((end_time - start_time))
        # Log: Timestamp, Full Command, Duration (seconds)
        echo "$(date '+%Y-%m-%d %H:%M:%S'),${cmd} $*,${duration}" >> "$LOGFILE"
        # --- Added Line Below ---
        # Output the duration to the terminal
        echo "Session finished. Duration: ${duration} seconds."
    }
    # Alias ssh and mosh to use the logger function
    alias ssh='ssh_mosh_logger ssh'
    alias mosh='ssh_mosh_logger mosh'
    ```
3.  Save the file (Ctrl+O, Enter) and exit nano (Ctrl+X).
4.  Apply the changes to your current session:
    ```bash
    source ~/.bashrc
    ```

## Usage

Simply use the `ssh` and `mosh` commands as you normally would. The logging happens automatically when the session ends. When you exit an SSH or Mosh session, you will now see a message printed in your terminal indicating the session's duration.

```bash
ssh user@your-server.com
# ... do work ...
# exit
# Output will appear: Session finished. Duration: 123 seconds.
```

```
mosh user@another-server.com
# ... do work ...
# exit
# Output will appear: Session finished. Duration: 456 seconds.
```

## Viewing Logs
The session logs are still stored in `~/.ssh_mosh_session_log`. 
You can view the raw log data using cat or other text tools:
`cat ~/.ssh_mosh_session_log`
### Log Format
Each line in the log file is a CSV record with the following format:YYYY-MM-DD HH:MM:SS,command arguments,duration_in_secondsExample:2025-04-08 15:30:15,ssh user@your-server.com,125
2025-04-08 15:45:02,mosh user@another-server.com,3600
### Calculating Total Time
To calculate the cumulative total time spent across all logged sessions (in seconds), you can still use awk:
```awk -F',' '{sum += $3} END {print "Total time logged (seconds):", sum}' ~/.ssh_mosh_session_log```

Contributing
Feel free to suggest improvements or report
