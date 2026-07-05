🚀 VPS Setup Guide for Telegram Bot
Here's a README.md formatted guide ready for your Telegram bot repository:

Telegram File Converter Bot - VPS Deployment Guide
Complete guide to deploy your Telegram bot on a VPS with Python, Nginx, and HTTPS.

📋 Prerequisites
VPS with Ubuntu 20.04/22.04

Domain name pointed to your VPS IP

Telegram Bot Token from @BotFather

API ID & API Hash from my.telegram.org

🚀 Step-by-Step Installation
1. Update Your Server
bash
sudo apt update && sudo apt upgrade -y
2. Install Dependencies
bash
sudo apt install -y python3 python3-venv python3-pip nginx certbot python3-certbot-nginx git
3. Clone Your Bot Project
bash
cd ~
git clone https://github.com/your-repo/MtProto_Bot.git
cd MtProto_Bot
Or if uploaded via FileZilla:

bash
cd ~/MtProto_Bot
4. Create Python Virtual Environment
bash
python3 -m venv venv
source venv/bin/activate
5. Install Required Python Packages
bash
pip install --upgrade pip
pip install pyrogram tgcrypto fastapi uvicorn aiohttp aiofiles python-dotenv
6. Add Your .env File
Create the environment configuration:

bash
nano ~/MtProto_Bot/.env
Paste your credentials:

env
API_ID=23323985
API_HASH=d24809282e7c046a98a04ca3c66659e7
BOT_TOKEN=YOUR_BOT_TOKEN
SESSION_NAME=my_bot
BASE_URL=https://yourdomain.com
⚠️ Important: Replace YOUR_BOT_TOKEN and yourdomain.com with your actual values.

Save and exit: Ctrl+X → Y → Enter

🧪 Test Run the Bot
7. Test Manually
bash
cd ~/MtProto_Bot
source venv/bin/activate
uvicorn bot:app --host 0.0.0.0 --port 8000
✅ If bot responds → it's working fine. Press Ctrl+C to stop.

🌐 Setup Nginx & HTTPS
8. Configure Nginx Reverse Proxy
Create the configuration file:

bash
sudo nano /etc/nginx/sites-available/bot
Paste this configuration:

nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
Enable the site:

bash
sudo ln -s /etc/nginx/sites-available/bot /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
9. Enable HTTPS with Certbot
bash
sudo certbot --nginx -d yourdomain.com
📌 Choose option 2 → "Redirect HTTP to HTTPS"

⚙️ Run as a Service
10. Create Systemd Service
Create the service file:

bash
sudo nano /etc/systemd/system/telegram-bot.service
Paste this:

ini
[Unit]
Description=Telegram File Converter Bot
After=network.target

[Service]
User=root
WorkingDirectory=/root/MtProto_Bot
ExecStart=/root/MtProto_Bot/venv/bin/python3 -m uvicorn bot:app --host 0.0.0.0 --port 8000
Restart=always
Environment="PYTHONUNBUFFERED=1"

[Install]
WantedBy=multi-user.target
11. Enable & Start the Bot
bash
sudo systemctl daemon-reload
sudo systemctl enable telegram-bot
sudo systemctl start telegram-bot
Check status:

bash
sudo systemctl status telegram-bot -n 20 --no-pager
View live logs:

bash
sudo journalctl -u telegram-bot -f
🔄 Auto Reload on Code Changes (Optional)
12. Create Path Unit
bash
sudo nano /etc/systemd/system/telegram-bot.path
Paste:

ini
[Unit]
Description=Restart telegram-bot on code change

[Path]
PathModified=/root/MtProto_Bot/*.py
Unit=telegram-bot.service

[Install]
WantedBy=multi-user.target
Enable and start:

bash
sudo systemctl enable telegram-bot.path
sudo systemctl start telegram-bot.path
🎯 Quick Commands Reference
Action	Command
Start bot	sudo systemctl start telegram-bot
Stop bot	sudo systemctl stop telegram-bot
Restart bot	sudo systemctl restart telegram-bot
Check status	sudo systemctl status telegram-bot
View logs	sudo journalctl -u telegram-bot -f
Reload Nginx	sudo systemctl reload nginx
Test Nginx	sudo nginx -t
Renew SSL	sudo certbot renew
🔧 Troubleshooting
Common Errors & Fixes
Error	Solution
ModuleNotFoundError	Run pip install -r requirements.txt
Port 8000 in use	sudo lsof -i :8000 then kill -9 PID
Nginx 502 Bad Gateway	Check if bot is running: sudo systemctl status telegram-bot
Certbot SSL error	Ensure domain DNS points to your server IP
Bot not responding	Check logs: sudo journalctl -u telegram-bot -f
Permission denied	Run chmod +x ~/MtProto_Bot
Firewall (UFW) Commands
bash
# Allow necessary ports
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable

# Check status
sudo ufw status
📝 Quick Setup Script
Save this as setup.sh and run bash setup.sh:

bash
#!/bin/bash

# Update system
sudo apt update && sudo apt upgrade -y

# Install dependencies
sudo apt install -y python3 python3-venv python3-pip nginx certbot python3-certbot-nginx git

# Clone repo
cd ~
git clone https://github.com/your-repo/MtProto_Bot.git
cd MtProto_Bot

# Setup Python
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install --upgrade pip
pip install pyrogram tgcrypto fastapi uvicorn aiohttp aiofiles python-dotenv

echo "✅ Setup complete!"
echo "📝 Now add your .env file and configure Nginx"
✅ Deployment Checklist
Server updated

Dependencies installed

Repository cloned

Virtual environment created

Python packages installed

.env file configured

Bot tested manually

Nginx configured

HTTPS enabled with Certbot

Systemd service created

Bot enabled and running

📞 Support
For issues or questions:

Telegram: @your_username

GitHub Issues: Create Issue

