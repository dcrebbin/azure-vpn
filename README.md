# Azure Proxy/VPN

How to setup a Shadowsocks proxy/VPN with a $5 per month VM hosted on Azure
(similar process for other Linux VMs too)

## Requirements:
- Azure VM with a public IP
- (optional) custom domain

### Azure VM

1. Head to [portal.azure.com](https://portal.azure.com/) and create an account
   
    a. Once Created -> Virtual Machines -> Create

<img width="811" alt="Screenshot 2025-05-01 at 3 41 51 am" src="https://github.com/user-attachments/assets/6cb39811-df2d-46ed-85b8-651949d43f3e" />

2. Select your desired VPN Region (Very important to make sure you choose the right one as switching over isn't simple
  - East Asia: Hong Kong
  - South East Asia: Singapore (recommended)

3. Ensure some version of Linux Ubuntu Server is choosen

4. Pick `Standard_B1ls - 1 vcpu, 0.5 GiB memory (US$3.94/month)` via 'Size'

5. Setup your SSH Public Key/Password (if not using SSH public key) and your username

6. Allow for SSH & HTTPS inbound ports

7. (Disks) Next > Ensure that 30GB is the selected disk space > select Standard SSD

8. (Networking) Create a new public IP (Make note of it)

9. Skip through everything and create your VM

### DNS custom Host

1. Head to Cloudflare (or your DNS provider)

2. Go to DNS Records

3. Type "A" | Name "your-subdomain" (or empty) | Content "your-azure-public-ip" | Proxy Status "DNS Only"

4. Add Record

From now on, only your custom host "example.com" where asked

### VM Setup 

Go to [`./commands.md`](https://github.com/dcrebbin/azure-vpn/blob/main/commands.md) for more information

### Mac Client [(ClashX)](https://en.clashx.org) (Free)

1. Go to https://en.clashx.org -> Free Download

2. Once running -> Select ClashX from your toolbar -> Config -> Open config folder

3. Replace the default `config.yaml` with the [`./clash.yaml`](https://github.com/dcrebbin/azure-vpn/blob/main/clash.yaml) in this repo

4. (ClashX Dropdown) -> Config -> Reload Config

5. (ClashX Dropdown) -> GLOBAL -> Benchmark

If "clash" is green or yellow your VPN is now woring

#### ClashX Debugging

1. (ClashX Dropdown) -> Setting -> Debug -> Open Log Folder -> (Open the latest log file in a text editor)

### iOS Client [(Shadowrocket)](https://apps.apple.com/us/app/shadowrocket/id932747118) (Paid: $2.99 USD)

Notes on other clients (Free)
- V2Box - V2ray Client: Not working
- Potatso: Not working
- v2RayTun: Not working
- Spectre VPN: Not working

Shadowrocket appears to be the only iOS app that has a big enough feature set that we can correctly configure your Shadowsocks VPN.
However let me know if there are any working free alternatives.

1. Add Server
   - Address: IP/Custom Domain
   - Port: 443
   - Password: Configured Password
   - Method: aes-256-cfb
   - Obfuscation: none
   - Plugin: v2ray-plugin
   - One Time Auth: Off
   - TCP Fast Open: Off
   - UDP Relay: Off
   - Remarks: *some-name*

3. v2ray-plugin details:
   - Address: None
   - Port: None
   - Mode: websocket
   - TLS: True
   - Allow Insecure: True (or false is you have setup Lets Encrypt on your server instead of a self-siged one)
   - SNI: None
   - TCP Fast Open: Off
   - Muxing: On
   - Path: /

3. Save Server

4. Settings -> Proxy Settings:
   - Compatability Mode: On
   - Proxy Type: None
   - Proxy Port: 7890
   - Proxy Address: 127.0.0.1
  
5. (Optional) Settings -> Tunnel (Up to you depending on your use case)
   - Enforce Routes: On
   - Include All Networks: True
   - Include Local Networks: True
   - Include APNs: False
   - Include Cellular Services: True
  
6. Settings -> Test Method -> Connect

7. Tap *Connectivity Test*: If Green or Yellow with XXXms then you are good to go!

#### iOS Shadowrocket Debuging

1. Settings -> Diagnostics -> Enable Logging -> Visit Address (https://...../7890/api/log) via a Web Browser
