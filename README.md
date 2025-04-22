# 🐧 Linux_cmds

**About:**  
A simple and growing collection of useful Linux commands, shell tricks, Docker examples, and server configuration notes. This repository helps me remember and organize common terminal workflows—like a personal Linux cheat sheet 📘.

---

## 📁 Repository Structure

```
Linux_cmds/
├── README.md        # You are here :)
├── docker.md        # Docker & Docker Compose examples
├── ssh.md           # SSH usage & remote access
├── filesystem.md    # File navigation, search, permissions
├── tailscale.md     # Tailscale VPN usage & tips
└── etc...
```

---

## 🧠 Sample Notes & Syntax

### 📂 File Management

```bash
# List contents with human-readable sizes
ls -lh

# Copy a file
cp source.txt destination.txt

# Remove a directory and its contents
rm -rf ./my-folder
```

---

### 🐋 Docker

```bash
# See all running containers
docker ps

# Stop and remove a container
docker stop my_container && docker rm my_container

# Prune unused images
docker image prune -a
```

---

### 🔒 SSH Access

```bash
# Connect to remote machine via SSH
ssh user@remote-ip

# Use a custom port
ssh -p 2222 user@remote-ip
```

---

### 🧪 Markdown Styling Tips

- `inline code` is wrapped in backticks  
- Code blocks use triple backticks (\`\`\`)  
- Use `###` for headings, `-` or `*` for bullets  
- Emphasis: *italics*, **bold**, `monospace`

---

## 📌 Tips

- Keep commands short and tested ✅  
- Use VS Code's Markdown preview (`Ctrl+Shift+V`)  
- Write brief descriptions above each code block  
- Organize by topic and use links for navigation

---

## 🔄 Contributing (for future me)

If I ever make this public, contributions are welcome via PRs and Issues!

---

Made with ❤️ on my homelab setup (`lacuevaserver`)
