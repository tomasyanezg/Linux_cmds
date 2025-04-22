# 🐧 Linux, Bash & Docker – Essential Commands Cheat Sheet

A growing list of everyday-useful commands in Ubuntu, Bash, and Docker, with examples and explanations. Updated as I learn and use more.

---

## 📁 Linux & Bash Basics

### 🔹 Navigation & Files
```bash
pwd                     # Show current directory
ls                      # List files in directory
ls -l                   # Long listing with permissions
cd /path/to/dir         # Change directory
cd ~                    # Go to home directory
cd -                    # Go back to previous directory
mkdir newfolder         # Create a directory
touch file.txt          # Create an empty file
rm file.txt             # Delete a file
rm -r folder/           # Delete a folder and its contents
```

### 🔹 Viewing Files
```bash
cat file.txt            # Show contents of a file
less file.txt           # View file one screen at a time (q to quit)
head file.txt           # Show first 10 lines
tail file.txt           # Show last 10 lines
tail -f file.txt        # Continuously monitor a file (like logs)
```

### 🔹 File Editing & Permissions
```bash
nano file.txt           # Simple terminal editor
chmod +x script.sh      # Make a script executable
chown user:group file   # Change file ownership
```

### 🔹 Searching & Filtering
```bash
grep 'word' file.txt    # Search for word in file
grep -r 'word' .        # Search recursively in current dir
find . -name "*.log"    # Find files by name
```

### 🔹 System Info
```bash
df -h                   # Disk space usage
du -sh folder/          # Size of a folder
top                     # Live system monitor
htop                    # Better version of top (install with `sudo apt install htop`)
free -h                 # Show RAM usage
uptime                  # Show how long the system has been running
```

### 🔹 Package Management (Ubuntu/Debian)
```bash
sudo apt update         # Update package list
sudo apt upgrade        # Upgrade installed packages
sudo apt install pkg    # Install a package
sudo apt remove pkg     # Remove a package
```
---
## 📦 File Manipulation (mv, cp, rename, etc.)

### 🔹 mv – Move or Rename files/folders
```bash
mv file1.txt folder/                   # Move file1.txt into folder/
mv oldname.txt newname.txt            # Rename a file
mv pydicom-data-master data           # Rename/move folder to "data"
mv *.txt backup/                      # Move all .txt files to backup folder
```

> 💡 If the destination is an existing folder, `mv` moves the file(s) into it.  
> If the destination does not exist, it acts as a rename.


### 🔹 cp – Copy files and folders
```bash
cp file.txt copy.txt                  # Copy a file
cp -r dir1 dir2                       # Copy a folder recursively
cp *.jpg images/                      # Copy all .jpg files to images/
```

#### 🔧 cp Common Flags
| Flag   | Description                    |
|--------|--------------------------------|
| `-r`   | Recursively copy directories   |
| `-v`   | Verbose: show files being copied |
| `-u`   | Only copy if source is newer   |



### 🔹 rm – Delete files and directories
```bash
rm file.txt                           # Remove a file
rm -r folder/                         # Remove a folder and its contents
rm -rf folder/                        # Force remove folder and ignore errors
```

> ⚠️ Be careful with `rm -rf` — there’s no undo!



### 🔹 rename – Rename multiple files (batch renaming)
```bash
rename 's/.txt/.bak/' *.txt           # Rename all .txt to .bak
rename 's/-/_/' *                     # Replace dashes with underscores in filenames
```

> On Ubuntu, install it with `sudo apt install rename`.  
> It uses Perl-style regex patterns.



### 🔹 file – Check file type
```bash
file myfile                          # Tells you what kind of file it is
```

> Useful when dealing with unknown or downloaded files.



### 🛠 Tip:
- Combine with `find`, `xargs`, and wildcards for mass operations.
- Always test on dummy files when doing batch renames or deletes!

---

## 🔍 Text Searching with grep

### 🔹 Basic usage
```bash
grep "hello" file.txt                # Search for lines containing 'hello'
grep -r "main()" src/               # Recursive search in directory
grep -n "error" file.txt            # Show line numbers with matches
grep -v "DEBUG" file.txt            # Invert match: show lines that DO NOT contain "DEBUG"
```



### 🔹 Useful Flags
| Flag  | Description |
|-------|-------------|
| `-i`  | Ignore case (match "ERROR", "error", etc.) |
| `-r`  | Recursive search in all subfolders |
| `-n`  | Show line numbers |
| `-v`  | Invert match (show lines that do NOT match) |
| `-E`  | Enable extended regex (e.g. `|`, `+`, `?` without backslashes) |



### 🔹 Combined Example: `-iE`
```bash
grep -iE "fail|error|critical" logs.txt
```
- `-i` makes it case-insensitive
- `-E` enables extended regular expressions
- Matches any line with **fail**, **error**, or **critical** (in any casing)



> 💡 Tip: Combine with `less` or `tail -f` for log monitoring  
> Example: `tail -f log.txt | grep -i error`


---

## 🐚 Bash Essentials

### 🔹 Variables & Exporting
```bash
MYVAR="hello"           # Define variable
echo $MYVAR             # Use variable
export MYVAR            # Export to subprocesses
```

### 🔹 Short Scripts & Loops
```bash
for f in *.txt; do echo $f; done        # Loop through files
if [ -f file.txt ]; then echo "exists"; fi  # Check if file exists
```

### 🔹 Redirection & Pipes
```bash
> file.txt              # Redirect output to file (overwrite)
>> file.txt             # Append output to file
cat file.txt | grep word     # Pipe output from one command to another
```

---

## 🐳 Docker Essentials

### 🔹 Containers
```bash
docker run hello-world          # Run test container
docker ps                       # List running containers
docker ps -a                    # List all containers (including stopped)
docker start <container>        # Start a stopped container
docker stop <container>         # Stop a running container
docker restart <container>      # Restart container
docker rm <container>           # Remove container
```

### 🔹 Images
```bash
docker images                   # List local images
docker pull image:tag           # Download image from Docker Hub
docker rmi image:tag            # Remove image
```

### 🔹 Running Containers
```bash
docker run -it ubuntu bash                        # Run Ubuntu container and open shell
docker run -d --name myapp -p 8080:80 image        # Run in detached mode with port mapping
```

### 🔹 Exec & Inspect
```bash
docker exec -it mycontainer bash                 # Open a shell inside running container
docker exec -it mycontainer cat /path/to/file    # Run a command inside container
docker logs mycontainer                          # Show container logs
docker inspect mycontainer                       # Detailed info (JSON)
```

### 🔹 Volumes & Bind Mounts
```bash
docker volume ls                                 # List volumes
docker volume create myvolume                    # Create volume
docker run -v myvolume:/data image               # Mount volume
docker run -v $(pwd)/data:/app/data image        # Bind local folder into container
```



## ✅ Tips
- Use `Ctrl + R` in terminal to search command history  
- Use `!!` to repeat last command, or `!<number>` to recall from history  
- Use `man <command>` to read the manual page for any command  

---

## 🧩 Common Flags & Parameters

### 🔹 General Shell & Bash
| Symbol / Flag | Meaning |
|---------------|---------|
| `~`           | User's home directory (e.g. `/home/username`) |
| `-` (dash)    | Refers to the previous working directory when used with `cd -` |
| `*`           | Wildcard for any number of characters (e.g., `*.txt`) |

### 🔹 File Commands
| Flag           | Used In         | Description |
|----------------|------------------|-------------|
| `-p`           | `mkdir -p`       | Create parent directories as needed (no error if exists) |

> 💡 **What does "parent directories" mean in `mkdir -p`?**  
> It refers to all folders in the path that are **above** the one you're creating.  
> Example:  
> ```bash
> mkdir -p ~/projects/dicom_test/data/incoming
> ```  
> If `projects`, `dicom_test`, or `data` don't exist, `-p` will create them automatically.  
> Without `-p`, this would fail unless the full path already exists.

| `+x`           | `chmod +x`       | Adds executable permission to a file |
| `-r`           | `rm -r`, `grep -r` | Recursive: apply to folders/subfolders |
| `-f`           | `rm -f`, `tail -f` | Force remove / Follow file in real-time |
| `-n`           | `cat -n`         | Number output lines |
| `-o`           | `curl -o`        | Write output to file instead of stdout |
| `-L`           | `curl -L`        | Follow redirects (Location headers) |

### 🔹 Docker
| Flag           | Used In                     | Description |
|----------------|------------------------------|-------------|
| `-d`           | `docker run -d`              | Run container in detached mode (in background) |
| `-p`           | `docker run -p 8080:80`      | Map host port to container port (HOST:CONTAINER) |
| `--name`       | `docker run --name`          | Assign a custom name to the container |
| `-v`           | `docker run -v ...`          | Mount a volume or bind a directory |
| `-it`          | `docker run -it`             | Run with interactive terminal support |
| `exec -it`     | `docker exec -it`            | Interact with a running container's shell |

---

## 🌐 Networking & Data Transfer (curl, wget, ping, etc.)

### 🔹 curl – Transfer data from or to a server
```bash
curl http://example.com                # Fetch a webpage (GET request)
curl -O http://example.com/file.zip    # Save file with original name
curl -o myfile.zip http://example.com/file.zip  # Save with custom name
curl -L http://short.url               # Follow redirects (like shortened URLs)
curl -u user:pass http://example.com   # Basic authentication
curl -X POST -d "key=value" http://example.com # POST request with data
```

#### 🔧 curl Common Flags
| Flag    | Description |
|---------|-------------|
| `-L`    | Follow redirects (Location headers) |
| `-o`    | Write output to a named file |
| `-O`    | Save file with original name |
| `-u`    | Provide username:password |
| `-X`    | Specify request method (GET, POST, PUT, etc.) |
| `-d`    | Send data in request body |

> 💡 `curl` is very script-friendly and commonly used in APIs, automation, and Docker setups.


### 🔹 wget – Download files from the internet
```bash
wget http://example.com/file.zip       # Download file
wget -c http://example.com/file.zip    # Continue interrupted download
```

> Unlike `curl`, `wget` is mainly for downloading files and supports recursive downloads with `-r`.



### 🔹 ping – Test connectivity to a host
```bash
ping google.com                        # Check if host is reachable
ping -c 4 google.com                   # Limit to 4 packets
```



### 🔹 ifconfig / ip – Network configuration
```bash
ifconfig                               # Show network interfaces (older, may need install)
ip a                                   # Modern alternative to ifconfig
ip r                                   # Show routing table
```

> On modern Linux (Ubuntu), prefer `ip` commands over `ifconfig`.


### 🔹 netstat / ss – Check open ports and sockets
```bash
netstat -tuln                          # List listening ports (requires `net-tools`)
ss -tuln                               # Faster and more modern replacement for netstat
```



### 🔹 nslookup / dig – DNS tools
```bash
nslookup example.com                   # Get IP address of a domain
dig example.com                        # More detailed DNS lookup
```



### 🔹 traceroute – Show the path to a remote host
```bash
traceroute example.com                 # Trace the route packets take
```

> Might require installation: `sudo apt install traceroute`



### 🛠 Tip:
Use `curl` or `wget` in Dockerfiles, bash scripts, or system setup scripts to automate downloads and API calls.


## 📝 To Add Later
- Docker Compose  
- Systemd basics  
- SSH & SCP  
- Git essentials  
- Bash functions & aliases  
