# 🐧 Linux + Open Source Crash Course (For Red Hat / Qualcomm)

> Red Hat = Linux company. They'll test if you actually USE Linux. Qualcomm = embedded systems, also needs Linux/OS basics.

---

## Essential Commands (Memorize These)

### File System Navigation
```bash
pwd                     # Print current directory
ls -la                  # List all files with details (-l=long, -a=hidden)
cd /path/to/dir         # Change directory
cd ~                    # Go to home directory
cd ..                   # Go up one level
mkdir -p dir1/dir2      # Create nested directories
rm file.txt             # Delete file
rm -rf directory/       # Delete directory recursively (DANGEROUS)
cp source dest          # Copy
mv source dest          # Move or rename
find / -name "*.log"    # Find files by name
which python            # Show path of a command
```

### File Operations
```bash
cat file.txt            # Print entire file
head -n 20 file.txt     # First 20 lines
tail -n 20 file.txt     # Last 20 lines
tail -f app.log         # Follow log in real-time (VERY useful)
grep "error" file.txt   # Search for "error" in file
grep -r "TODO" ./src/   # Search recursively in directory
grep -i "error" file    # Case-insensitive search
wc -l file.txt          # Count lines
sort file.txt           # Sort lines
uniq                    # Remove adjacent duplicates (use with sort)
diff file1 file2        # Compare two files
```

### Piping & Redirection (Important!)
```bash
# Pipe: Output of command1 → input of command2
cat server.log | grep "ERROR" | wc -l       # Count error lines
ps aux | grep python                         # Find python processes

# Redirect output
echo "hello" > file.txt    # Write (overwrite)
echo "world" >> file.txt   # Append
command 2> errors.log      # Redirect stderr
command > out.log 2>&1     # Redirect both stdout and stderr
```

### Permissions
```bash
ls -la
# -rwxr-xr-- 1 user group 1234 Jul 13 file.txt
# |||
# user/group/others  (r=read, w=write, x=execute)

chmod 755 script.sh     # rwxr-xr-x (owner: all, others: read+execute)
chmod +x script.sh      # Add execute permission
chown user:group file   # Change ownership
```

**Permission numbers:** r=4, w=2, x=1. Add them up.
- 7 = rwx (4+2+1)
- 5 = r-x (4+0+1)
- 4 = r-- (4+0+0)

### Process Management
```bash
ps aux                  # List all processes
ps aux | grep nginx     # Find specific process
top / htop              # Live process monitor
kill PID                # Send SIGTERM (graceful stop)
kill -9 PID             # Send SIGKILL (force kill)
bg                      # Send job to background
fg                      # Bring job to foreground
nohup command &         # Run command immune to hangups
```

### Networking
```bash
ping google.com         # Test connectivity
curl https://api.com    # Make HTTP request
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.com
wget https://url/file   # Download file
netstat -tlnp           # Show listening ports
ss -tlnp                # Modern netstat
ip addr                 # Show IP addresses
```

### Package Management
```bash
# Debian/Ubuntu (apt)
sudo apt update              # Refresh package list
sudo apt install nginx       # Install
sudo apt remove nginx        # Uninstall

# Red Hat/CentOS (dnf/yum)
sudo dnf install httpd       # Install
sudo dnf update              # Update all
sudo dnf search python       # Search
rpm -qa                      # List all installed packages

# Python
pip install package
pip freeze > requirements.txt
pip install -r requirements.txt
```

### systemd (Service Management)
```bash
sudo systemctl start nginx     # Start service
sudo systemctl stop nginx      # Stop service
sudo systemctl restart nginx   # Restart
sudo systemctl status nginx    # Check status
sudo systemctl enable nginx    # Start on boot
sudo systemctl disable nginx   # Don't start on boot
journalctl -u nginx -f         # View service logs
```

---

## Containers (Docker / Podman)

> Red Hat uses **Podman** (Docker alternative, rootless, daemonless). Commands are nearly identical.

```bash
# Build an image
docker build -t myapp .          # or: podman build -t myapp .

# Run a container
docker run -d -p 8080:80 myapp   # -d=detached, -p=port mapping

# List containers
docker ps                         # Running
docker ps -a                      # All (including stopped)

# Logs
docker logs container_id

# Stop and remove
docker stop container_id
docker rm container_id

# Docker Compose
docker-compose up -d              # Start all services
docker-compose down               # Stop all services
docker-compose logs -f             # Follow logs
```

### Dockerfile Basics
```dockerfile
FROM python:3.11-slim       # Base image
WORKDIR /app                # Set working directory
COPY requirements.txt .     # Copy dependencies file
RUN pip install -r requirements.txt  # Install deps
COPY . .                    # Copy app code
EXPOSE 8000                 # Document the port
CMD ["python", "main.py"]   # Default command
```

---

## Git (Version Control)

```bash
git init                        # Initialize repo
git clone url                   # Clone repo
git status                      # See changes
git add .                       # Stage all changes
git commit -m "message"         # Commit
git push origin main            # Push to remote
git pull                        # Pull latest
git branch feature-x            # Create branch
git checkout feature-x          # Switch to branch
git checkout -b feature-x       # Create + switch
git merge feature-x             # Merge branch into current
git log --oneline -10           # Last 10 commits (short)
git diff                        # See unstaged changes
git stash                       # Temporarily save changes
git stash pop                   # Restore stashed changes
```

---

## Open Source Basics (For Red Hat)

### What is Open Source?
Software whose source code is publicly available. Anyone can view, use, modify, and distribute it under the terms of its license.

### Common Licenses
| License | Key Feature |
|:--------|:-----------|
| MIT | Most permissive — do whatever you want, just include the license |
| Apache 2.0 | Like MIT but with patent protection |
| GPL | Copyleft — if you modify it, your code must also be open source |
| LGPL | Weaker GPL — can use in proprietary software without open-sourcing yours |

### Red Hat's Open Source Model
- Red Hat takes open source projects (Linux kernel, Kubernetes, Ansible) and provides **enterprise support, security patches, and certification**
- RHEL (Red Hat Enterprise Linux) is the commercial product built from open source Fedora
- Revenue comes from **subscriptions for support**, not the software itself
- Key projects: RHEL, OpenShift (Kubernetes), Ansible (automation), Podman (containers)

### How to Contribute to Open Source
1. Find a project on GitHub
2. Fork it
3. Clone your fork
4. Create a feature branch
5. Make changes
6. Submit a Pull Request
7. Respond to code review feedback

---

## OS Concepts (For Qualcomm)

### Process vs Thread
- **Process:** Independent program with its own memory space. Isolated. Heavy to create.
- **Thread:** Lightweight execution unit within a process. Shares memory. Cheaper to create.

### Virtual Memory
- Programs see a virtual address space, mapped to physical RAM by the OS
- Allows running programs larger than physical memory (uses disk as swap)

### Kernel vs User Space
- **Kernel space:** OS core — manages hardware, memory, processes
- **User space:** Where applications run. Can't directly access hardware — must use system calls

### File System Hierarchy (Linux)
```
/          Root
/home      User home directories
/etc       Configuration files
/var       Variable data (logs, caches)
/tmp       Temporary files
/usr       User programs and libraries
/bin       Essential binaries (ls, cp, cat)
/sbin      System binaries (systemctl, iptables)
/dev       Device files
/proc      Process information (virtual filesystem)
```

---

## Quick Interview Questions

| Question | Answer |
|:---------|:-------|
| "What's the difference between a process and a thread?" | "A process has its own memory space and is isolated. A thread shares memory with other threads in the same process and is lightweight." |
| "What does chmod 755 mean?" | "Owner gets read+write+execute (7), group gets read+execute (5), others get read+execute (5)." |
| "How do you find which process is using port 8080?" | "`netstat -tlnp \| grep 8080` or `ss -tlnp \| grep 8080` or `lsof -i :8080`" |
| "What's the difference between Docker and Podman?" | "Podman is daemonless and rootless — no background daemon needed, runs as non-root. Docker requires a daemon. Commands are nearly identical." |
| "What's a kernel?" | "The core of the OS. Manages hardware, memory, processes, and provides system calls for user applications." |
| "What's systemd?" | "The init system and service manager for modern Linux. Manages starting, stopping, and monitoring services." |
| "Explain pipe (\|) in Linux." | "Takes the output of one command and feeds it as input to the next command. `cat log \| grep ERROR \| wc -l` counts error lines." |
