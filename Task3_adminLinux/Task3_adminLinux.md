# Linux Administration - Task 3  
<img src="../Task1_adminLinux/linux_logo.png" width="200" height="150" align="center">

<img src="./task3.png">

## C Program to Adjust Brightness

**Program Description:**
- This C program adjusts screen brightness by writing values to the `brightness` file under `/sys/class/backlight/intel_backlight/`.
- The program accepts percentage values as command-line arguments: `0%`, `25%`, `50%`, `75%`, or `100%`.
- Brightness values are mapped to corresponding numeric values used by the system.

**Code:**
```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <string.h>
#include <unistd.h>
#include <stdlib.h>

int main(int argc, char* argv[]) {
    // File path to the brightness control
    const char *brightness_file = "/sys/class/backlight/intel_backlight/brightness";
    
    // Open the brightness file
    int fd = open(brightness_file, O_RDWR);
    if (fd == -1) {
        perror("Error opening brightness file");
        return 1;
    }

    // Check if argument is passed
    if (argc > 1) {
        // Map percentage arguments to brightness values
        if (strcmp(argv[1], "0%") == 0) {
            dprintf(fd, "%s", "1200");
            printf("Brightness set to 0% → 1200\n");
        } else if (strcmp(argv[1], "25%") == 0) {
            dprintf(fd, "%s", "30900");
            printf("Brightness set to 25% → 30900\n");
        } else if (strcmp(argv[1], "50%") == 0) {
            dprintf(fd, "%s", "60600");
            printf("Brightness set to 50% → 60600\n");
        } else if (strcmp(argv[1], "75%") == 0) {
            dprintf(fd, "%s", "90300");
            printf("Brightness set to 75% → 90300\n");
        } else if (strcmp(argv[1], "100%") == 0) {
            dprintf(fd, "%s", "120000");
            printf("Brightness set to 100% → 120000\n");
        } else {
            printf("Invalid argument. Use 0%, 25%, 50%, 75%, or 100% to set brightness.\n");
        }
    } else {
        printf("No argument provided. Usage: sudo ./bc <0%|25%|50%|75%|100%>\n");
    }

    // Close the file descriptor
    close(fd);
    return 0;
}
```

<img src="./bt_50.png">

<img src="./bt_75.png">

---

## Bash Script to Modify .bashrc

**Script Description:**
- This script checks if the `.bashrc` file exists in the user's home directory.
- If it exists, it appends environment variables to the file:
  - `HELLO` with the value of the system's hostname.
  - `LOCAL` with the current username using the `whoami` command.
- It also includes a command to open a new terminal.

**Script:**
```bash
#!/bin/bash

# Check if .bashrc exists in the user's home directory
if [ -f "$HOME/.bashrc" ]; then
    echo "# Appended environment variables" >> "$HOME/.bashrc"
    echo "export HELLO=\"$(hostname)\"" >> "$HOME/.bashrc"
    echo "export LOCAL=\"$(whoami)\"" >> "$HOME/.bashrc"
    echo "# Open new terminal" >> "$HOME/.bashrc"
    echo "gnome-terminal &" >> "$HOME/.bashrc"
    echo ".bashrc updated successfully!"
else
    echo "The .bashrc file does not exist in the home directory."
fi
```

**What Happens When the Terminal Opens:**
- The new terminal automatically loads the `.bashrc` file.
- The `HELLO` variable will hold the system's hostname.
- The `LOCAL` variable will display the username.
- Any appended environment variables will be accessible in the new terminal.

<img src="./env_script.png">

---

## Questions

1. [Commands and Redirection](#1-commands-and-redirection)  
2. [Profile Editing and Prompt Change](#2-profile-editing-and-prompt-change)  
3. [File and Command Operations](#3-file-and-command-operations)
4. [File Search and Links](#4-file-search-and-links)
5. [Echo Prompt Issue](#5-echo-prompt-issue)

## Answers

### 1. Commands and Redirection

1. **List the user commands and redirect the output to `/tmp/commands.list`:**
   ```bash
   history > /tmp/commands.list
   ```

   <img src="./cmds_hist.png">

2. **Count the number of user commands:**
   ```bash
   wc -l /tmp/commands.list
   ```

   <img src="./num_usr_cmds.png">

   ```bash
   history | wc -l
   ```

   <img src="./numOfWords_hist.png">

---

### 2. Profile Editing and Prompt Change

1. **Edit your profile to display the date at login:**
   Add the following line to your `.profile` file:
   ```bash
   echo "Today's date: $(date)"
   ```

   Then, apply changes using, and it will only appear on running this:
   ```bash
   source ~/.profile
   ```

   <img src="./profile.png">

   <img src="./source_profile.png">

2. **Change your prompt permanently:**
   Add the following line to your `.bashrc` file:
   ```bash
   echo "Today's date: $(date)"
   ```
   Then, apply changes using:
   ```bash
   source ~/.bashrc
   ```

   <img src="./bashrc.png">

   <img src="./bashrc2.png">

---

### 3. File and Command Operations

1. **Count the words in a file or the number of files in a directory:**
   - To count words in a file:
     ```bash
     wc -w filename
     ```

     <img src="./numOfWords_in_usr_cmds.png">

   - To count the number of files in a directory:
     ```bash
     ls | wc -l
     ```

     <img src="./numOfFiles_dir.png">

2. **What happens if you execute:**
   - `cat filename1 | cat filename2`
     - This will concatenate `filename2` to the output of `filename1`.
   - `ls | rm`
     - **This will fail** because `rm` expects filenames as arguments, not input from `ls`.
   - `ls /etc/passwd | wc -l`
     - The command incorrectly applies `ls` to a single file, resulting in an output of `1`.

---

### 4. File Search and Links

1. **Search for all files named `.profile`:**
   ```bash
   sudo find / -name ".profile"
   ```

<img src="./search.png">

2. **List the inode numbers of `/`, `/etc`, `/etc/hosts`:**
   ```bash
   ls -i /
   ls -i /etc
   ls -i /etc/hosts
   ```

   <img src="./inode 1.png">
   <img src="./inode 2.png">
   <img src="./inode 3.png">

3. **Create a symbolic link of `/etc/passwd` in `/boot`:**
   ```bash
   ln -s /etc/passwd /boot/passwd_link
   ```

   <img src="./passwd_link.png">

4. **Create a hard link of `/etc/passwd` in `/boot`:**
   - **Cannot be done** because `/boot` is typically on a separate filesystem, and hard links cannot cross filesystem boundaries.

---

### 5. Echo Prompt Issue

1. **Execute the following command:**
   ```bash
   echo \
   ```
   **Explanation:**
   - The backslash (`\`) at the end of a line serves as a line continuation character.
   - The shell displays the `>` prompt, indicating it expects further input.

2. **Change the prompt from `>` to `:` when the `\` causes continuation:**
   Use the `PS2` variable to change the continuation prompt:
   ```bash
   export PS2=": "
   ```

---

*Ali Mohamed Ali Elmansoury*  
*ITI Embedded Systems - Intake 45*