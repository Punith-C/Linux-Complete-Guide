# Complete Linux Guide

---

## Table of Contents

1. [What is Linux?](#1-what-is-linux)
2. [The Linux Filesystem](#2-the-linux-filesystem)
3. [The Command Line](#3-the-command-line)
4. [File Permissions](#4-file-permissions)
5. [Text Processing](#5-text-processing)
6. [Pipes & Redirection](#6-pipes--redirection)
7. [Process Management](#7-process-management)
8. [Users & Groups](#8-users--groups)
9. [Package Management](#9-package-management)
10. [Networking](#10-networking)
11. [Storage & Filesystems](#11-storage--filesystems)
12. [Services & systemd](#12-services--systemd)
13. [Shell Scripting](#13-shell-scripting)
14. [Security](#14-security)
15. [System Administration](#15-system-administration)
16. [Advanced Topics](#16-advanced-topics)
17. [Cheat Sheet](#17-cheat-sheet)

---

## 1. What is Linux?

Linux is an **operating system** — just like Windows or macOS — that runs everything from your phone to supercomputers. Unlike those, Linux is:

| Feature | Description |
|---------|-------------|
| **Free** | Costs nothing to download, use, or share |
| **Open Source** | Anyone can look at, modify, or improve the code |
| **Flexible** | Runs on everything from a Raspberry Pi to a supercomputer |
| **Ubiquitous** | 96% of web servers, 100% of top 500 supercomputers, most of the internet |

### The Linux Philosophy

```
📁 Everything is a file     — hardware, processes, configuration, devices
🔧 Small, single-purpose    — each tool does one thing well
🔗 Chain programs together  — pipes connect small tools for complex tasks
📝 Text is universal        — configuration is text files
🧠 You decide               — configuration over convention
```

### Distributions (Distros)

A distro = Linux kernel + package manager + desktop environment + applications

| Distro | Base | Package Manager | Best For |
|--------|------|----------------|----------|
| **Ubuntu** | Debian | `apt` | Beginners, general desktop |
| **Debian** | — | `apt` | Stability, servers |
| **Fedora** | — | `dnf` | Cutting edge, Red Hat ecosystem |
| **CentOS/RHEL** | — | `yum`/`dnf` | Enterprise servers |
| **Mint** | Ubuntu | `apt` | Windows refugees, beginners |
| **Kali** | Debian | `apt` | Penetration testing |
| **Arch** | — | `pacman` | Customization, learning |
| **openSUSE** | — | `zypper` | System administrators |

> 💡 **If you're new:** Start with **Ubuntu** or **Mint**. You can always switch later — they all share the same core concepts.

---

## 2. The Linux Filesystem

### The Tree Structure

Everything starts at `/` (root). **There are NO drive letters** like `C:` or `D:`.

```
/                              → Root of everything
├── bin → /usr/bin             → Essential binary commands (symlink on modern systems)
├── boot                       → Boot loader, kernel, initramfs
│   ├── vmlinuz-linux          → The Linux kernel itself
│   ├── initramfs-linux.img    → Initial RAM filesystem
│   └── grub/                  → GRUB bootloader configuration
├── dev                        → Device files (hardware interfaces)
│   ├── sda                    → First SATA disk
│   ├── sda1                   → First partition on first disk
│   ├── tty0                   → Terminal device
│   ├── null                   → /dev/null (discards all data)
│   ├── zero                   → Produces null bytes
│   └── random                 → Random number generator
├── etc                        → System-wide configuration files
│   ├── passwd                 → User accounts
│   ├── shadow                 → Encrypted passwords
│   ├── group                  → User groups
│   ├── fstab                  → Filesystem mount table
│   ├── hosts                  → Hostname-to-IP mappings
│   ├── hostname               → System hostname
│   ├── resolv.conf            → DNS servers
│   ├── ssh/                   → SSH server configuration
│   ├── nginx/                 → Nginx web server config
│   ├── systemd/               → Systemd unit files
│   ├── cron.d/                → Cron jobs
│   ├── sudoers                → Sudo permissions
│   └── apt/ (Ubuntu/Debian)   → APT package manager config
├── home                       → User home directories
│   └── username/              → Each user gets their own folder
│       ├── Documents/
│       ├── Downloads/
│       ├── .bashrc            → Bash configuration
│       ├── .profile           → Login shell configuration
│       ├── .ssh/              → SSH keys
│       └── .local/            → User-local programs
├── lib → /usr/lib             → Shared libraries (symlink)
├── media                      → Removable media mount points
│   └── username/
│       ├── CDROM/
│       └── USB_DRIVE/
├── mnt                        → Temporary mounts (manual)
├── opt                        → Optional/third-party software
│   └── google-chrome/         → Example: Chrome installs here
├── proc                       → Virtual filesystem (running processes)
│   ├── cpuinfo                → CPU information
│   ├── meminfo                → Memory information
│   ├── uptime                 → System uptime
│   └── 1234/                  → Process #1234 info
├── root                       → Root user's home directory
├── run                        → Runtime variable data (tmpfs)
│   └── user/1000/             → Per-user runtime directory
├── sbin → /usr/sbin           → System binaries (root-only commands)
├── srv                        → Service data (HTTP, FTP servers)
├── sys                        → Virtual filesystem (kernel info)
├── tmp                        → Temporary files (deleted on reboot)
├── usr                        → User system resources (second root)
│   ├── bin/                   → User commands (most programs here)
│   ├── lib/                   → Libraries
│   ├── local/                 → Locally compiled software
│   │   ├── bin/
│   │   └── lib/
│   ├── share/                 → Shared data (man pages, icons, docs)
│   └── src/                   → Source code (sometimes)
└── var                        → Variable data (changes during operation)
    ├── log/                   → Log files
    │   ├── syslog             → System log
    │   ├── auth.log           → Authentication logs
    │   ├── kern.log           → Kernel logs
    │   └── nginx/             → Web server access/error logs
    ├── cache/                 → Application cache data
    ├── lib/                   → Dynamic state data
    ├── spool/                 → Print/email queues
    ├── tmp/                   → Temporary files (persist across reboot)
    └── www/                   → Web server document root (common)
```

### Understanding Mount Points

```bash
# See what's mounted
df -h
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1       234G   45G  189G  19% /
# /dev/sdb1       1.8T  300G  1.5T  17% /data

# Mount/Unmount
mount /dev/sdb1 /mnt/data
umount /mnt/data
umount -l /mnt/data      # Lazy unmount (when device is busy)

# Find drive UUIDs (unique identifiers)
blkid
ls -l /dev/disk/by-uuid/

# Permanent mounts — /etc/fstab
cat /etc/fstab
# Format: <device>  <mountpoint>  <fstype>  <options>  <dump>  <pass>
# UUID=abc123  /  ext4  defaults  0  1
```

---

## 3. The Command Line

### Getting Started

```bash
# Your prompt
username@computer:~$
# ^user    ^machine  ^location  ^$=user, #=root

# Basic commands
whoami            # Who are you?
pwd               # Where are you? (Print Working Directory)
ls                # What's here?
ls -la            # Detailed list with hidden files
cd /path          # Change directory
cd ~              # Go home
cd -              # Go to previous directory

# Getting help
man ls            # Manual for any command
ls --help         # Quick help
man -k "search"   # Search manuals
```

### Navigation

```bash
pwd                     # Print Working Directory
ls -l                   # Long format
ls -a                   # Show hidden files (.files)
ls -la                  # Long + hidden
ls -lh                  # Human-readable sizes
ls -lS                  # Sort by size
ls -lt                  # Sort by date

cd /path/to/dir         # Change directory
cd ~                    # Go home
cd -                    # Go to previous directory
cd ..                   # Parent directory
cd ../..                # Grandparent directory

tree                    # Directory structure (install: apt install tree)
tree -L 2               # Limit depth
tree -d                 # Directories only
```

### Creating, Copying, Moving, Deleting

```bash
# CREATE
touch file.txt                  # Empty file
mkdir folder                    # Directory
mkdir -p a/b/c/d                # Nested directories
echo "hello" > file.txt         # File with content (OVERWRITES)
echo "hello" >> file.txt        # File with content (APPENDS)

# COPY
cp source.txt dest.txt          # Copy file
cp -r source/ dest/             # Copy directory recursively
cp -a source/ dest/             # Archive (preserve permissions)
cp -i source dest               # Interactive (ask before overwrite)

# MOVE / RENAME
mv old.txt new.txt              # Rename
mv file.txt /target/dir/        # Move
mv *.txt target/                # Move all .txt files

# DELETE (PERMANENT — no trash bin!)
rm file.txt                     # Delete file
rm -i file.txt                  # Interactive
rm -f file.txt                  # Force (no confirmation)
rm -r folder/                   # Delete directory + contents
rm -rf folder/                  # Force recursive delete ⚠️
```

### Viewing & Editing Files

```bash
# VIEWING
cat file.txt                    # Print entire file
cat -n file.txt                 # With line numbers
less file.txt                   # Page through (q=quit, /=search)
head file.txt                   # First 10 lines
head -n 50 file.txt             # First 50 lines
tail file.txt                   # Last 10 lines
tail -f log.txt                 # Follow (live updates)
wc file.txt                     # Lines, words, characters
wc -l file.txt                  # Lines only

# EDITING
nano file.txt                   # Simple editor
vim file.txt                    # Modal editor
code file.txt                   # VS Code (GUI)
```

---

## 4. File Permissions

### Reading Permissions

```bash
ls -l
# -rwxr-xr-x  1  alice  developers  4096  Jun 15 10:00  script.sh
# ^^^^^^^^^^  ^  ^^^^^  ^^^^^^^^^^
# permissions |  owner  group
# |
# file type: -=file, d=directory, l=symlink
```

**Permission values:**

| Value | Binary | Meaning |
|-------|--------|---------|
| `r` = 4 | 100 | Read |
| `w` = 2 | 010 | Write |
| `x` = 1 | 001 | Execute |
| `7` | 111 | `rwx` — all permissions |
| `6` | 110 | `rw-` — read + write |
| `5` | 101 | `r-x` — read + execute |
| `4` | 100 | `r--` — read only |
| `0` | 000 | `---` — no permissions |

### Changing Permissions

```bash
# SYMBOLIC MODE
chmod u+x script.sh         # Add execute for owner
chmod g+w file.txt          # Add write for group
chmod o-r file.txt          # Remove read for others
chmod a+x script.sh         # Add execute for everyone
chmod u=rwx,g=rx,o=r file  # Set exact permissions

# NUMERIC MODE (octal)
chmod 755 script.sh         # rwxr-xr-x (standard for scripts)
chmod 644 file.txt          # rw-r--r-- (standard for files)
chmod 600 private.txt       # rw------- (private files)
chmod 700 folder/           # rwx------ (private directory)
chmod 400 key.pem           # r-------- (SSH keys)

# OWNERSHIP
sudo chown alice file.txt               # Change owner
sudo chown alice:developers file.txt    # Change owner and group
sudo chown -R alice:alice /home/alice   # Recursive

# SPECIAL PERMISSIONS
chmod u+s script.sh         # SUID — runs as file owner
chmod g+s directory/        # SGID — files inherit group
chmod +t /tmp/              # Sticky bit — owner-only deletion
```

---

## 5. Text Processing

### grep — Find Text

```bash
grep "pattern" file.txt               # Search in file
grep -i "pattern" file.txt             # Case-insensitive
grep -r "password" /etc/               # Recursive search
grep -n "error" log.txt                # Show line numbers
grep -v "exclude" file.txt             # Lines WITHOUT pattern
grep -c "error" log.txt                # Count matches
grep -l "TODO" *.py                    # Show filenames only
grep -A5 "error" log.txt               # 5 lines AFTER match
grep -B5 "error" log.txt               # 5 lines BEFORE match
grep -C5 "error" log.txt               # 5 lines BEFORE and AFTER

# Regular expressions
grep "^start" file.txt                 # Lines starting with "start"
grep "end$" file.txt                   # Lines ending with "end"
grep -E "error|warning|fail" log.txt   # OR
grep -P "\d{3}-\d{3}-\d{4}" file.txt  # Phone numbers
```

### sort, uniq, cut

```bash
sort file.txt                          # Alphabetical sort
sort -n file.txt                       # Numerical sort
sort -r file.txt                       # Reverse sort
sort -u file.txt                       # Sort + remove duplicates

sort file.txt | uniq -c                # Count occurrences
sort file.txt | uniq -d                # Show only duplicates

cut -d: -f1 /etc/passwd                # Usernames (colon-delimited)
cut -c1-10 file.txt                    # First 10 characters
```

### sed — The Stream Editor

```bash
sed 's/old/new/' file.txt              # Replace first occurrence per line
sed 's/old/new/g' file.txt             # Replace ALL occurrences
sed -i 's/old/new/g' file.txt          # Edit in place
sed -i.bak 's/old/new/g' file.txt      # Edit + backup

sed -n '5,10p' file.txt                # Print lines 5-10
sed '/pattern/d' file.txt              # Delete matching lines
sed '/^$/d' file.txt                   # Delete empty lines
sed 's/^/PREFIX: /' file.txt           # Add prefix to each line
```

### awk — Text Processing Language

```bash
awk '{print $1}' file.txt              # First field
awk '{print $1, $3}' file.txt          # Fields 1 and 3
awk -F: '{print $1}' /etc/passwd       # Colon-delimited

awk '/error/ {print}' log.txt          # Lines matching pattern
awk '$2 > 100 {print}' data.txt        # Where field 2 > 100
awk '{sum+=$1} END {print sum}' nums.txt  # Sum column
awk '{count++} END {print count}' file.txt  # Count lines
```

---

## 6. Pipes & Redirection

### Redirection

```bash
# OUTPUT
command > file               # STDOUT → file (OVERWRITE)
command >> file              # STDOUT → file (APPEND)
command 2> file              # STDERR → file
command &> file              # BOTH stdout + stderr → file

# INPUT
command < file               # Read stdin from file
command << EOF               # Here-document (inline input)
> line 1
> line 2
> EOF

# DISCARD
command > /dev/null          # Discard stdout
command 2> /dev/null         # Discard errors
```

### Pipes (`|`)

The pipe sends output of one command to input of another:

```bash
# Classic examples
ps aux | grep firefox                          # Find process
cat log.txt | grep "ERROR" | tail -50          # Last 50 errors
history | awk '{print $2}' | sort | uniq -c | sort -rn | head -10  # Top 10 commands
ls -la | sort -k5 -n                           # Files sorted by size
cat file.txt | tr -s ' ' '\n' | sort | uniq -c | sort -rn | head   # Word frequency
```

### tee — Split Output

```bash
command | tee output.txt               # Show on screen AND save to file
command | tee -a output.txt            # Append to file
command | tee output.txt | grep pattern  # Save full, filter screen
```

---

## 7. Process Management

### Viewing Processes

```bash
ps                           # Your processes
ps aux                       # ALL processes
ps aux --sort=-%cpu          # Sorted by CPU
ps aux --sort=-%mem          # Sorted by memory
pstree                       # Tree view

top                          # Interactive viewer (q=quit)
htop                         # Better top (install: apt install htop)
```

### Managing Processes

```bash
# Signals
kill PID                     # SIGTERM (ask to stop nicely)
kill -9 PID                  # SIGKILL (force kill — last resort)
kill -1 PID                  # SIGHUP (reload config)
pkill process_name           # Kill by name
killall process_name         # Kill all with that name

# Background/Foreground
command &                    # Run in background
Ctrl+Z                       # Suspend current process
bg                           # Resume in background
fg                           # Bring to foreground
jobs                         # List background jobs

# Persistent processes
nohup command &              # Keep running after logout
nohup command > output.log 2>&1 &
```

### Screen & tmux

```bash
# Screen
screen -S work               # Start session
Ctrl+A D                     # Detach
screen -ls                   # List sessions
screen -r work               # Reattach

# tmux
tmux new -s work             # Start session
tmux ls                      # List sessions
tmux attach -t work          # Reattach
```

---

## 8. Users & Groups

### User Management

```bash
# View info
whoami                       # Current username
id                           # UID, GID, groups
who                          # Who is logged in
w                            # Who + what they're doing
last                         # Login history

# Create user
sudo useradd -m -s /bin/bash username
sudo passwd username
sudo usermod -aG sudo username     # Give sudo access

# Modify user
sudo usermod -aG docker username   # Add to group
sudo usermod -l newname oldname    # Change username
sudo usermod -L username           # Lock account
sudo usermod -U username           # Unlock account

# Delete user
sudo userdel username              # Delete user
sudo userdel -r username           # Delete + home directory
```

### Groups

```bash
groups username                 # User's groups
getent group                    # All groups
sudo groupadd developers        # Create group
sudo groupdel developers        # Delete group
sudo gpasswd -a user group      # Add user to group
sudo gpasswd -d user group      # Remove user from group
```

### sudo

```bash
sudo command                    # Run as root
sudo -i                         # Root shell (login)
sudo -u username command        # Run as specific user
sudo -l                         # List your privileges

# Edit sudoers (use visudo!)
sudo visudo
# Common entries:
# username  ALL=(ALL:ALL) ALL
# %wheel    ALL=(ALL:ALL) ALL
# username  ALL=(ALL) NOPASSWD: ALL
```

---

## 9. Package Management

### Debian/Ubuntu (apt)

```bash
sudo apt update                    # Refresh package database
sudo apt upgrade                   # Upgrade all packages
sudo apt install firefox           # Install package
sudo apt remove firefox            # Remove (keep config)
sudo apt purge firefox             # Remove + config files
sudo apt autoremove                # Remove orphans
apt search "text editor"           # Search
apt show firefox                   # Show package info
apt list --installed               # List installed
apt list --upgradable              # List upgradable
sudo apt --fix-broken install      # Fix broken dependencies
```

### Fedora/RHEL (dnf)

```bash
sudo dnf install firefox
sudo dnf remove firefox
sudo dnf upgrade
sudo dnf search "text editor"
sudo dnf info firefox
sudo dnf list installed
sudo dnf autoremove
```

### Arch (pacman)

```bash
sudo pacman -S firefox              # Install
sudo pacman -Rs firefox             # Remove + dependencies
sudo pacman -Syu                    # Full system update
pacman -Ss "text editor"            # Search
pacman -Qe                          # Explicitly installed
```

### Universal: Snap & Flatpak

```bash
# Snap
sudo snap install firefox
sudo snap remove firefox
snap list
snap refresh

# Flatpak
flatpak install flathub firefox
flatpak remove firefox
flatpak list
flatpak update
```

---

## 10. Networking

### Configuration

```bash
# IP info
ip addr                            # Show all IPs
ip -br addr                        # Brief output
ip link                            # Show interfaces
ip route                           # Routing table

# Configure
sudo ip link set eth0 up           # Enable interface
sudo ip addr add 192.168.1.100/24 dev eth0  # Set static IP
sudo ip route add default via 192.168.1.1   # Default gateway

# DNS
cat /etc/resolv.conf               # DNS servers
# nameserver 8.8.8.8

# NetworkManager
nmcli device status
nmcli device wifi list
nmcli device wifi connect "SSID" password "pass"
nmcli connection show
```

### Diagnostics

```bash
ping -c 4 8.8.8.8                  # Test connectivity
traceroute google.com               # Show route to host
mtr 8.8.8.8                        # Continuous traceroute

dig google.com                     # DNS lookup
dig +short google.com              # Brief output
dig -x 8.8.8.8                     # Reverse DNS
nslookup google.com                # Simple DNS

curl -I https://example.com        # HTTP headers
curl -o /dev/null -s -w "%{http_code}\n" url  # Just status code
wget --spider url                  # Check if URL exists

# Port checking
nc -zv google.com 80               # Check port 80
ss -tuln                           # All listening ports
ss -tup                            # Active connections
sudo lsof -i :80                   # What's using port 80?
```

### Firewall (ufw)

```bash
sudo ufw enable                    # Enable firewall
sudo ufw default deny incoming     # Block incoming
sudo ufw default allow outgoing    # Allow outgoing

sudo ufw allow ssh                 # Allow SSH
sudo ufw allow 80/tcp              # Allow HTTP
sudo ufw allow 443/tcp             # Allow HTTPS
sudo ufw deny from 1.2.3.4        # Block specific IP

sudo ufw status                    # Check status
sudo ufw status numbered           # Show rules
sudo ufw delete 3                  # Delete rule #3
```

---

## 11. Storage & Filesystems

### Partitioning

```bash
sudo fdisk -l                      # List all disks
sudo fdisk /dev/sda                # Interactive partition editor
# n = new partition, d = delete, t = change type
# w = write changes, q = quit

# Format
sudo mkfs.ext4 /dev/sda1           # Format as ext4
sudo mkfs.ext4 -L "DATA" /dev/sda1 # With label
sudo mkfs.xfs /dev/sda1            # XFS
sudo mkfs.ntfs /dev/sda1           # NTFS
sudo mkswap /dev/sda2              # Swap partition
```

### Mounting

```bash
# Temporary
sudo mount /dev/sda1 /mnt
sudo mount -o ro /dev/sda1 /mnt    # Read-only
sudo umount /mnt                   # Unmount
sudo umount -l /mnt                # Lazy unmount

# Permanent (/etc/fstab)
# UUID=xxxx-xxxx  /mnt/data  ext4  defaults  0  2
sudo mount -a                      # Mount all fstab entries
```

### Disk Usage

```bash
df -h                              # Disk space
df -h /                            # Root partition only
du -sh /home/username/             # Directory total size
du -sh * | sort -h                 # Sorted directory sizes
ncdu                               # Interactive disk usage
```

### LVM (Logical Volume Manager)

```bash
sudo apt install lvm2              # Install

sudo pvcreate /dev/sda1            # Physical volume
sudo vgcreate vg_data /dev/sda1    # Volume group
sudo lvcreate -L 100G vg_data -n lv_data  # Logical volume

sudo mkfs.ext4 /dev/vg_data/lv_data
sudo mount /dev/vg_data/lv_data /mnt/data

# Extend
sudo lvextend -L +50G /dev/vg_data/lv_data  # Add 50GB
sudo resize2fs /dev/vg_data/lv_data         # Resize filesystem
```

---

## 12. Services & systemd

### Service Management

```bash
# Start/Stop
sudo systemctl start nginx         # Start service
sudo systemctl stop nginx          # Stop service
sudo systemctl restart nginx       # Restart
sudo systemctl reload nginx        # Reload config (no downtime)

# Enable/Disable (at boot)
sudo systemctl enable nginx        # Start at boot
sudo systemctl disable nginx       # Don't start at boot
sudo systemctl enable --now nginx  # Enable AND start
sudo systemctl disable --now nginx # Disable AND stop

# Status
sudo systemctl status nginx        # Full status + logs
systemctl is-active nginx          # active/inactive
systemctl is-enabled nginx         # enabled/disabled
```

### Creating a Custom Service

```bash
sudo vim /etc/systemd/system/myapp.service

[Unit]
Description=My Custom Application
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/main.py
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

# Then:
sudo systemctl daemon-reload
sudo systemctl enable --now myapp
```

### journalctl — Logs

```bash
journalctl                          # All logs
journalctl -u nginx                 # Specific service
journalctl -u nginx -u sshd        # Multiple services
journalctl -f                       # Follow (like tail -f)
journalctl -n 50                    # Last 50 lines
journalctl --since "1 hour ago"
journalctl -p err                   # Only errors
journalctl -b                       # Since last boot
journalctl -b -1                    # Previous boot

# Cleanup
sudo journalctl --vacuum-size=100M  # Keep only 100MB
sudo journalctl --vacuum-time=2weeks
```

### Scheduling Tasks (cron)

```bash
crontab -e                          # Edit your cron jobs
crontab -l                          # List your jobs
sudo crontab -e                     # Root's crontab

# Format: Minute Hour Day Month Weekday Command
#         0-59   0-23 1-31 1-12  0-7

*/5 * * * * /script.sh              # Every 5 minutes
0 * * * * /script.sh                # Every hour
0 2 * * * /script.sh                # Daily at 2 AM
0 0 * * 0 /script.sh                # Weekly (Sunday)
0 0 1 * * /script.sh                # Monthly (1st)
@reboot /script.sh                  # On boot
```

---

## 13. Shell Scripting

### Your First Script

```bash
#!/bin/bash
# The shebang — tells the system which interpreter to use

echo "Hello, World!"
```

Save as `hello.sh`:

```bash
chmod +x hello.sh
./hello.sh
```

### Variables

```bash
#!/bin/bash

name="Alice"
age=30
echo "$name is $age years old"

# Command substitution
today=$(date)
echo "Today is $today"

# Arithmetic
sum=$((5 + 3))

# Read-only
readonly PI=3.14159

# Local (inside functions)
myfunc() {
    local var="I'm local"
}
```

### User Input

```bash
read -p "Enter your name: " name
echo "Hello, $name!"

read -s -p "Password: " pass    # Silent input
echo

# Choice menu
echo "1) Option A"
echo "2) Option B"
read -p "Choose: " choice
case $choice in
    1) echo "You chose A" ;;
    2) echo "You chose B" ;;
    *) echo "Invalid" ;;
esac
```

### Conditionals

```bash
# IF/ELIF/ELSE
if [ "$1" = "hello" ]; then
    echo "Hi!"
elif [ "$1" = "bye" ]; then
    echo "Goodbye!"
else
    echo "I don't understand"
fi

# File tests
[ -f "$file" ]  # Is it a file?
[ -d "$dir" ]   # Is it a directory?
[ -x "$file" ]  # Is it executable?
[ -z "$var" ]   # Is string empty?
[ -n "$var" ]   # Is string non-empty?

# Comparisons
[ "$a" = "$b" ]     # String equal
[ "$a" -eq "$b" ]   # Numeric equal
[ "$a" -gt "$b" ]   # Greater than
[ "$a" -lt "$b" ]   # Less than

# Logical
[ "$a" = "yes" ] && [ "$b" = "yes" ]  # AND
[ "$a" = "yes" ] || [ "$b" = "yes" ]  # OR
! [ -f "$file" ]                        # NOT
```

### Loops

```bash
# FOR loop
for fruit in apple banana cherry; do
    echo "I like $fruit"
done

for i in {1..10}; do
    echo "Number $i"
done

for ((i=0; i<10; i++)); do
    echo "i = $i"
done

for file in *.txt; do
    echo "Processing $file"
done

# WHILE loop
count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done

# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < file.txt

# UNTIL loop
count=1
until [ $count -gt 5 ]; do
    echo $count
    ((count++))
done
```

### Functions

```bash
#!/bin/bash

greet() {
    echo "Hello, $1! You are $2."
}

greet "Alice" 30

# Return value via stdout
add() {
    echo $(($1 + $2))
}
result=$(add 5 3)
echo "5 + 3 = $result"
```

### Practical Script Example

```bash
#!/bin/bash

# System Information Script

echo "═══════════════════════════════"
echo "  System Information"
echo "  $(date)"
echo "═══════════════════════════════"

echo "Hostname: $(hostname)"
echo "Kernel: $(uname -r)"
echo "Uptime: $(uptime -p)"
echo "CPU Cores: $(nproc)"

mem=$(free -h | grep Mem | awk '{print $3 " / " $2}')
echo "Memory: $mem"

disk=$(df -h / | tail -1 | awk '{print $3 " / " $2 " (" $5 ")"}')
echo "Disk (/): $disk"

ip=$(ip addr | grep inet | grep -v 127.0.0.1 | awk '{print $2}' | head -1)
echo "IP: $ip"

echo "═══════════════════════════════"
```

---

## 14. Security

### File Integrity

```bash
md5sum file.txt                     # MD5 hash
sha1sum file.txt                    # SHA-1
sha256sum file.txt                  # SHA-256 (recommended)
sha512sum file.txt                  # SHA-512

# Verify
echo "hash filename" | sha256sum -c

# Generate checksums for all files
sha256sum * > checksums.sha256
sha256sum -c checksums.sha256
```

### Encryption

```bash
# GPG
gpg --gen-key                                       # Generate key
gpg --encrypt --recipient user@email file.txt       # Encrypt
gpg --decrypt file.txt.gpg                          # Decrypt
gpg --export --armor user@email > public.key        # Export key
gpg --import public.key                             # Import key

# OpenSSL
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc  # Encrypt
openssl enc -d -aes-256-cbc -in file.enc -out file.txt    # Decrypt
openssl rand -base64 32                             # Random password

# LUKS (Disk encryption)
sudo cryptsetup luksFormat /dev/sda1                # Encrypt partition
sudo cryptsetup open /dev/sda1 secret               # Decrypt
sudo mkfs.ext4 /dev/mapper/secret                   # Format
sudo mount /dev/mapper/secret /mnt/secret            # Mount
sudo cryptsetup close secret                         # Close
```

### SSH Hardening

```bash
# Edit /etc/ssh/sshd_config
Port 2222                           # Change default port
PermitRootLogin no                  # No root login
PasswordAuthentication no           # Key-based only
PubkeyAuthentication yes
AllowUsers alice bob                # Specific users only
MaxAuthTries 3                      # Limit attempts
ClientAliveInterval 300             # Disconnect idle

sudo systemctl restart sshd

# Key-based auth
ssh-keygen -t ed25519               # Generate key
ssh-copy-id user@server.com         # Copy to server
ssh user@server.com                 # Connect
```

---

## 15. System Administration

### System Monitoring

```bash
# CPU
lscpu
cat /proc/cpuinfo
mpstat -P ALL 1                     # Per-CPU every second

# Memory
free -h
cat /proc/meminfo
vmstat 2

# Disk I/O
iostat -x 2
iotop

# Network
iftop
nethogs

# Top processes
ps aux --sort=-%cpu | head -20
ps aux --sort=-%mem | head -20
```

### Log Management

```bash
# Log locations
/var/log/syslog                     # System log (Ubuntu/Debian)
/var/log/messages                   # System log (RHEL/Fedora)
/var/log/auth.log                   # Authentication (Ubuntu)
/var/log/secure                     # Authentication (RHEL)
/var/log/kern.log                   # Kernel messages

# Viewing
tail -f /var/log/syslog
grep -i "error" /var/log/syslog
journalctl -xe
journalctl -u nginx --since "1 hour ago"
```

### Backup & Restore

```bash
# rsync (efficient sync)
rsync -av source/ dest/
rsync -avz /local/ user@host:/remote/
rsync -av --delete source/ dest/    # Mirror (delete removed files)

# tar
tar -czf backup.tar.gz /home/username/
tar -czf backup_$(date +%Y%m%d).tar.gz /data/
tar -xzf backup.tar.gz

# Exclude files
tar --exclude='*.log' -czf backup.tar.gz /var/www

# dd (disk cloning)
sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress
sudo dd if=/dev/sda of=disk.img bs=4M
```

---

## 16. Advanced Topics

### Process Substitution

```bash
# Compare command outputs without temp files
diff <(ls /dir1) <(ls /dir2)

# Join sorted files
join <(sort file1.txt) <(sort file2.txt)
```

### Regular Expressions

```bash
# Basic
grep 'hello' file.txt           # Literal string
grep '^start' file.txt          # Line starts with
grep 'end$' file.txt            # Line ends with
grep 'h.llo' file.txt           # Any character (.)
grep 'h[ae]llo' file.txt        # "hallo" or "hello"
grep '[0-9]' file.txt           # Any digit
grep '[^0-9]' file.txt          # NOT a digit

# Extended (grep -E)
grep -E 'hello|world'           # OR
grep -E 'colou?r'               # ? = zero or one
grep -E 'colou*r'               # * = zero or more
grep -E 'colou+r'               # + = one or more
grep -E '[0-9]{3}'              # Exactly 3 digits

# Perl (grep -P)
grep -P '\d{3}-\d{3}-\d{4}'    # Phone numbers
grep -P '\w+@\w+\.\w+'         # Simple emails
```

### Docker

```bash
docker pull ubuntu                    # Download image
docker run -it ubuntu bash            # Interactive container
docker run -d -p 80:80 nginx          # Background, port 80
docker ps                             # Running containers
docker ps -a                          # All containers
docker stop container_id              # Stop
docker rm container_id                # Remove
docker images                         # List images
docker exec -it container_id bash     # Execute command
docker logs container_id              # View logs
```

---

## 17. Cheat Sheet

### Navigation
| Command | What it does |
|---------|-------------|
| `pwd` | Show current directory |
| `ls -la` | List all files with details |
| `cd /path` | Change to directory |
| `cd ~` | Go home |
| `cd -` | Go to previous directory |
| `mkdir folder` | Create folder |
| `rm -rf folder` | Delete folder |

### File Operations
| Command | What it does |
|---------|-------------|
| `cp src dst` | Copy file |
| `mv old new` | Rename/move file |
| `rm file` | Delete file |
| `touch file` | Create empty file |
| `cat file` | View file |
| `less file` | Page through file |
| `head -n 10 file` | First 10 lines |
| `tail -f file` | Watch file live |

### Permissions
| Command | What it does |
|---------|-------------|
| `chmod 755 file` | Set permissions |
| `chmod +x file` | Make executable |
| `chown user:group file` | Change owner |

### Finding Things
| Command | What it does |
|---------|-------------|
| `find . -name "*.txt"` | Find files by name |
| `grep "text" file` | Find text in file |
| `which command` | Where is a command? |
| `locate file` | Find from database |

### System
| Command | What it does |
|---------|-------------|
| `ps aux` | All running processes |
| `kill PID` | Stop process |
| `df -h` | Disk space |
| `du -sh *` | Directory sizes |
| `free -h` | Memory usage |
| `uname -a` | System info |
| `uptime` | How long running |

### Networking
| Command | What it does |
|---------|-------------|
| `ip addr` | Show IP addresses |
| `ping host` | Test connectivity |
| `dig domain` | DNS lookup |
| `ss -tuln` | Listening ports |
| `curl -I URL` | HTTP headers |
| `wget URL` | Download file |

### Package Management
| Command | What it does |
|---------|-------------|
| `sudo apt update` | Update package list |
| `sudo apt upgrade` | Update all packages |
| `sudo apt install pkg` | Install package |
| `sudo apt remove pkg` | Remove package |
| `apt search term` | Search packages |

### Services
| Command | What it does |
|---------|-------------|
| `sudo systemctl start svc` | Start service |
| `sudo systemctl stop svc` | Stop service |
| `sudo systemctl restart svc` | Restart service |
| `sudo systemctl enable svc` | Start at boot |
| `sudo systemctl status svc` | Check status |
| `journalctl -u svc` | Service logs |

---
