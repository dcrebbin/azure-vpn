## SSH into the Server

Notes:

- user is what you defined in your Azure VM
- host is your Azure VM Public IP or Domain that is linked to that IP

```
ssh -i ~/.ssh/public_key.pem user@host
```

## Setting up Memory Swap

```bash
sudo fallocate -l 2G /swapfile
# If fallocate fails (on some file systems), use dd instead:
# sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
# Set correct permissions
sudo chmod 600 /swapfile
# Set up the swap space
sudo mkswap /swapfile
# Enable the swap file
sudo swapon /swapfile
# Make it permanent
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
# Check swap
free -h
```

## Installing the required packages and plugins

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install shadowsocks-libev -y
cd /usr/local/bin
sudo wget https://github.com/shadowsocks/v2ray-plugin/releases/download/v1.3.2/v2ray-plugin-linux-amd64-v1.3.2.tar.gz
sudo tar -xvzf v2ray-plugin-linux-amd64-v1.3.2.tar.gz
sudo mv v2ray-plugin_linux_amd64 v2ray-plugin
sudo chmod +x v2ray-plugin
```

## Creating the Shadowsocks Config

```bash
sudo vi /etc/shadowsocks-libev/config.json
```

note: example.com can be switched out for your IPV4 from Azure

1. delete all the original content

   - command: `:%d`

2. copy the below

```JSON
{
  "server": "0.0.0.0",
  "server_port": 443,
  "password": "your-password",
  "timeout": 300,
  "method": "aes-256-gcm",
  "fast_open": false,
  "plugin": "v2ray-plugin",
  "plugin_opts": "server;tls;host=example.com;mode=websocket;cert=/etc/v2ray-selfsigned.crt;key=/etc/v2ray-selfsigned.key"
}
```

3. cmd/ctrl + v
4. :wq!

## Allow port access

```bash
sudo ufw allow 8388/tcp
sudo ufw allow 8388/udp
sudo ufw allow 443/tcp
sudo ufw allow 22/tcp
sudo ufw enable
```

### Configure Shadowsocks Environment

```bash
sudo mkdir -p /etc/systemd/system/shadowsocks-libev-server@config.service.d
sudo vi /etc/systemd/system/shadowsocks-libev-server@config.service.d/override.conf
```

```yaml
[Service]
Environment=HOME=/root
```

## Generate the self-signed cert

```bash
sudo openssl req -x509 -newkey rsa:2048 -keyout /etc/v2ray-selfsigned.key -out /etc/v2ray-selfsigned.crt -days 365 -nodes -subj "/CN=dev.langpal.com.hk"
# modify the permissions
sudo chmod 644 /etc/v2ray-selfsigned.crt
sudo chmod 644 /etc/v2ray-selfsigned.key
```

## Reload everything

```bash
sudo systemctl daemon-reload
sudo systemctl enable shadowsocks-libev-server@config
sudo systemctl restart shadowsocks-libev-server@config
```

## Debug if it worked

```bash
sudo journalctl -u shadowsocks-libev-server@config -n 300 --no-pager
```

## Successful Logs:

```bash
vpn systemd[1]: Started shadowsocks-libev-server@config.service - Shadowsocks-Libev Custom Server Service for config.
vpn ss-server[8873]:  2025-04-30 19:26:09 INFO: plugin "v2ray-plugin" enabled
vpn ss-server[8873]:  2025-04-30 19:26:09 INFO: initializing ciphers... aes-256-gcm
vpn ss-server[8873]:  2025-04-30 19:26:09 INFO: tcp server listening at ...
vpn ss-server[8874]: 2025/04/30 19:26:09 V2Ray 4.38.3 (V2Fly, a community-driven edition of V2Ray.) Custom (go1.16.15 linux/amd64)
```
