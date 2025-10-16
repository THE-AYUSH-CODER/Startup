# Code Run on Startup
/home/pi/Desktop/Object_Detection_Files/object-ident.py

Let’s make that run automatically at startup reliably, even if the camera or display takes a few seconds to initialize.


---

✅ Step-by-step setup using systemd (most reliable)

1️⃣ Create a new service file

Open terminal and run:

sudo nano /etc/systemd/system/objectdetection.service


---

2️⃣ Paste this inside:

[Unit]
Description=Object Detection Autostart
After=network.target multi-user.target
StartLimitIntervalSec=0

[Service]
ExecStartPre=/bin/sleep 10
ExecStart=/usr/bin/python3 /home/pi/Desktop/Object_Detection_Files/object-ident.py
WorkingDirectory=/home/pi/Desktop/Object_Detection_Files
StandardOutput=inherit
StandardError=inherit
Restart=always
RestartSec=5
User=pi
Environment=DISPLAY=:0
Environment=XAUTHORITY=/home/pi/.Xauthority

[Install]
WantedBy=graphical.target

Explanation:

ExecStartPre=/bin/sleep 10 → waits 10 seconds before running (lets camera + GUI load).

DISPLAY=:0 and XAUTHORITY → required if using cv2.imshow() or text-to-speech.

Restart=always → auto restarts if it crashes.

WantedBy=graphical.target → ensures it starts after GUI loads.



---

3️⃣ Enable and start the service

sudo systemctl daemon-reload
sudo systemctl enable objectdetection.service
sudo systemctl start objectdetection.service


---

4️⃣ Check its status

sudo systemctl status objectdetection.service

If you see:

Active: active (running)

✅ It’s working fine!

If it fails, view detailed logs:

journalctl -u objectdetection.service -b


---

5️⃣ Reboot to test

sudo reboot

After reboot, your object detection program should automatically start — no manual command needed.


---

Would you like me to modify it so it waits for camera detection before starting (instead of a fixed 10 seconds)?
I can add a small line to check /dev/video0 before running your script — more reliable for Pi Camera or USB webcams.

