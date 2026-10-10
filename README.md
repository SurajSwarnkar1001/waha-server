# WAHA

<p align="center">
  <img src="./logo.png" style='border-radius: 50%' width='150'/>
</p>

**WAHA** - **W**hats**A**pp **H**TTP **A**PI (REST API) that you can install on your own server and run in less than 5 minutes!

[![Docker Pulls](https://img.shields.io/docker/pulls/devlikeapro/waha)](https://hub.docker.com/r/devlikeapro/waha)

- Documentation: [https://waha.devlike.pro/](https://waha.devlike.pro/)
- Dashboard Example: [https://waha.devlike.pro/dashboard](https://waha.devlike.pro/dashboard)
- Swagger Example: [https://waha.devlike.pro/swagger](https://waha.devlike.pro/swagger)

# Tables of Contents

<!-- toc -->

- [Quick start](#quick-start)
  * [Requirements](#requirements)
  * [Send your first message](#send-your-first-message)
    + [1. Download image](#1-download-image)
    + [2. Run WhatsApp HTTP API](#2-run-whatsapp-http-api)
    + [3. Start a new session](#3-start-a-new-session)
    + [4. Get and scan QR](#4-get-and-scan-qr)
    + [5. Get the screenshot](#5-get-the-screenshot)
    + [6. Send a text message](#6-send-a-text-message)
  * [What is next?](#what-is-next)
- [Development](#development)
  * [Start the project](#start-the-project)

<!-- tocstop -->

# Quick start

## Requirements

Only thing that you must have - installed docker. Please follow the original
instruction <a href="https://docs.docker.com/get-docker/" target="_blank" rel="noopener">how to install docker -></a>.

When you are ready - come back and follows the below steps to send the first text message to WhatsApp via HTTP API!

## Send your first message

Let's go over steps that allow you to send your first text message via WhatsApp HTTP API!

### 1. Download image

Assuming you have installed [Docker](https://docs.docker.com/get-docker/), let's download the image.


```bash
docker pull devlikeapro/waha
```


```bash
docker login -u devlikeapro -p {KEY}
docker pull devlikeapro/waha-plus
docker logout
```

Read more about how to get `PASSWORD` for [**➕ WAHA Plus**](https://waha.devlike.pro/docs/how-to/waha-plus/)

### 2. Run WhatsApp HTTP API

Run WhatsApp HTTP API:

```bash
docker run -it --rm -p 3000:3000/tcp --name waha devlikeapro/waha

# It prints logs and the last line must be
# WhatsApp HTTP API is running on: http://[::1]:3000
```

Open the link in your browser [http://localhost:3000/](http://localhost:3000/) and you'll see API documentation
(Swagger).


### 3. Start a new session

To start a new session you should have your mobile phone with installed WhatsApp application close to you.

Please go and read how what we'll need to a bit
later:
<a href="https://faq.whatsapp.com/381777293328336/?helpref=hc_fnav" target="_blank">
How to log in - the instruction on WhatsApp site
</a>

When your ready - find `POST /api/sessions`, click on **Try it out**, then **Execute** a bit below.


The example payload:
```json
{
  "name": "default"
}
```


By using the request with `name` values you can start multiple session (WhatsApp accounts) inside the single docker container in Plus


### 4. Get and scan QR

Find `GET /api/screenshot` and execute it, it shows you QR code.


**Scan the QR with your cell phone's WhatsApp app.**


### 5. Get the screenshot

Execute `GET /api/screenshot` after a few seconds after scanning the QR - it'll show you the screenshot of you Whatsapp
instance. If you can get the actual screenshot - then you're ready to start sending messages!


### 6. Send a text message

Let's send a text message - find `POST /api/sendText`  in [swagger](http://localhost:3000/) and change `chatId` this
way: use a phone international phone number without `+` symbol and add `@c.us` at the end.

For phone number `12132132131` the `chatId` is  `12132132131@c.us`.

The example payload:
```json
{
  "chatId": "12132132130@c.us",
  "text": "Hi there!",
  "session": "default"
}
```

Also, you can use `curl` and send POST request like this:

```bash
# Phone without +
export PHONE=12132132130
curl -d "{\"chatId\": \"${PHONE}@c.us\", \"text\": \"Hello from WhatsApp HTTP API\" }" -H "Content-Type: application/json" -X POST http://localhost:3000/api/sendText
```

## What is next?
[Go and read the full documentation!](https://waha.devlike.pro/docs/overview/introduction/)

# Development

## Start the project
1. Clone the repository
2. Make sure you're using node>=22 (check [.nvmrc](/.nvmrc) to get the version)
3. Install the **whatsapp-rust-bridge prerequisites**:
 ```bash
# Bun runtime used by whatsapp-rust-bridge prepare scripts
curl -fsSL https://bun.sh/install | bash -s -- bun-v1.3.9

# Rust nightly toolchain used for whatsapp-rust-bridge
curl -fsSL https://sh.rustup.rs | bash -s -- -y --default-toolchain nightly-2026-01-30
rustup target add wasm32-unknown-unknown
cargo install wasm-pack --vers 0.14.0 --locked
```

4. Run the following commands:
```bash
# Install dependencies
yarn install
# Fetch and compile proto files
yarn gows:proto
# Run
yarn start
# open http://localhost:3000
```

## ☁️ Self-Hosting WAHA on AWS EC2 (Free Tier)
# WAHA (WhatsApp HTTP API) - Full Self-Hosting Guide (AWS EC2 & Docker)

Is document me **WAHA (WhatsApp HTTP API)** ko **AWS EC2** par setup karne, Docker install karne, Dashboard login karne, aur Credentials (API Key & Passwords) ko manage/reset karne ki poori jankari di gayi hai.

---

## 📌 Index / Table of Contents
1. [WAHA Kya Hai & Platform Comparison](#1-waha-kya-hai--platform-comparison)
2. [AWS EC2 Instance Setup (Step-by-Step)](#2-aws-ec2-instance-setup-step-by-step)
3. [Docker Installation & Firewall Configuration](#3-docker-installation--firewall-configuration)
4. [WAHA Launch (Docker Command)](#4-waha-launch-docker-command)
5. [Dashboard Access & QR Code Scan](#5-dashboard-access--qr-code-scan)
6. [Credentials (API Key, Username, Password) Change & Reset Karna](#6-credentials-api-key-username-password-change--reset-karna)
7. [Useful Maintenance Commands (Troubleshooting)](#7-useful-maintenance-commands-troubleshooting)
8. [API Usage Examples (Send Message)](#8-api-usage-examples-send-message)

---

## 1. WAHA Kya Hai & Platform Comparison

**WAHA (WhatsApp HTTP API)** ek open-source software hai jo aapke WhatsApp account ko REST HTTP API me convert kar deta hai. Isse aap automated text, images, files, aur webhooks send/receive kar sakte hain.

### ❓ Render Free Tier vs AWS EC2:

| Feature | Render Free Tier | AWS EC2 (Free Tier) |
| :--- | :--- | :--- |
| **Storage (Persistent)** | ❌ Ephemeral (Data delete ho jata hai) | ✅ 30 GB EBS Volume (Session Safe) |
| **Server Sleep** | ❌ 15 min me sleep ho jata hai | ✅ 24/7 Active (No Sleep) |
| **RAM** | ❌ 512 MB (Chromium crash) | ✅ 1 GB RAM (t3.micro / t2.micro) |
| **WhatsApp Session** | ❌ Daily Logout / Re-scan | ✅ Permanent Login (Until Manual Logout) |

---

## 2. AWS EC2 Instance Setup (Step-by-Step)

1. [AWS Console](https://aws.amazon.com) me Login karein aur **EC2** Service par jayein.
2. **Launch Instance** (Orange Button) par click karein:
   * **Name:** `waha-server`
   * **Application & OS Image (AMI):** Select **`Ubuntu`** -> **`Ubuntu Server 22.04 LTS (HVM), SSD Volume Type`** (Free Tier Eligible).
   * **Instance Type:** Select **`t3.micro`** ya **`t2.micro`**.
   * **Key Pair:** Create New Key Pair -> Name: `waha-key`, Type: `RSA`, Format: `.pem` (Download & Save).
3. **Network Settings (Security Group):**
   * Edit par click karein.
   * **Rule 1:** Allow `SSH` (Port `22`) from `Anywhere (0.0.0.0/0)`.
   * **Rule 2 (Add Security Group Rule):** Select `Custom TCP`, Port range `3000`, Source `Anywhere (0.0.0.0/0)`.
4. **Configure Storage:** `8 GiB` ko badal kar **`30 GiB`** kar dein.
5. **Launch Instance** par click karein.

---

## 3. Docker Installation & Firewall Configuration

EC2 Instance me **1-Click Connect** (Browser Terminal) ya SSH se login karein aur ye commands run karein:

```bash
# 1. System Packages Update & Upgrade
sudo apt update && sudo apt upgrade -y

# 2. Official Docker Script Download & Install
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 3. Ubuntu User ko Docker Group me add karein
sudo usermod -aG docker ubuntu
newgrp docker
```

---

## 4. WAHA Launch (Docker Command)

WAHA ko persistent session storage ke saath launch karne ke liye ye command chalaein:

```bash
docker run -d \
  --name waha \
  --restart always \
  -p 3000:3000 \
  -v waha_sessions:/app/.sessions \
  -e WHATSAPP_API_KEY="mysecretkey123" \
  -e WAHA_DASHBOARD_USERNAME="admin" \
  -e WAHA_DASHBOARD_PASSWORD="adminpassword123" \
  devlikeapro/waha
```

> ⚠️ **Key Parameters:**
> * `-p 3000:3000`: Port 3000 ko external traffic ke liye open karta hai.
> * `-v waha_sessions:/app/.sessions`: WhatsApp QR Code Login Session ko storage volume me permanent save karta hai.
> * `--restart always`: Server restart hone par WAHA automatically restart ho jata hai.

---

## 5. Dashboard Access & QR Code Scan

1. Apne Server ka **Public IP** pata karein:
   ```bash
   curl ifconfig.me
   ```
2. Apne browser (Chrome/Edge) me ye URL kholein:
   ```text
   http://YOUR_SERVER_PUBLIC_IP:3000/dashboard
   ```
3. Login Details:
   * **Username:** `admin` (ya jo aapne set kiya ho)
   * **Password:** `adminpassword123` (ya jo aapne set kiya ho)
   * **API Key:** `mysecretkey123`
4. Dashboard me **Start Session** button dabayein aur apne mobile WhatsApp app (Linked Devices) se QR Code scan karein.

---

## 6. Credentials (API Key, Username, Password) Change & Reset Karna

Jab bhi aapko API Key, Username, ya Password change karna ho, to ye **2 steps** kijiye:

### Step 1: Current Container Stop & Remove Karein
*(Note: Isse aapka WhatsApp Login Session delete NAHI hoga, kyunki wo `waha_sessions` volume me safe hai).*

```bash
docker stop waha && docker rm waha
```

### Step 2: Naye Environment Variables ke saath Restart Karein

```bash
docker run -d \
  --name waha \
  --restart always \
  -p 3000:3000 \
  -v waha_sessions:/app/.sessions \
  -e WHATSAPP_API_KEY="YOUR_NEW_API_KEY" \
  -e WAHA_DASHBOARD_USERNAME="YOUR_NEW_USERNAME" \
  -e WAHA_DASHBOARD_PASSWORD="YOUR_NEW_PASSWORD" \
  devlikeapro/waha
```

---

## 7. Useful Maintenance Commands (Troubleshooting)

| Task | Command |
| :--- | :--- |
| **Check Container Running Status** | `docker ps` |
| **Check Live Logs / Errors** | `docker logs waha` |
| **Follow Live Logs in Realtime** | `docker logs -f waha` |
| **Restart WAHA Container** | `docker restart waha` |
| **Check Server Public IP** | `curl ifconfig.me` |
| **Check Server Memory (RAM) Usage** | `free -h` |
| **Check Server Storage (Disk) Space** | `df -h` |

---

## 8. API Usage Examples (Send Message)

### 🔹 Send Text Message via cURL / HTTP POST

```bash
curl -X 'POST' \
  'http://YOUR_SERVER_PUBLIC_IP:3000/api/sendText' \
  -H 'accept: application/json' \
  -H 'x-api-key: mysecretkey123' \
  -H 'Content-Type: application/json' \
  -d '{
  "chatId": "919876543210@c.us",
  "text": "Hello! WAHA WhatsApp API is working fine.",
  "session": "default"
}'
```

---

*Guide Created for WAHA Self-Hosting on AWS EC2.*



