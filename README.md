# Linux-Day4

🧪 Linux Hands-On Lab & Homework

This project covers:

Disk Monitoring Script

Capturing HTTP Traffic

Bash Argument Script

Process & Log Analysis

Cron Job Automation

systemd Service & Timer Setup

📀 Lab 1 — Disk Monitoring Script
🎯 Objective

Monitor disk usage and generate alert if usage exceeds threshold.

📝 Create Script
nano disk_check.sh

Add:

#!/bin/bash
set -eu

threshold=80

usage=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')

if [ "$usage" -gt "$threshold" ]; then
    echo "$(date): Disk usage high - $usage%" >> /tmp/disk_alert.log
fi
🔧 Make Script Executable
chmod +x disk_check.sh
▶️ Run Script
./disk_check.sh
📄 Check Log File
cat /tmp/disk_alert.log
🌐 Lab 2 — Capture HTTP Traffic
🎯 Objective

Observe TCP handshake and HTTP packets.

🖥 Terminal 1 — Start Packet Capture
sudo tcpdump -i eth0 port 80
🌍 Terminal 2 — Generate HTTP Traffic
curl http://example.com
👀 Observe

You will see:

SYN

SYN-ACK

ACK

HTTP GET

HTTP Response

This shows TCP 3-way handshake and HTTP communication.

🏠 Homework / Deliverables
1️⃣ countargs.sh
📝 Script
vim countargs.sh

Add:

#!/bin/bash

for arg in "$@"
do
    echo "$arg"
done

echo "Total arguments: $#"
🔧 Make Executable
chmod +x countargs.sh
▶️ Run Example
./countargs.sh Linux AWS Docker
2️⃣ Which Process Uses Most Memory?
ps aux --sort=-%mem | head -11

👉 First process after header uses most memory.

3️⃣ Largest Directory in /var/log
du -sh /var/log/* | sort -hr | head -1
4️⃣ Last 20 SSH Logs
journalctl -u ssh -n 20
⚙️ Create systemd Service & Timer for Disk Script
Step 1: Create Service File
sudo nano /etc/systemd/system/disk_check.service

Add:

[Unit]
Description=Disk Check Service

[Service]
ExecStart=/home/user/disk_check.sh
Step 2: Create Timer File
sudo nano /etc/systemd/system/disk_check.timer

Add:

[Unit]
Description=Run Disk Check Every 5 Minutes

[Timer]
OnBootSec=1min
OnUnitActiveSec=5min
Unit=disk_check.service

[Install]
WantedBy=timers.target
Step 3: Reload systemd
sudo systemctl daemon-reload
Step 4: Enable & Start Timer
sudo systemctl enable disk_check.timer
sudo systemctl start disk_check.timer
Step 5: Verify
systemctl list-timers
journalctl -u disk_check.service
⏰ Schedule Script via Cron
Edit Crontab
crontab -e
Add Entry
*/5 * * * * /home/user/disk_check.sh
