# 📘 Notes: Shell Scripting

---

## 🧩 1. Hello World Shell Script

### 📄 Script: `hello_world.sh`

```bash
#!/bin/bash
# This is a simple shell script that prints "Hello, World!" to the terminal
echo "Hello, World!"
```

### 🧠 Explanation

* `#!` — **Shebang**, pronounced “sha-bang”.
* Tells the OS to execute the script using **/bin/bash** (Bash shell).
* `echo` prints text to the terminal.

### 🔹 Common Shebang Options

| Shebang               | Interpreter              | Use Case                                      |
| --------------------- | ------------------------ | --------------------------------------------- |
| `#!/bin/bash`         | Bash shell               | Most common; full-featured scripting shell.   |
| `#!/bin/sh`           | Basic shell (often dash) | POSIX-compliant; faster but limited features. |
| `#!/usr/bin/env bash` | Finds Bash via PATH      | Portable across systems.                      |
| `#!/bin/zsh`          | Z shell                  | For Zsh-specific scripts.                     |
| `#!/usr/bin/python3`  | Python                   | For Python scripts.                           |
| `#!/usr/bin/perl`     | Perl                     | For Perl scripting.                           |

---

## 🧮 2. Basic Shell Script Examples

### 🕓 Display Current Date & Time

```bash
#!/bin/bash
echo "Current date and time: $(date)"
```

### 💽 Check Disk Usage

```bash
#!/bin/bash
echo "Disk usage report:"
df -h
```

### 📁 List Files in Directory

```bash
#!/bin/bash
echo "Files in current directory:"
ls -l
```

### 📂 Check If a File Exists

```bash
#!/bin/bash
read -p "Enter filename: " file
if [ -f "$file" ]; then
  echo "File '$file' exists."
else
  echo "File '$file' does not exist."
fi
```

### ➕ Add Two Numbers

```bash
#!/bin/bash
read -p "Enter first number: " a
read -p "Enter second number: " b
sum=$((a + b))
echo "Sum: $sum"
```

### ⏱️ Print System Uptime

```bash
#!/bin/bash
echo "System has been up for:"
uptime -p
```

### 👤 Check User Login

```bash
#!/bin/bash
read -p "Enter username to check: " user
if id "$user" &>/dev/null; then
  echo "User '$user' exists."
else
  echo "User '$user' not found."
fi
```

### 📦 Backup a Directory

```bash
#!/bin/bash
read -p "Enter source directory: " src
read -p "Enter backup directory: " dest
tar -czf "$dest/backup_$(date +%F).tar.gz" "$src"
echo "Backup completed successfully!"
```

### 🌐 Check Internet Connectivity

```bash
#!/bin/bash
if ping -c 1 8.8.8.8 &>/dev/null; then
  echo "Internet is working."
else
  echo "No internet connection."
fi
```

---

## 🔠 String Operations in Bash

### 📏 Find Length of a String

```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Length of string: ${#str}"
```

**Concept:**
`${variable}` retrieves value. `${#variable}` returns string length.

---

### 🔤 Convert String to Uppercase

```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Uppercase: ${str^^}"
```

### 🔡 Convert String to Lowercase

```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Lowercase: ${str,,}"
```

### 🔁 Reverse a String

```bash
#!/bin/bash
read -p "Enter a string: " str
rev_str=$(echo "$str" | rev)
echo "Reversed string: $rev_str"
```

### 🔍 Compare Two Strings

```bash
#!/bin/bash
read -p "Enter first string: " str1
read -p "Enter second string: " str2
if [[ "$str1" == "$str2" ]]; then
  echo "Strings are equal."
else
  echo "Strings are not equal."
fi
```

### ✂️ Extract Substring

```bash
#!/bin/bash
read -p "Enter a string: " str
read -p "Enter starting position: " pos
read -p "Enter length: " len
echo "Substring: ${str:$pos:$len}"
```

---

## ⚙️ `/bin/sh` vs `/bin/bash`

| Feature     | `/bin/sh`            | `/bin/bash`                        |
| ----------- | -------------------- | ---------------------------------- |
| Name        | Bourne Shell         | Bourne Again Shell                 |
| Speed       | Faster (lightweight) | Slightly slower                    |
| Portability | Very high (POSIX)    | Common but not universal           |
| Features    | Basic                | Arrays, regex, `[[ ]]`, string ops |
| Best Use    | Simple scripts       | Full-featured automation           |

### ⚠️ Bash-only Features (not supported by sh)

* `${var^^}` → uppercase
* Arrays
* `[[ ... ]]` conditions

---

### 📘 Examples

**1️⃣ Uppercase Variable**

```bash
#!/bin/bash
name="vilas"
echo "Uppercase: ${name^^}"
```

→ Works in Bash
❌ Fails in sh with “Bad substitution”.

**2️⃣ Arrays**

```bash
#!/bin/bash
colors=("red" "green" "blue")
echo "First: ${colors[0]}"
echo "All: ${colors[@]}"
```

❌ `/bin/sh` doesn’t support arrays.

**3️⃣ Conditional Expressions**

```bash
#!/bin/bash
str="devops"
if [[ $str == devops ]]; then
  echo "Match"
fi
```

✅ Works in Bash
❌ `/bin/sh`: `[: not found`

**4️⃣ Arithmetic (POSIX-compliant)**

```bash
#!/bin/sh
echo $((3 + 4))
```

✅ Works in both sh and bash.

---

### 🧭 Practical Guidelines

| Scenario                            | Recommended Shell | Why                     |
| ----------------------------------- | ----------------- | ----------------------- |
| Simple portable scripts             | `/bin/sh`         | Works everywhere        |
| Scripts using arrays, regex, colors | `/bin/bash`       | Bash features           |
| Automation scripts (DevOps)         | `/bin/bash`       | Default on most distros |
| Minimal systems (e.g., Alpine)      | `/bin/sh`         | Lightweight             |

---

## 👤 User Creation Script

### 🧾 Basic Version

```bash
#!/bin/bash
USERNAME="$1"
GROUPNAME="$2"
groupadd "$GROUPNAME"
useradd -m -s /bin/bash -g "$GROUPNAME" "$USERNAME"
echo "echo \"Welcome, $USERNAME!!\"" >> /home/$USERNAME/.bashrc
echo "Setup complete! When '$USERNAME' logs in, they'll see a welcome message."
```

### ✅ Standard Version (`create_user.sh`)

```bash
#!/bin/bash
# ===============================================
# Script Name: create_user.sh
# Description: Create a new Linux user and group
# ===============================================

set -e  # Exit on error

if [[ $EUID -ne 0 ]]; then
  echo "This script must be run as root"
  exit 1
fi

if [[ $# -ne 2 ]]; then
  echo "Usage: $0 <username> <groupname>"
  exit 1
fi

USERNAME="$1"
GROUPNAME="$2"

if getent group "$GROUPNAME" >/dev/null; then
  echo "Group '$GROUPNAME' already exists."
else
  groupadd "$GROUPNAME"
  echo "Group '$GROUPNAME' created."
fi

if id "$USERNAME" &>/dev/null; then
  echo "User '$USERNAME' already exists."
else
  useradd -m -s /bin/bash -g "$GROUPNAME" "$USERNAME"
  echo "User '$USERNAME' created with home /home/$USERNAME"
fi

read -p "Set password for $USERNAME? (y/n): " choice
if [[ "$choice" =~ ^[Yy]$ ]]; then
  passwd "$USERNAME"
fi

echo "✅ User setup completed successfully!"
```

---

## 🧰 Assignment: Project Setup Utility

### 🗂️ Objective

Create reusable scripts that generate uniquely named project directories using timestamps.

### ⚙️ `utils.sh`

```bash
#!/bin/bash
create_timestamped_dir() {
  local project_name="$1"
  if [ -z "$project_name" ]; then
    echo "Error: Project name not provided."
    return 1
  fi

  local timestamp
  timestamp=$(date +%Y%m%d-%H%M%S)
  local dir_path="/tmp/${project_name}-${timestamp}"

  mkdir -p "$dir_path"
  echo "Directory created: $dir_path"
}
```

### 🚀 `setup_project.sh`

```bash
#!/bin/bash
source "$(dirname "$0")/utils.sh"
create_timestamped_dir "my-new-app"
```

**Outcome:**

* Practice **functions**, **argument handling**, **sourcing**, and **timestamped directory creation**.

---

## 🔐 Access Control Lists (ACLs)

### 1️⃣ What are ACLs?

ACLs allow **fine-grained file permissions** — multiple users or groups can have distinct access.

| Traditional                  | ACL                                                 |
| ---------------------------- | --------------------------------------------------- |
| One owner, one group, others | Individual users/groups can have custom permissions |

---

### 2️⃣ Check if ACLs are Enabled

```bash
mount | grep acl
sudo mount -o remount,acl /home
```

Add `acl` to `/etc/fstab` for persistence.

---

### 3️⃣ View ACLs

```bash
getfacl filename
```

Example output:

```
# file: project.txt
user::rw-
user:john:r--
group::r--
mask::r--
other::---
```

---

### 4️⃣ Set ACLs

```bash
setfacl -m u:john:r-- project.txt
setfacl -m g:developers:rw- project.txt
```

---

### 5️⃣ Remove ACLs

```bash
setfacl -x u:john project.txt   # Remove one
setfacl -b project.txt          # Remove all
```

---

### 6️⃣ Directory ACLs (Recursive)

```bash
setfacl -R -m u:john:rwX /var/www/
```

* `-R` → Recursive
* `X` → Execute only for directories

---

### 7️⃣ Default ACLs

```bash
setfacl -d -m u:john:rwX /var/www/
```

Applies automatically to newly created files under `/var/www/`.

---

### 8️⃣ Mask Field

Defines **max allowed permissions** for users/groups (except owner).
Example:

```bash
setfacl -m m::r-- file.txt
```

---

### 9️⃣ Automate ACLs via Script

```bash
#!/bin/bash
SHARE_DIR="/data/team"
USER_LIST="alice bob charlie"

for user in $USER_LIST; do
  setfacl -m u:$user:rwx $SHARE_DIR
done
```

Make executable:

```bash
chmod +x set_team_acl.sh
```

---

## 🏁 Summary

* **Bash scripting** enables automation of repetitive tasks.
* Understand **shebang**, **variables**, **conditionals**, **functions**, and **string manipulation**.
* ACLs offer **fine-grained access control** beyond basic UNIX permissions.
* Follow best practices: use `#!/bin/bash`, error handling (`set -e`), and comments for clarity.

💬 *Shell scripting mastery = Automation + Productivity + Control.*

