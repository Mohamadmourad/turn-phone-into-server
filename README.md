# Turn Your Android Phone into a Server Using Termux

This guide explains how to turn your Android phone into a functional server using Termux. Termux is a powerful terminal emulator that allows you to run a Linux-like environment on your phone. By the end of this guide, you’ll have a fully functional web server hosted from your device.

---

## Prerequisites
1. An Android phone.
2. A domain name.
3. Termux installed ([download from F-Droid](https://f-droid.org/)).
4. A stable internet connection.

---

### Step 1: Install Termux
- Download Termux from [F-Droid](https://f-droid.org/).
- Open Termux and update the packages:
  ```bash
  pkg update && pkg upgrade

---

### Step 2: Install Required Tools 
1. **Install `proot-distro` in Termux:**
  ```bash
   pkg install proot-distro 
   proot-distro install debian
   proot-distro login debian
  ```
2. **Install `proot-distro` in Termux:**

   ```bash
   apt install wget
   wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm
     or
   wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64
   ```

 Note : it depends on your system you can check using this command (uname -m)


  ### now write the following commands:
  
    mv cloudflared-linux-arm cloudflared
    chmod +x cloudflared
    mv cloudflared $PREFIX/bin/
  
  
  #### to check if its working type the following command:
   ```bash
   cloudflared version
   ```
   

3. **Install Git, Node.js, and npm:** (or any tool you need for your server)
  ```bash
  apt install git nodejs
  ```

  Note: we installed debian to bypass android restrictions.

---

### Step 3: Cloudflare tunnel setup

1. Go to your Cloudflare dashboard in the **Account Home** tab click on add a domain
2. Go to your domain name provider delete all nameservers and add cloudflare one

 *daisy.ns.cloudflare.com*

 and

 *jay.ns.cloudflare.com*

this process might take some time to verify but once its completed they will send you an email

3. when its completed go to your cloudflare account again and go to the **Access** tap and click the launch zero trust and complete the setup

4. on you phone (not in termux in browser) go to this link and login with your clouflare account you will get the cert.pem file ([https://dash.cloudflare.com/argotunnel](https://dash.cloudflare.com/argotunnel))

5. move the cert.pem to termux 
- add the cert.pem to your download folder
- run the following commands in termux:

```bash
termux-setup-storage
cp /storage/emulated/0/Download/cert.pem ~/.cloudflared/
```

6. now create the tunnel using:
```bash
cloudflared tunnel create <tunnel-name>
```
7. create config.yml file:
```bash
nano ~/.cloudflared/config.yml
```
8. add the following content:
```bash
tunnel: <tunnel-id>
credentials-file: /data/data/com.termux/files/home/.cloudflared/my-tunnel.json

ingress:
  - hostname: subdomain.yourdomain.com
    service: http://localhost:<the port you want>
  - service: http_status:404
```
9. for the final step run the tunnel:
```bash
cloudflared tunnel run <tunnel-name>
```

---

### Step 4: Run server

you're all set swipe to the top left of your screen and start a new session and start whatever server you want
