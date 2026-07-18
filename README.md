<p align="center">
  <img src="assets/logo.png" alt="Logo" width="128">
</p>

<h1 align="center">Turn Your Android Phone into a Server Using Termux</h1>

<p align="center">
  <img src="assets/banner.png" alt="Banner" width="800">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Android-green" alt="Android">
  <img src="https://img.shields.io/badge/Terminal-Termux-black" alt="Termux">
  <img src="https://img.shields.io/badge/Tunnel-Cloudflare-orange" alt="Cloudflare">
  <img src="https://img.shields.io/badge/Contributions-Welcome-brightgreen" alt="Contributions Welcome">
</p>

## Video Tutorial

[![Watch the video](https://img.youtube.com/vi/62LxkfvY_pQ/maxresdefault.jpg)](https://youtu.be/62LxkfvY_pQ?si=UwOQoLs7PcdzHM2V)

---

This guide explains how to turn your Android phone into a functional server using Termux. Termux is a powerful terminal emulator that allows you to run a Linux-like environment on your phone. By the end of this guide, you'll have a fully functional web server hosted from your device.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Install Termux](#step-1-install-termux)
- [Step 2: Install Required Tools](#step-2-install-required-tools)
- [Step 3: Cloudflare Tunnel Setup](#step-3-cloudflare-tunnel-setup)
- [Step 4: Run Server](#step-4-run-server)
- [Contributing](#contributing)
- [License](#license)

---

## Prerequisites

1. An Android phone.
2. A domain name.
3. Termux installed ([download from F-Droid](https://f-droid.org/)).
4. A stable internet connection.

---

## Step 1: Install Termux

- Download Termux from [F-Droid](https://f-droid.org/).
- Open Termux and update the packages:
  ```bash
  pkg update && pkg upgrade
  ```

---

## Step 2: Install Required Tools

1. **Install `proot-distro` in Termux:**
   ```bash
   pkg install proot-distro
   proot-distro install debian
   proot-distro login debian
   ```

2. **Update Debian packages:**
   Once inside Debian, update and upgrade the packages first:
   ```bash
   apt update && apt upgrade
   ```

3. **Install Cloudflared:**

   ```bash
   apt install wget
   wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm
   ```
   or
   ```bash
   wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64
   ```

   > **Note:** It depends on your system architecture. You can check using `uname -m`.

   Then run the following commands:
   ```bash
   mv cloudflared-linux-arm* cloudflared
   chmod +x cloudflared
   mv cloudflared $PREFIX/bin/
   ```

   Verify the installation:
   ```bash
   cloudflared version
   ```

4. **Install Git, Node.js, and npm** (or any tool you need for your server):
   ```bash
   apt install git nodejs
   ```

   > **Note:** We installed Debian to bypass Android restrictions.

---

## Step 3: Cloudflare Tunnel Setup

1. Go to your Cloudflare dashboard in the **Account Home** tab and click on **Add a domain**.

2. Go to your domain name provider, delete all existing nameservers and add the Cloudflare ones.

   Use the nameservers shown in **your Cloudflare dashboard** (they are unique per account). For example:

   > `daisy.ns.cloudflare.com`
   >
   > `jay.ns.cloudflare.com`

   > **Note:** This process might take some time to verify, but once it's completed they will send you an email.

3. When it's completed, go to your Cloudflare account again, go to the **Access** tab, click **Launch Zero Trust**, and complete the setup.

4. On your phone (not in Termux, in a browser) go to [https://dash.cloudflare.com/argotunnel](https://dash.cloudflare.com/argotunnel) and login with your Cloudflare account. You will get the `cert.pem` file.

5. Move the `cert.pem` to Termux:
   - Add the `cert.pem` to your Downloads folder.
   - Run the following commands in Termux:
   ```bash
   termux-setup-storage
   mkdir -p ~/.cloudflared/
   cp /storage/emulated/0/Download/cert.pem ~/.cloudflared/
   ```

6. Create the tunnel:
   ```bash
   cloudflared tunnel create <tunnel-name>
   ```

7. Create the config file:
   ```bash
   nano ~/.cloudflared/config.yml
   ```

8. Add the following content:
   ```yaml
   tunnel: <tunnel-id>
   credentials-file: /data/data/com.termux/files/home/.cloudflared/<tunnel-id>.json

   ingress:
     - hostname: subdomain.yourdomain.com
       service: http://localhost:<the port you want>
     - service: http_status:404
   ```

9. Run the tunnel:
   ```bash
   cloudflared tunnel run <tunnel-name>
   ```

---

## Step 4: Run Server

You're all set! Swipe to the top left of your screen, start a new Termux session, and start whatever server you want.

---

## Contributing

Contributions are welcome! If you have suggestions or improvements, feel free to open an issue or submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).
