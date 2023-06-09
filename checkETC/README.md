# script used to check etc dir and make log file of changes

```md
directory_changes.sh


#!/bin/bash

# Set the directory path you want to monitor
DIR="/etc"

# Set the log directory path to store the changes
LOG_DIR="/home/group-e/files"

# Check if the log directory exists, otherwise create it
if [ ! -d "$LOG_DIR" ]; then
    mkdir -p "$LOG_DIR"
fi

# Set the log file path
LOG_FILE="$LOG_DIR/changes.log"

# Get the current timestamp
timestamp=$(date +"%Y-%m-%d %T")

# Find files in the directory modified within the last 8 hours
find "$DIR" -type f -mmin -480 -print0 | while IFS= read -r -d '' file; do
    echo "$timestamp: Modified: $file" >> "$LOG_FILE"
done

# Check the number of files in the log directory and delete the oldest file if there are six files
num_files=$(ls -l "$LOG_DIR" | grep -c '^-' )
if [ "$num_files" -gt 6 ]; then
    oldest_file=$(ls -rt "$LOG_DIR" | head -1)
    rm "$LOG_DIR/$oldest_file"
fi
```
