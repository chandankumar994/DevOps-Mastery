# Linux Server Administration Guide for Beginners

A practical, no-fluff introduction to managing a Linux server — plus a quick-reference cheat sheet at the end.

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Users & Permissions](#2-users--permissions)
3. [The Filesystem](#3-the-filesystem)
4. [Package Management](#4-package-management)
5. [Process & Service Management](#5-process--service-management)
6. [Networking Basics](#6-networking-basics)
7. [Security Essentials](#7-security-essentials)
8. [Storage & Disk Management](#8-storage--disk-management)
9. [Logs & Monitoring](#9-logs--monitoring)
10. [Automation with Cron](#10-automation-with-cron)
11. [Backups](#11-backups)
12. [Troubleshooting Checklist](#12-troubleshooting-checklist)
13. [Cheat Sheet](#13-cheat-sheet)

---

## 1. Getting Started

### Connecting to your server

Most servers are managed remotely over **SSH** (Secure Shell):

```bash
ssh username@server_ip
# Example:
ssh root@203.0.113.10

# Using a specific port or key file:
ssh -p 2222 -i ~/.ssh/id_rsa username@server_ip
```

First time connecting, you'll see a fingerprint prompt — type `yes` to trust the host.

### Your first few commands

```bash
whoami          # who am I logged in as?
hostname        # what is this machine called?
uname -a        # kernel & system info
uptime          # how long has it been running, and load average
df -h           # disk space, human-readable
free -h         # memory usage, human-readable
```

> 💡 **Tip:** Almost every Linux command supports `--help`, and most have a manual page: `man ls`. When in doubt, check there first.

---

## 2. Users & Permissions

Linux is a multi-user system at its core. Understanding users, groups, and permissions is essential.

### Managing users

```bash
sudo adduser alice              # create a new user (interactive, friendlier)
sudo useradd -m bob             # create a new user (lower-level, -m makes home dir)
sudo passwd alice                # set/change a password
sudo deluser alice               # remove a user
sudo usermod -aG sudo alice      # add alice to the sudo group (admin rights)
```

### Understanding `sudo`

`sudo` lets a permitted user run a command as another user (usually root) without logging in as root directly. This is safer than using the root account for daily work.

```bash
sudo apt update        # run a single command as root
sudo -i                # start a root shell (use sparingly)
```

### File permissions

Every file has an **owner**, a **group**, and **permissions** for owner/group/others (read, write, execute):

```bash
ls -l file.txt
# -rw-r--r-- 1 alice staff 220 Jan 1 09:00 file.txt
#  ^^^^^^^^^
#  owner: rw-, group: r--, others: r--
```

Changing permissions and ownership:

```bash
chmod 644 file.txt        # owner: read/write, group & others: read only
chmod +x script.sh        # make a file executable
chown alice:staff file.txt   # change owner and group
chown -R alice:staff /var/www/mysite   # recursively, for a whole directory
```

**Permission numbers, quick reference:**

| Number | Meaning |
|---|---|
| 7 | read + write + execute |
| 6 | read + write |
| 5 | read + execute |
| 4 | read only |
| 0 | no permission |

A common pattern is `755` (owner: full access, others: read/execute) for scripts and directories, and `644` (owner: read/write, others: read) for regular files.

---

## 3. The Filesystem

### Key directories

| Path | Purpose |
|---|---|
| `/` | Root of the entire filesystem |
| `/home` | User home directories |
| `/etc` | System-wide configuration files |
| `/var` | Variable data: logs, mail, caches |
| `/var/log` | Log files |
| `/usr` | Installed software and libraries |
| `/bin`, `/usr/bin` | Essential executable programs |
| `/tmp` | Temporary files (often cleared on reboot) |
| `/root` | The root user's home directory |
| `/opt` | Optional/third-party software |
| `/mnt`, `/media` | Mount points for external drives |

### Navigating and managing files

```bash
pwd                      # print working directory
cd /var/log              # change directory
ls -la                   # list all files, including hidden, with details
cp file.txt backup.txt   # copy a file
mv file.txt /tmp/        # move (or rename) a file
rm file.txt              # delete a file
rm -rf directory/        # delete a directory and its contents (careful!)
mkdir new-folder         # create a directory
find /var/log -name "*.log"   # search for files by name
grep -r "error" /var/log      # search inside files for text
```

> ⚠️ **Warning:** `rm -rf` has no undo. Double-check your path before running it, especially as root.

---

## 4. Package Management

Software installation depends on your Linux distribution's package manager.

### Debian / Ubuntu (`apt`)

```bash
sudo apt update                 # refresh the list of available packages
sudo apt upgrade                # upgrade installed packages
sudo apt install nginx          # install a package
sudo apt remove nginx           # remove a package (keeps config files)
sudo apt purge nginx            # remove a package and its config files
sudo apt autoremove             # clean up unneeded dependencies
apt search nginx                # search for a package
```

### RHEL / CentOS / Fedora / Rocky (`dnf`, or `yum` on older systems)

```bash
sudo dnf check-update
sudo dnf update
sudo dnf install nginx
sudo dnf remove nginx
```

### Verifying what's installed

```bash
dpkg -l | grep nginx       # Debian/Ubuntu
rpm -qa | grep nginx       # RHEL/Fedora
which nginx                # show the path to an installed program
```

---

## 5. Process & Service Management

### Viewing processes

```bash
ps aux                 # list all running processes
top                     # live-updating process viewer (press 'q' to quit)
htop                    # nicer version of top (may need: sudo apt install htop)
pgrep nginx             # find process IDs by name
```

### Killing processes

```bash
kill 1234               # ask process 1234 to stop gracefully
kill -9 1234            # force-kill process 1234 (last resort)
pkill nginx             # kill by process name
```

### Managing services with `systemd`

Most modern distros use `systemctl` to manage background services (daemons):

```bash
sudo systemctl status nginx     # check if a service is running
sudo systemctl start nginx      # start it
sudo systemctl stop nginx       # stop it
sudo systemctl restart nginx    # restart it
sudo systemctl reload nginx     # reload config without dropping connections
sudo systemctl enable nginx     # start automatically on boot
sudo systemctl disable nginx    # don't start automatically on boot
systemctl list-units --type=service   # list all services
```

---

## 6. Networking Basics

```bash
ip a                      # show network interfaces and IP addresses
ip route                  # show the routing table
ping google.com            # test connectivity
curl -I https://example.com   # test an HTTP endpoint, show headers only
ss -tuln                  # show listening ports (modern replacement for netstat)
traceroute example.com    # trace the path packets take to a destination
dig example.com           # DNS lookup
nslookup example.com      # DNS lookup (alternative)
```

### Firewalls

**Ubuntu/Debian (`ufw` — Uncomplicated Firewall):**

```bash
sudo ufw status
sudo ufw allow 22/tcp      # allow SSH
sudo ufw allow 80,443/tcp  # allow HTTP and HTTPS
sudo ufw enable
sudo ufw deny 8080
```

**RHEL/Fedora/CentOS (`firewalld`):**

```bash
sudo firewall-cmd --state
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

> 💡 **Tip:** Always allow SSH *before* enabling a firewall, or you may lock yourself out of a remote server!

---

## 7. Security Essentials

A short list of things every server should have from day one:

- **Disable root SSH login** and use key-based authentication instead of passwords.
  Edit `/etc/ssh/sshd_config`:
  ```
  PermitRootLogin no
  PasswordAuthentication no
  ```
  Then restart SSH: `sudo systemctl restart sshd`

- **Keep the system updated:**
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

- **Use a firewall** (see section 6) and only open ports you actually need.

- **Install fail2ban** to auto-block IPs after repeated failed login attempts:
  ```bash
  sudo apt install fail2ban
  sudo systemctl enable --now fail2ban
  ```

- **Create a non-root admin user** rather than working as root all the time (see section 2).

- **Set up automatic security updates** (Ubuntu):
  ```bash
  sudo apt install unattended-upgrades
  sudo dpkg-reconfigure --priority=low unattended-upgrades
  ```

- **Check who's logged in and login history:**
  ```bash
  who              # currently logged in users
  last             # login history
  lastb            # failed login attempts
  ```

---

## 8. Storage & Disk Management

```bash
df -h                       # disk usage per filesystem
du -sh /var/log             # size of a specific directory
du -sh /var/log/*           # size of everything inside a directory
lsblk                       # list block devices (disks & partitions)
mount /dev/sdb1 /mnt/data   # mount a device
umount /mnt/data            # unmount a device
fdisk -l                    # list partitions (detailed, needs sudo)
```

Finding what's eating disk space:

```bash
du -ah / 2>/dev/null | sort -rh | head -n 20
```

---

## 9. Logs & Monitoring

Logs are your first stop when troubleshooting.

```bash
journalctl                       # view systemd logs
journalctl -u nginx              # logs for a specific service
journalctl -f                    # follow logs live (like tail -f)
journalctl --since "1 hour ago"  # logs from a time window
tail -f /var/log/syslog          # follow a traditional log file live
tail -n 100 /var/log/auth.log    # last 100 lines of the auth log
```

### Basic resource monitoring

```bash
top / htop        # CPU & memory usage, live
vmstat 1           # system performance stats every 1 second
iostat 1           # disk I/O stats (may need: sudo apt install sysstat)
free -h            # memory usage
uptime             # load averages (1, 5, 15 min)
```

---

## 10. Automation with Cron

`cron` runs scheduled tasks automatically.

```bash
crontab -e          # edit your personal scheduled tasks
crontab -l           # list your scheduled tasks
sudo crontab -e -u www-data   # edit another user's crontab
```

Cron schedule format:

```
* * * * *  command-to-run
│ │ │ │ │
│ │ │ │ └── day of week (0–6, Sun=0)
│ │ │ └──── month (1–12)
│ │ └────── day of month (1–31)
│ └──────── hour (0–23)
└────────── minute (0–59)
```

**Examples:**

```bash
0 3 * * *      /home/alice/backup.sh        # every day at 3:00 AM
*/15 * * * *   /home/alice/check.sh         # every 15 minutes
0 0 * * 0      /home/alice/weekly-report.sh # every Sunday at midnight
```

---

## 11. Backups

A simple, solid backup approach beats a complex one nobody maintains.

```bash
# Archive a directory with compression
tar -czvf backup-$(date +%F).tar.gz /var/www/mysite

# Extract an archive
tar -xzvf backup-2026-08-02.tar.gz

# Sync files to another machine (efficient, incremental)
rsync -avz /var/www/mysite/ user@backup-server:/backups/mysite/

# Copy a file to/from a remote server over SSH
scp localfile.txt user@server:/remote/path/
scp user@server:/remote/file.txt ./
```

> 💡 **Tip:** Test restoring a backup occasionally — a backup you've never restored is a hope, not a plan.

---

## 12. Troubleshooting Checklist

When something's wrong, work through this order:

1. **Is the service running?** `systemctl status <service>`
2. **What do the logs say?** `journalctl -u <service> -e`
3. **Is there disk space left?** `df -h`
4. **Is there memory available?** `free -h`
5. **Is the network reachable?** `ping`, `curl`, `ss -tuln`
6. **Are permissions correct?** `ls -l`
7. **Did something change recently?** `history`, `journalctl --since "2 hours ago"`
8. **Can you reproduce it manually?** Run the command yourself, outside the service, to see the raw error.

---

## 13. Cheat Sheet

A condensed one-page reference for everyday use.

### System Info
| Command | Purpose |
|---|---|
| `uname -a` | Kernel/system info |
| `uptime` | Uptime & load average |
| `whoami` | Current user |
| `hostname` | Machine name |
| `df -h` | Disk space |
| `free -h` | Memory usage |

### Files & Permissions
| Command | Purpose |
|---|---|
| `ls -la` | List all files, detailed |
| `cd <dir>` | Change directory |
| `cp a b` | Copy file |
| `mv a b` | Move/rename file |
| `rm -rf <dir>` | Delete directory (careful!) |
| `chmod 755 file` | Set permissions |
| `chown user:group file` | Change ownership |
| `find / -name "x"` | Find files by name |
| `grep -r "text" .` | Search inside files |

### Users
| Command | Purpose |
|---|---|
| `sudo adduser name` | Create user |
| `sudo passwd name` | Set password |
| `sudo usermod -aG sudo name` | Grant admin rights |
| `sudo deluser name` | Delete user |

### Packages
| Command (Debian/Ubuntu) | Purpose |
|---|---|
| `sudo apt update && sudo apt upgrade` | Update system |
| `sudo apt install <pkg>` | Install package |
| `sudo apt remove <pkg>` | Remove package |

### Processes & Services
| Command | Purpose |
|---|---|
| `ps aux` | List processes |
| `top` / `htop` | Live process monitor |
| `kill -9 <pid>` | Force-kill process |
| `sudo systemctl status <svc>` | Check service |
| `sudo systemctl restart <svc>` | Restart service |
| `sudo systemctl enable <svc>` | Enable on boot |

### Networking
| Command | Purpose |
|---|---|
| `ip a` | Show IP addresses |
| `ping <host>` | Test connectivity |
| `ss -tuln` | Show listening ports |
| `curl -I <url>` | Check HTTP response headers |
| `sudo ufw allow 22/tcp` | Allow a port through firewall |

### Logs
| Command | Purpose |
|---|---|
| `journalctl -u <svc> -f` | Follow service logs live |
| `tail -f /var/log/syslog` | Follow a log file live |
| `journalctl --since "1 hour ago"` | Recent logs |

### Backups & Transfers
| Command | Purpose |
|---|---|
| `tar -czvf out.tar.gz dir/` | Compress a directory |
| `tar -xzvf out.tar.gz` | Extract an archive |
| `rsync -avz src/ user@host:dst/` | Sync files to remote host |
| `scp file user@host:/path/` | Copy file over SSH |

### Cron
| Format | Meaning |
|---|---|
| `* * * * *` | min hour day month weekday |
| `0 3 * * *` | Every day at 3 AM |
| `*/15 * * * *` | Every 15 minutes |

---

*Keep this guide handy while you work — most day-to-day server admin tasks come down to a small set of commands used consistently and carefully. When unsure, check `man <command>` or run it with `--help` before you run it with `sudo`.*
