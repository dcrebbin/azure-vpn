# Azure VPN

How to setup a Shadowsock VPN with a $5per month VM hosted on Azure
(similar process for other Linux VMs too)

## Requirements:
- Azure VM with a public IP
- (optional) custom domain

### Azure VM

1. Head to portal.azure.com -> Virtual Machines -> Create

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

Go to `./commands.md` for more information 

### Client (MacOS Instructions Only)

1. Go to https://en.clashx.org -> Free Download

2. Once running -> Select ClashX from your toolbar -> Config -> Open config folder

3. Replace the default `config.yaml` with the `./clash.yaml` in this repo

4. (ClashX Dropdown) -> Config -> Reload Config

5. (ClashX Dropdown) -> GLOBAL -> Benchmark

If "cash" is green or yellow your VPN is now woring

#### Debuging

1. (ClashX Dropdown) -> Setting -> Debug -> Open Log Folder -> (Open the latest log file in a text editor)
