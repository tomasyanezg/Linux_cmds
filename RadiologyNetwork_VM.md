# 🧠 RadiologyNetwork VM – DICOM Lab Setup Guide (Ubuntu 22.04 + Docker + Orthanc)

> A modular guide for building and extending a local PACS test environment using Docker, Orthanc, and DICOM tools.

---

# 🔧 SYSTEM SETUP

## OS: Ubuntu Server 22.04 LTS (VirtualBox or VMware)

- 2 CPUs, 4 GB RAM, 20 GB disk
- Network: Bridged Adapter (NAT w/ port forwarding also possible)

### Initial Configuration
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl unzip
```

---

# 🐳 DOCKER SETUP

## Install Docker (Official Method)
```bash
sudo apt-get install ca-certificates curl gnupg lsb-release -y

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo systemctl enable docker
sudo systemctl start docker
```

---

# 📦 ORTHANC (PACS) SETUP

## Run Orthanc Docker Container
```bash
docker run -d --name orthanc \
  -p 8042:8042 -p 4242:4242 \
  jodogne/orthanc
```

- Web Interface: [http://localhost:8042](http://localhost:8042)
- Default Login: `orthanc / orthanc`

### Auto-start on Reboot
```bash
docker update --restart unless-stopped orthanc
```

---

# 🧰 DICOM TOOLS INSTALLATION

```bash
sudo apt install -y dcmtk gdcm
```

Tools installed:
- `storescu` – Send DICOM files
- `dcmdump` – View DICOM tags
- `dcmodify` – Modify DICOM headers
- `gdcmconv` – Convert or decompress DICOM files

---

# 📁 DICOM FILE MANAGEMENT

## Download Sample DICOM Files
```bash
mkdir -p ~/dicom_test/data && cd ~/dicom_test/data
curl -LO https://github.com/pydicom/pydicom/releases/download/v1.2/MR_small.dcm
cp MR_small.dcm 693_UNCI.dcm
```

## Send File to Orthanc
```bash
storescu 127.0.0.1 4242 ~/dicom_test/data/693_UNCI.dcm
```

---

# 🧪 DICOM ANALYSIS SCRIPT

## Create `dicom_info.sh`:
```bash
nano ~/dicom_test/dicom_info.sh
```

Paste:
```bash
#!/bin/bash

if [ -z "$1" ]; then
  echo "❌ Usage: $0 <dicom-file>"
  exit 1
fi

DICOM_FILE="$1"

if [ ! -f "$DICOM_FILE" ]; then
  echo "❌ File not found: $DICOM_FILE"
  exit 2
fi

TRANSFER_SYNTAX=$(dcmdump "$DICOM_FILE" | grep 'Transfer Syntax UID' | awk '{for(i=1;i<=NF;i++){if($i ~ /\=/){print $i}}}' | cut -d= -f2)
MODALITY=$(dcmdump "$DICOM_FILE" | grep '(0008,0060)' | awk -F'[][]' '{print $2}')
ROWS=$(dcmdump "$DICOM_FILE" | grep '(0028,0010)' | awk '{print $3}')
COLUMNS=$(dcmdump "$DICOM_FILE" | grep '(0028,0011)' | awk '{print $3}')
PATIENT_NAME=$(dcmdump "$DICOM_FILE" | grep '(0010,0010)' | awk -F'[][]' '{print $2}')
PIXEL_SPACING=$(dcmdump "$DICOM_FILE" | grep '(0028,0030)' | awk -F'[][]' '{print $2}')

echo "🧾 DICOM File Info: $DICOM_FILE"
echo "-------------------------------"
echo "📌 Modality:        ${MODALITY:-Unknown}"
echo "🖼️  Resolution:      ${ROWS:-?} x ${COLUMNS:-?} pixels"
echo "📐 Pixel Spacing:   ${PIXEL_SPACING:-Unknown}"
echo "🧬 Patient Name:    ${PATIENT_NAME:-(anonymized)}"
echo "🔄 Transfer Syntax: ${TRANSFER_SYNTAX:-Unknown}"
echo "-------------------------------"

if [[ "$TRANSFER_SYNTAX" == "1.2.840.10008.1.2.1" || "$TRANSFER_SYNTAX" == "LittleEndianExplicit" ]]; then
  echo "✅ Compatible: Explicit VR Little Endian"
else
  echo "⚠️  Unsupported Transfer Syntax for Orthanc (might fail to send)"
fi
```

Make executable:
```bash
chmod +x ~/dicom_test/dicom_info.sh
```

Run:
```bash
~/dicom_test/dicom_info.sh ~/dicom_test/data/693_UNCI.dcm
```

---

# ✅ NEXT STEPS (Optional Additions)

- Watchdog script to detect new DICOMs
- AI simulation container using Python or Bash
- Export Orthanc studies for re-processing
- Remote access setup or HTTPS for PACS
- Multi-VM networking for distributed testing

---

# 📎 NOTES

- IP `127.0.0.1` = localhost inside the VM
- You can expose Orthanc to the network via Bridged Adapter or NAT port forward
- Files with unsupported transfer syntax (e.g., JPEG 2000) need conversion

---

> Author: tomasyanezg   
> Hostname: `radiologynetwork`  
> Version: `v0.1` (Last Updated: 2025-04-22)
