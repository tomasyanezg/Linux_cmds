🔹 1. Create Your VM
VirtualBox

OS: Ubuntu Server 22.04 LTS (or Desktop if you want GUI)

Specs: 2 CPU, 4GB RAM, 20GB disk (you can expand later)

Networking: Bridged Adapter (for easy access from your host or LAN)

---

🔹 2. Initial System Setup (Inside the VM)
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io docker-compose dcmtk curl unzip # The full command silently installs Docker, Docker Compose, DICOM tools, curl for web requests, and unzip support — all in one go using apt.

sudo systemctl enable docker && sudo systemctl start docker
```

---
🔹 3. Run Orthanc (DICOM Server) in Docker

```bash
docker run -d --name orthanc \
  -p 8042:8042 -p 4242:4242 \
  jodogne/orthanc
```

Web UI: http://localhost:8042 (user: orthanc, password: orthanc)

DICOM port: 4242

Let’s go step by step and clarify the port mapping logic and how we know which ports Orthanc uses.

🔹 -p <host>:<container> — What does it do?
This flag tells Docker:

“Take a port on my host machine (<host>) and forward it to a port inside the container (<container>).”

8042 on the host → 8042 in the container

4242 on the host → 4242 in the container

That’s just port mapping. But…
🔍 So how do we know that port 8042 is the web UI, and 4242 is DICOM?
That info comes from Orthanc's documentation and the way the software itself is configured by default.

In the official Orthanc Docker image, the default behavior is:


Service	Port	Description
Web Interface	8042	REST API + web UI
DICOM Interface	4242	Accepts/store/send DICOM images
These ports are hardcoded defaults in Orthanc’s config (/etc/orthanc/orthanc.json inside the container), unless you override them.

You can confirm this by looking into:
```bash
docker exec -it orthanc cat /etc/orthanc/orthanc.json
```
(Or by checking the official GitHub repo).