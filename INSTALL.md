# Installing Family Chore Management App on Raspberry Pi 4B

## Prerequisites

1. Raspberry Pi 4B with Raspberry Pi OS installed
2. Internet connection
3. Terminal access to Raspberry Pi (either directly or via SSH)

## Installation Steps

### 1. Update System

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Install Node.js and npm

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

Verify installation:
```bash
node --version
npm --version
```

### 3. Install Git

```bash
sudo apt install git -y
```

### 4. Clone the Repository

```bash
git clone <your-repo-url> /home/pi/family-chores
cd /home/pi/family-chores
```

### 5. Install Dependencies

```bash
npm install
```

### 6. Build the Application

```bash
npm run build
```

### 7. Create a Service File

Create a new service file:

```bash
sudo nano /etc/systemd/system/family-chores.service
```

Add the following content:

```ini
[Unit]
Description=Family Chore Management App
After=network.target

[Service]
Type=simple
User=pi
WorkingDirectory=/home/pi/family-chores
ExecStart=/usr/bin/npm start
Restart=always
Environment=NODE_ENV=production
Environment=PORT=3000

[Install]
WantedBy=multi-user.target
```

### 8. Start and Enable the Service

```bash
sudo systemctl start family-chores
sudo systemctl enable family-chores
```

### 9. Configure Auto-start Browser in Kiosk Mode (for Tablet Display)

Create a startup script:

```bash
nano /home/pi/.config/autostart/kiosk.desktop
```

Add the following content:

```ini
[Desktop Entry]
Type=Application
Name=Kiosk
Exec=chromium-browser --kiosk --disable-restore-session-state http://localhost:3000/tablet
```

## Accessing the App

- Tablet Interface: http://localhost:3000/tablet
- Admin Interface: http://localhost:3000/admin

## Data Storage

The app stores data in JSON files in the `/home/pi/family-chores/data` directory. Ensure this directory has proper permissions:

```bash
mkdir -p /home/pi/family-chores/data
chown -R pi:pi /home/pi/family-chores/data
```

## Troubleshooting

1. Check service status:
```bash
sudo systemctl status family-chores
```

2. View logs:
```bash
journalctl -u family-chores
```

3. If the service fails to start:
- Check Node.js installation
- Verify file permissions
- Ensure all dependencies are installed
- Check the data directory exists and is writable

## Security Considerations

1. Change default Pi user password:
```bash
passwd
```

2. Enable UFW firewall:
```bash
sudo apt install ufw
sudo ufw allow ssh
sudo ufw allow 3000
sudo ufw enable
```

3. Consider setting up HTTPS if accessing from outside the local network.

## Maintenance

1. Update the application:
```bash
cd /home/pi/family-chores
git pull
npm install
npm run build
sudo systemctl restart family-chores
```

2. Backup data:
```bash
cp -r /home/pi/family-chores/data /backup/location
```

## Additional Notes

- The app runs on port 3000 by default. You can change this in the service file if needed.
- For the tablet display, consider setting up the Pi to auto-login and start in kiosk mode.
- Regular backups of the data directory are recommended.
- Monitor system resources (CPU, memory, disk space) periodically.
