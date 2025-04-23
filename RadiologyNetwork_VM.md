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

### Tools installed:
| Tool       | Description                                      |
|------------|--------------------------------------------------|
| `storescu` | Send (C-STORE) DICOM files to a PACS             |
| `dcmdump`  | View full metadata of a DICOM file               |
| `dcmodify` | Edit DICOM headers (patient name, ID, etc.)      |
| `gdcmconv` | Convert between DICOM transfer syntaxes          |

---

# 📁 DICOM FILE MANAGEMENT

## Download Sample DICOM Files
```bash
mkdir -p ~/dicom_test/data && cd ~/dicom_test/data
curl -LO https://github.com/pydicom/pydicom/releases/download/v1.2/MR_small.dcm
cp MR_small.dcm 693_UNCI.dcm
```

## Send File to Orthanc (C-STORE)
```bash
storescu 127.0.0.1 4242 ~/dicom_test/data/693_UNCI.dcm
```



# 🔧 DICOM & ORTHANC COMMAND REFERENCE

## 📤 Send or Upload a DICOM File to Orthanc

### Using DICOM (storescu):
```bash
storescu 127.0.0.1 4242 path/to/file.dcm
```

### Using REST API (upload from disk):
```bash
curl -u orthanc:orthanc -X POST http://localhost:8042/instances \
     -H "Content-Type: application/dicom" \
     --data-binary @~/dicom_test/data/693_UNCI.dcm
```


## 🧼 Modify Tags in a DICOM File (Before Sending)

### Change Patient Name:
```bash
dcmodify -i "(0010,0010)=NewName" ~/dicom_test/data/693_UNCI.dcm
```

### Change Patient ID:
```bash
dcmodify -i "(0010,0020)=NEW_ID_123" file.dcm
```

⚠️ You can also script mass changes before upload.



## ❌ Delete a DICOM Instance (via REST API)

### Get Instance ID (example):
```bash
curl -u orthanc:orthanc http://localhost:8042/instances
```

### Then delete:
```bash
curl -u orthanc:orthanc -X DELETE http://localhost:8042/instances/<InstanceID>
```

You can also delete **series**, **studies**, or **patients** using their respective endpoints.



## 📑 List Orthanc Resources

| Action                 | Command                                                                 |
|------------------------|-------------------------------------------------------------------------|
| List all patients      | `curl -u orthanc:orthanc http://localhost:8042/patients`                |
| List studies           | `curl -u orthanc:orthanc http://localhost:8042/studies`                 |
| List series            | `curl -u orthanc:orthanc http://localhost:8042/series`                  |
| List instances         | `curl -u orthanc:orthanc http://localhost:8042/instances`               |
| View full tags         | `curl -u orthanc:orthanc http://localhost:8042/instances/<id>/tags`     |



## 🔍 Search DICOM by Patient Name (wildcard match)

### Using REST API:
```bash
curl -u orthanc:orthanc -X POST http://localhost:8042/tools/find \
  -H "Content-Type: application/json" \
  -d '{"Level":"Patient", "Query":{"PatientName":"CQ*"}}'
```

### Using `findscu` (DCMTK):
```bash
findscu -v -S -k QueryRetrieveLevel=PATIENT -k PatientName="CQ*" 127.0.0.1 4242
```


## 🧪 Anonymize a DICOM Instance via REST

```bash
INSTANCE_ID=$(curl -s -u orthanc:orthanc http://localhost:8042/instances | jq -r '.[0]')

curl -u orthanc:orthanc -X POST \
  http://localhost:8042/instances/$INSTANCE_ID/anonymize \
  -H "Content-Type: application/json" \
  -d '{}'
```

Returns the ID of a **new anonymized** instance in Orthanc.


## 🔁 Download an Image from Orthanc

Download a DICOM instance:
```bash
curl -u orthanc:orthanc http://localhost:8042/instances/<id>/file > download.dcm
```

Download as PNG (if PNG plugin or DICOMWeb is enabled):
```bash
curl -u orthanc:orthanc http://localhost:8042/instances/<id>/preview > image.png
```


## 🛠 Other Useful Actions

| Action                                | Command                                                                 |
|---------------------------------------|-------------------------------------------------------------------------|
| Get system info                       | `curl -u orthanc:orthanc http://localhost:8042/system`                  |
| View Orthanc configuration (live)     | `curl -u orthanc:orthanc http://localhost:8042/configuration`           |
| Reconstruct missing series            | `curl -u orthanc:orthanc -X POST http://localhost:8042/tools/reconstruct` |
| Create a dummy DICOM                  | `curl -u orthanc:orthanc -X POST http://localhost:8042/tools/create-dicom -d '{}'` |



## 🔒 Pro tip: Secure Orthanc before going live!

Edit `Orthanc.json` or pass with Docker:
```json
"AuthenticationEnabled": true,
"RegisteredUsers": {
  "orthanc": "yourStrongPassword"
}
```

Or add basic auth to all endpoints:
```bash
curl -u orthanc:yourStrongPassword ...
```

---

# 🧪 DICOM ANALYSIS SCRIPT

## Create `dicom_info.sh`
```bash
nano ~/dicom_test/dicom_info.sh
```

### Paste the following script:
```bash
#!/bin/bash

# ==========================================
# DICOM Tag Inspector & Compatibility Checker
# ==========================================

# ✅ 1. Validate input
if [ -z "$1" ]; then
  echo "❌ Usage: $0 <dicom-file>"
  exit 1
fi

DICOM_FILE="$1"

if [ ! -f "$DICOM_FILE" ]; then
  echo "❌ File not found: $DICOM_FILE"
  exit 2
fi

# ✅ 2. Extract metadata using dcmdump

# Extract Transfer Syntax (raw value from tag 0002,0010)
TRANSFER_SYNTAX=$(dcmdump "$DICOM_FILE" | awk '/\(0002,0010\)/ {gsub(/^.*= */, "", $0); print $1; exit}')

# Other useful DICOM tags
MODALITY=$(dcmdump "$DICOM_FILE" | grep '(0008,0060)' | awk -F'[][]' '{print $2}')
ROWS=$(dcmdump "$DICOM_FILE" | grep '(0028,0010)' | awk '{print $3}')
COLUMNS=$(dcmdump "$DICOM_FILE" | grep '(0028,0011)' | awk '{print $3}')
PATIENT_NAME=$(dcmdump "$DICOM_FILE" | grep '(0010,0010)' | awk -F'[][]' '{print $2}')
PATIENT_ID=$(dcmdump "$DICOM_FILE" | grep '(0010,0020)' | awk -F'[][]' '{print $2}')
PIXEL_SPACING=$(dcmdump "$DICOM_FILE" | grep '(0028,0030)' | awk -F'[][]' '{print $2}')

# ✅ 3. Output formatted info
echo "🧾 DICOM File Info: $DICOM_FILE"
echo "-------------------------------"
echo "📌 Modality:        ${MODALITY:-Unknown}"
echo "🖼️  Resolution:      ${ROWS:-?} x ${COLUMNS:-?} pixels"
echo "📐 Pixel Spacing:   ${PIXEL_SPACING:-Unknown}"
echo "🧬 Patient Name:    ${PATIENT_NAME:-(anonymized)}"
echo "🆔 Patient ID:      ${PATIENT_ID:-Unknown}"
echo "🔄 Transfer Syntax: ${TRANSFER_SYNTAX:-Unknown}"
echo "-------------------------------"

# ✅ 4. Check Transfer Syntax Compatibility
if [[ "$TRANSFER_SYNTAX" == "1.2.840.10008.1.2.1" || \
      "$TRANSFER_SYNTAX" == "LittleEndianExplicit" || \
      "$TRANSFER_SYNTAX" == "1.2.840.10008.1.2.2" || \
      "$TRANSFER_SYNTAX" == "BigEndianExplicit" || \
      "$TRANSFER_SYNTAX" == "1.2.840.10008.1.2"   || \
      "$TRANSFER_SYNTAX" == "ImplicitVRLittleEndian" ]]; then
  echo "✅ Compatible: Standard Uncompressed Transfer Syntax (Little/Big Endian, Implicit/Explicit)"
else
  echo "⚠️  Possibly Unsupported Transfer Syntax (e.g., JPEG, JPEG2000, RLE)"
fi
```


## Make it executable:
```bash
chmod +x ~/dicom_test/dicom_info.sh
```


## Run it on a DICOM file:
```bash
~/dicom_test/dicom_info.sh ~/dicom_test/data/693_UNCI.dcm
```

### ✅ Sample Output:
```
🧾 DICOM File Info: ./data/693_UNCI.dcm
-------------------------------
📌 Modality:        CT
🖼️  Resolution:      512 x 512 pixels
📐 Pixel Spacing:   0.478516\0.478516
🧬 Patient Name:    CQ500-CT-310
🆔 Patient ID:      CQ500-CT-310
🔄 Transfer Syntax: LittleEndianExplicit
-------------------------------
✅ Compatible: Standard Uncompressed Transfer Syntax (Little/Big Endian, Implicit/Explicit)
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
