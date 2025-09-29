## 🔧 Linux DevOps Scenario-Based Questions (1–10)

---

### **Question 1: How do you troubleshoot high CPU usage on a production Linux server?**

**Answer:**
1. Identify top CPU-consuming processes:
   ```bash
   top
   ```
2. Use `pidstat` for per-process CPU breakdown:
   ```bash
   pidstat -u 1 5
   ```
3. Investigate specific PID:
   ```bash
   ps -p <PID> -o %cpu,%mem,cmd
   ```
4. Check for runaway processes or loops in logs.

**Tips & Best Practices:**
- Use `htop` for interactive analysis.
- Monitor with `sar` or `collectl` for historical data.

**Example:**
```bash
top -b -n1 | head -20
```

---

### **Question 2: How do you find and clean up large files consuming disk space?**

**Answer:**
1. Check disk usage:
   ```bash
   df -h
   ```
2. Find large files:
   ```bash
   find / -type f -size +500M -exec ls -lh {} \;
   ```
3. Clean logs:
   ```bash
   truncate -s 0 /var/log/syslog
   ```

**Tips & Best Practices:**
- Use `ncdu` for interactive disk usage.
- Automate cleanup with cron.

**Example:**
```bash
du -sh /var/* | sort -hr | head -10
```

---

### **Question 3: How do you automate user creation with a shell script?**

**Answer:**
1. Create a script:
   ```bash
   #!/bin/bash
   for user in user1 user2 user3; do
     useradd -m "$user"
     echo "$user:password123" | chpasswd
   done
   ```

**Tips & Best Practices:**
- Validate usernames.
- Use encrypted passwords with `chpasswd`.

**Example:**
```bash
chmod +x create_users.sh && ./create_users.sh
```

---

### **Question 4: How do you monitor memory usage and detect leaks?**

**Answer:**
1. Use `free -m`:
   ```bash
   free -m
   ```
2. Check swap usage:
   ```bash
   swapon -s
   ```
3. Use `smem` or `ps_mem.py` for detailed breakdown.

**Tips & Best Practices:**
- Monitor with Prometheus + Grafana.
- Set alerts for swap usage.

**Example:**
```bash
ps aux --sort=-%mem | head -10
```

---

### **Question 5: How do you schedule a backup job using cron?**

**Answer:**
1. Edit crontab:
   ```bash
   crontab -e
   ```
2. Add job:
   ```bash
   0 2 * * * tar -czf /backup/home.tar.gz /home
   ```

**Tips & Best Practices:**
- Use `cron.d` for system-wide jobs.
- Log output with `>> /var/log/backup.log 2>&1`.

**Example:**
```bash
crontab -l
```

---

### **Question 6: How do you secure SSH access on a Linux server?**

**Answer:**
1. Disable root login:
   ```bash
   sudo nano /etc/ssh/sshd_config
   PermitRootLogin no
   ```
2. Change default port:
   ```bash
   Port 2222
   ```
3. Restart SSH:
   ```bash
   sudo systemctl restart ssh
   ```

**Tips & Best Practices:**
- Use key-based authentication.
- Monitor with `fail2ban`.

**Example:**
```bash
ssh -p 2222 user@host
```

---

### **Question 7: How do you configure log rotation for custom logs?**

**Answer:**
1. Create config in `/etc/logrotate.d/myapp`:
   ```bash
   /var/log/myapp/*.log {
     daily
     rotate 7
     compress
     missingok
     notifempty
   }
   ```

**Tips & Best Practices:**
- Test with `logrotate -d`.
- Use `postrotate` for service reloads.

**Example:**
```bash
logrotate -f /etc/logrotate.d/myapp
```

---

### **Question 8: How do you mount an NFS share on a Linux client?**

**Answer:**
1. Install NFS client:
   ```bash
   sudo apt install nfs-common
   ```
2. Mount:
   ```bash
   sudo mount -t nfs server:/share /mnt
   ```

**Tips & Best Practices:**
- Add to `/etc/fstab` for persistence.
- Use `autofs` for dynamic mounts.

**Example:**
```bash
mount | grep nfs
```

---

### **Question 9: How do you set up a basic firewall using UFW?**

**Answer:**
1. Enable UFW:
   ```bash
   sudo ufw enable
   ```
2. Allow SSH:
   ```bash
   sudo ufw allow 22
   ```
3. Deny all incoming:
   ```bash
   sudo ufw default deny incoming
   ```

**Tips & Best Practices:**
- Use `ufw status numbered`.
- Backup rules before changes.

**Example:**
```bash
sudo ufw allow from 192.168.1.0/24
```

---

### **Question 10: How do you monitor file changes in real-time?**

**Answer:**
1. Use `inotifywait`:
   ```bash
   inotifywait -m /var/log
   ```
2. Monitor with auditd:
   ```bash
   auditctl -w /etc/passwd -p wa
   ```

**Tips & Best Practices:**
- Use `auditd` for compliance.
- Combine with alerting tools.

**Example:**
```bash
inotifywait -m /etc | grep passwd
```

---
Perfect, Mahesh! Here's the next batch of **Linux DevOps Scenario-Based Questions (11–20)** — each crafted to reflect real-world challenges in automation, security, performance, and system management.

---

## 🔧 Linux DevOps Scenario-Based Questions (11–20)

---

### **Question 11: How do you find which process is using a specific port (e.g., 8080)?**

**Answer:**
1. Use `lsof`:
   ```bash
   sudo lsof -i :8080
   ```
2. Or use `netstat`:
   ```bash
   sudo netstat -tulnp | grep :8080
   ```
3. Or `ss` (modern alternative):
   ```bash
   sudo ss -tulnp | grep :8080
   ```

**Tips & Best Practices:**
- Use `ss` for faster output.
- Combine with `ps` to inspect the process.

**Example:**
```bash
PID=$(sudo lsof -t -i:8080)
ps -fp $PID
```

---

### **Question 12: How do you check if a service is running and restart it if it’s down?**

**Answer:**
1. Check status:
   ```bash
   systemctl is-active nginx
   ```
2. Restart if inactive:
   ```bash
   systemctl restart nginx
   ```

**Tips & Best Practices:**
- Use `systemctl` in scripts with conditional checks.
- Monitor with `monit` or `supervisord`.

**Example:**
```bash
if ! systemctl is-active --quiet nginx; then
  systemctl restart nginx
fi
```

---

### **Question 13: How do you find failed login attempts on a Linux server?**

**Answer:**
1. Check `/var/log/auth.log`:
   ```bash
   grep "Failed password" /var/log/auth.log
   ```
2. Count attempts:
   ```bash
   grep "Failed password" /var/log/auth.log | wc -l
   ```

**Tips & Best Practices:**
- Use `fail2ban` to block brute-force attempts.
- Monitor logs with `logwatch`.

**Example:**
```bash
awk '/Failed password/ {print $(NF-3)}' /var/log/auth.log | sort | uniq -c | sort -nr
```

---

### **Question 14: How do you check and update file permissions recursively?**

**Answer:**
1. Check permissions:
   ```bash
   find /var/www -type f -exec ls -l {} \;
   ```
2. Update:
   ```bash
   find /var/www -type f -exec chmod 644 {} \;
   find /var/www -type d -exec chmod 755 {} \;
   ```

**Tips & Best Practices:**
- Always test on a sample directory first.
- Use `-exec` with caution.

**Example:**
```bash
chmod -R u=rwX,go=rX /var/www
```

---

### **Question 15: How do you create a swap file and enable it?**

**Answer:**
1. Create file:
   ```bash
   sudo fallocate -l 2G /swapfile
   ```
2. Set permissions:
   ```bash
   sudo chmod 600 /swapfile
   ```
3. Format and enable:
   ```bash
   sudo mkswap /swapfile
   sudo swapon /swapfile
   ```

**Tips & Best Practices:**
- Add to `/etc/fstab` for persistence.
- Monitor with `free -h`.

**Example:**
```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

---

### **Question 16: How do you find zombie processes and clean them up?**

**Answer:**
1. List zombies:
   ```bash
   ps aux | awk '{ if ($8 == "Z") print $0; }'
   ```
2. Find parent PID:
   ```bash
   ps -o ppid= -p <Zombie_PID>
   ```
3. Kill parent:
   ```bash
   kill -HUP <Parent_PID>
   ```

**Tips & Best Practices:**
- Use `top` to spot zombies.
- Avoid killing critical parent processes.

**Example:**
```bash
ps -eo pid,ppid,state,cmd | grep 'Z'
```

---

### **Question 17: How do you limit CPU usage of a process?**

**Answer:**
1. Use `cpulimit`:
   ```bash
   sudo apt install cpulimit
   cpulimit -p <PID> -l 30
   ```

**Tips & Best Practices:**
- Use `nice` and `ionice` for priority control.
- Monitor with `top` or `htop`.

**Example:**
```bash
cpulimit -e myscript.sh -l 20
```

---

### **Question 18: How do you find and kill a hung process?**

**Answer:**
1. Identify hung process:
   ```bash
   ps -eo pid,stat,cmd | grep 'D'
   ```
2. Kill:
   ```bash
   kill -9 <PID>
   ```

**Tips & Best Practices:**
- Use `kill -15` before `-9`.
- Log before killing.

**Example:**
```bash
kill -15 $PID || kill -9 $PID
```

---

### **Question 19: How do you check system boot time and uptime?**

**Answer:**
1. Boot time:
   ```bash
   who -b
   ```
2. Uptime:
   ```bash
   uptime
   ```

**Tips & Best Practices:**
- Use `uptime -p` for readable format.
- Monitor with `systemd-analyze`.

**Example:**
```bash
systemd-analyze
```

---

### **Question 20: How do you find which cron jobs are running and debug failures?**

**Answer:**
1. List user cron jobs:
   ```bash
   crontab -l
   ```
2. Check logs:
   ```bash
   grep CRON /var/log/syslog
   ```

**Tips & Best Practices:**
- Redirect output to logs.
- Use `MAILTO` for alerts.

**Example:**
```bash
* * * * * /path/to/script.sh >> /var/log/myscript.log 2>&1
```

---
Awesome, Mahesh! Here's the third batch of **Linux DevOps Scenario-Based Questions (21–30)** — focused on networking, scripting, system hardening, and real-time troubleshooting. These are designed to sharpen your edge in production environments and automation workflows.

---

## 🔧 Linux DevOps Scenario-Based Questions (21–30)

---

### **Question 21: How do you check DNS resolution issues on a Linux server?**

**Answer:**
1. Test with `dig` or `nslookup`:
   ```bash
   dig example.com
   nslookup example.com
   ```
2. Check `/etc/resolv.conf`:
   ```bash
   cat /etc/resolv.conf
   ```
3. Use `ping` or `curl` to verify connectivity.

**Tips & Best Practices:**
- Use multiple DNS servers (e.g., 8.8.8.8, 1.1.1.1).
- Avoid hardcoding DNS in scripts.

**Example:**
```bash
dig +short example.com @8.8.8.8
```

---

### **Question 22: How do you write a script to monitor disk usage and alert if it exceeds 90%?**

**Answer:**
1. Create a shell script:
   ```bash
   #!/bin/bash
   THRESHOLD=90
   df -H | grep -vE '^Filesystem|tmpfs' | awk '{ print $5 " " $1 }' | while read output; do
     usep=$(echo $output | awk '{ print $1}' | cut -d'%' -f1)
     partition=$(echo $output | awk '{ print $2 }')
     if [ $usep -ge $THRESHOLD ]; then
       echo "Running out of space \"$partition ($usep%)\" on $(hostname) as on $(date)"
     fi
   done
   ```

**Tips & Best Practices:**
- Schedule with cron.
- Redirect output to email or log.

**Example:**
```bash
chmod +x disk_alert.sh && ./disk_alert.sh
```

---

### **Question 23: How do you list all open network connections?**

**Answer:**
1. Use `netstat`:
   ```bash
   netstat -tulnp
   ```
2. Or `ss`:
   ```bash
   ss -tuln
   ```

**Tips & Best Practices:**
- Use `ss` for faster and more detailed output.
- Combine with `lsof` for file-level inspection.

**Example:**
```bash
ss -tulnp | grep LISTEN
```

---

### **Question 24: How do you find which package installed a specific binary (e.g., `nginx`)?**

**Answer:**
1. Use `dpkg` (Debian/Ubuntu):
   ```bash
   dpkg -S $(which nginx)
   ```
2. Use `rpm` (RHEL/CentOS):
   ```bash
   rpm -qf $(which nginx)
   ```

**Tips & Best Practices:**
- Use `which` or `type -a` to locate binaries.
- Keep package managers updated.

**Example:**
```bash
dpkg -S /usr/sbin/nginx
```

---

### **Question 25: How do you check if a Linux system is under a DDoS attack?**

**Answer:**
1. Monitor connections:
   ```bash
   netstat -an | grep :80 | wc -l
   ```
2. Check for IP floods:
   ```bash
   netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr | head
   ```

**Tips & Best Practices:**
- Use `iftop` or `nload` for bandwidth monitoring.
- Deploy rate limiting with `iptables`.

**Example:**
```bash
iptables -A INPUT -p tcp --dport 80 -m connlimit --connlimit-above 50 -j DROP
```

---

### **Question 26: How do you create a systemd service for a custom script?**

**Answer:**
1. Create unit file `/etc/systemd/system/myscript.service`:
   ```ini
   [Unit]
   Description=My Custom Script
   After=network.target

   [Service]
   ExecStart=/usr/local/bin/myscript.sh
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

2. Enable and start:
   ```bash
   systemctl enable myscript
   systemctl start myscript
   ```

**Tips & Best Practices:**
- Use `Restart=on-failure` for resilience.
- Log output with `StandardOutput`.

**Example:**
```bash
journalctl -u myscript
```

---

### **Question 27: How do you find and remove orphaned packages?**

**Answer:**
1. On Debian/Ubuntu:
   ```bash
   sudo apt autoremove
   ```
2. On RHEL/CentOS:
   ```bash
   package-cleanup --leaves
   ```

**Tips & Best Practices:**
- Review before removing.
- Use `deborphan` for deeper analysis.

**Example:**
```bash
sudo apt-get autoremove --dry-run
```

---

### **Question 28: How do you check kernel version and update it safely?**

**Answer:**
1. Check version:
   ```bash
   uname -r
   ```
2. Update (Debian/Ubuntu):
   ```bash
   sudo apt update && sudo apt install linux-generic
   ```

**Tips & Best Practices:**
- Reboot after kernel update.
- Use `grub` to select older kernel if needed.

**Example:**
```bash
dpkg --list | grep linux-image
```

---

### **Question 29: How do you restrict a user to SFTP only (no SSH shell)?**

**Answer:**
1. Create user and set shell:
   ```bash
   useradd -m -s /sbin/nologin sftpuser
   ```
2. Configure `/etc/ssh/sshd_config`:
   ```text
   Match User sftpuser
     ForceCommand internal-sftp
     ChrootDirectory /home/sftpuser
     PermitTunnel no
     AllowTcpForwarding no
   ```

**Tips & Best Practices:**
- Ensure `ChrootDirectory` is owned by root.
- Restart SSH after changes.

**Example:**
```bash
sudo systemctl restart ssh
```

---

### **Question 30: How do you find and delete files older than 30 days?**

**Answer:**
1. Use `find`:
   ```bash
   find /path/to/files -type f -mtime +30 -exec rm -f {} \;
   ```

**Tips & Best Practices:**
- Test with `-print` before `-exec rm`.
- Use `-delete` for faster cleanup.

**Example:**
```bash
find /var/log/myapp -type f -mtime +30 -delete
```

---
You're on fire, Mahesh! Here's the final batch of **Linux DevOps Scenario-Based Questions (31–40)** — covering advanced scripting, SELinux, containers, and performance tuning. These are the kinds of tasks that separate seasoned DevOps engineers from the rest.

---

## 🔧 Linux DevOps Scenario-Based Questions (31–40)

---

### **Question 31: How do you use `awk` to extract the 5th column from a log file?**

**Answer:**
1. Basic extraction:
   ```bash
   awk '{print $5}' /var/log/syslog
   ```
2. Filter by keyword:
   ```bash
   awk '/error/ {print $5}' /var/log/syslog
   ```

**Tips & Best Practices:**
- Use `awk -F` to change field separator.
- Combine with `sort`, `uniq`, `grep` for powerful pipelines.

**Example:**
```bash
awk -F':' '{print $2}' /etc/passwd | sort | uniq
```

---

### **Question 32: How do you enable and configure SELinux in enforcing mode?**

**Answer:**
1. Check status:
   ```bash
   sestatus
   ```
2. Set enforcing mode:
   ```bash
   sudo setenforce 1
   ```
3. Make persistent:
   ```bash
   sudo sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config
   ```

**Tips & Best Practices:**
- Use `audit2allow` to generate policies.
- Avoid disabling SELinux unless absolutely necessary.

**Example:**
```bash
getenforce
```

---

### **Question 33: How do you run a script only if another process is not running?**

**Answer:**
1. Check with `pgrep`:
   ```bash
   if ! pgrep -x "nginx" > /dev/null; then
     ./start_nginx.sh
   fi
   ```

**Tips & Best Practices:**
- Use `pidof` or `pgrep` for reliability.
- Log actions for audit trail.

**Example:**
```bash
pgrep -x "mysqld" || systemctl start mysql
```

---

### **Question 34: How do you limit memory usage of a process using `cgroups`?**

**Answer:**
1. Create cgroup:
   ```bash
   sudo cgcreate -g memory:/limitedgroup
   ```
2. Set limit:
   ```bash
   echo 100M | sudo tee /sys/fs/cgroup/memory/limitedgroup/memory.limit_in_bytes
   ```
3. Run process:
   ```bash
   sudo cgexec -g memory:limitedgroup ./myapp
   ```

**Tips & Best Practices:**
- Use `systemd` slices for modern cgroup management.
- Monitor with `cat memory.usage_in_bytes`.

**Example:**
```bash
cgexec -g memory:limitedgroup /usr/bin/python3 script.py
```

---

### **Question 35: How do you containerize a simple shell script using Docker?**

**Answer:**
1. Create Dockerfile:
   ```Dockerfile
   FROM alpine
   COPY myscript.sh /myscript.sh
   RUN chmod +x /myscript.sh
   CMD ["/myscript.sh"]
   ```
2. Build and run:
   ```bash
   docker build -t myscript .
   docker run myscript
   ```

**Tips & Best Practices:**
- Use minimal base images.
- Avoid hardcoding secrets.

**Example:**
```bash
docker run --rm myscript
```

---

### **Question 36: How do you find which files were modified in the last 24 hours?**

**Answer:**
1. Use `find`:
   ```bash
   find /path -type f -mtime -1
   ```

**Tips & Best Practices:**
- Use `-cmin` or `-mmin` for minute-level granularity.
- Combine with `ls -lt` for sorting.

**Example:**
```bash
find /etc -type f -mtime -1 -exec ls -l {} \;
```

---

### **Question 37: How do you trace system calls made by a process?**

**Answer:**
1. Use `strace`:
   ```bash
   strace -p <PID>
   ```
2. Or trace command:
   ```bash
   strace -o trace.log ./myapp
   ```

**Tips & Best Practices:**
- Use `-e trace=network` to filter.
- Avoid running on production unless necessary.

**Example:**
```bash
strace -e openat -p $(pidof nginx)
```

---

### **Question 38: How do you benchmark disk I/O performance?**

**Answer:**
1. Use `dd`:
   ```bash
   dd if=/dev/zero of=testfile bs=1G count=1 oflag=dsync
   ```
2. Use `fio` for advanced benchmarking:
   ```bash
   fio --name=randwrite --rw=randwrite --size=1G --bs=4k --numjobs=4 --group_reporting
   ```

**Tips & Best Practices:**
- Use `iostat` for real-time monitoring.
- Clean up test files after benchmarking.

**Example:**
```bash
rm testfile
```

---

### **Question 39: How do you detect and fix broken symlinks?**

**Answer:**
1. Find broken links:
   ```bash
   find -L /path -type l
   ```
2. Remove or fix:
   ```bash
   rm /path/to/broken/link
   ln -s /new/target /path/to/link
   ```

**Tips & Best Practices:**
- Use `readlink` to inspect targets.
- Automate cleanup with scripts.

**Example:**
```bash
find -L /var/www -type l -exec rm {} \;
```

---

### **Question 40: How do you list all environment variables of a running process?**

**Answer:**
1. Use `cat` on `/proc/<PID>/environ`:
   ```bash
   tr '\0' '\n' < /proc/<PID>/environ
   ```

**Tips & Best Practices:**
- Use `env` for current shell.
- Avoid exposing secrets in environment.

**Example:**
```bash
ps aux | grep myapp
tr '\0' '\n' < /proc/1234/environ
```

---
