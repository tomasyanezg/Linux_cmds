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

---

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

## 📝 To Add Later
- Docker Compose  
- Systemd basics  
- Networking (`curl`, `ifconfig`, `ip`)  
- SSH & SCP  
- Git essentials  
- Bash functions & aliases  
