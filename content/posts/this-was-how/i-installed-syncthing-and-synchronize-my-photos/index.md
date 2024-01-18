+++
title = 'This Was How I Installed Syncthing and Synchronize My Photos'
date = 2024-01-16T18:36:42-05:00
draft = false
+++

I assumed TBD

The result is my phone's camera gallery synced with a special folder in my user home directory with syncthing running as a daemon.

Syncthing was already present as a package in my raspberry, so i installed as stated in the syncthind page 
```
# Add the release PGP keys:
sudo mkdir -p /etc/apt/keyrings
sudo curl -L -o /etc/apt/keyrings/syncthing-archive-keyring.gpg https://syncthing.net/release-key.gpg
# Add the "stable" channel to your APT sources:
echo "deb [signed-by=/etc/apt/keyrings/syncthing-archive-keyring.gpg] https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list
# Update and install syncthing:
sudo apt-get update
sudo apt-get install syncthing
```

Then i started it once with the command
```
syncthing
```

I stopped the process to edit the newly created file in `~/.local/state/syncthing/config.xml`. A grep search told me it was in line 110
```
cat config.xml | grep -n 127.0.0.1
```
This outputs the line (110 in my case) that i needed to edit to be able to explore the web interface of syncthing in another connected host.
```
<address>127.0.0.1:8384</address>
```
I changed the 127.0.0.1, that only allows connection from localhost, to 0.0.0.0, that allows connection from any connected host. 

At this point syncthing didn't start automatically, for that, i created a file for systemd in the location `/lib/systemd/system/syncthing.service` with the next content: 

```
[Unit]
Description=Syncthing - Open Source Continuous File Synchronization
Documentation=man:syncthing(1)
After=network.target

[Service]
User=pi
Group=syncthing
ExecStart=/usr/bin/syncthing -no-browser -no-restart -logflags=0
Restart=on-failure
RestartSec=5
SuccessExitStatus=3 4
RestartForceExitStatus=3 4

# Hardening
ProtectSystem=full
PrivateTmp=true
SystemCallArchitectures=native
MemoryDenyWriteExecute=true
NoNewPrivileges=true

[Install]
WantedBy=multi-user.target
``` 

The user i am using to install everything is also pi, so i didn't change it. I added a group there too, syncthing. So, any user in the syncthing group could access the folders owned by that group. That is important because I need to actively grant permission to the folders i want to sync to the syncthing group. 

For that matter, I created a folder inside the user home directory with syncthing permission, the foto folder `/home/emmanuel/fotos`
```
mkdir /home/emmanuel/fotos
sudo chown emmanuel:syncthing /home/emmanuel
sudo chown -R emmanuel:syncthing /home/emmanuel/fotos
sudo chmod -R 770 /home/emmanuel/fotos
```

Finally, i enabled syncthing with systemd and started it.

```
sudo systemctl enable syncthing
sudo systemctl start syncthing
```
With that done, i opened the syncthing web gui in `http://raspberrypi.local:8384`. Then i wen to Actions->Settings->GUI to set an user and a password.
I relogin with the new credential data, (i think). And then i went to Actions->Show ID, and stayed there to grab my phone. 

In my phone side, i installed the syncthing app througt the playstore https://play.google.com/store/apps/details?id=com.nutomic.syncthingandroid. Once installed, and opened, i went to the devices tab and tap the plus icon. In the row device Id I scanned the qr code in the syncthing web gui i let opened, that autocompleted the id, then i set the name `raspberry` to the device, leave everything else with their default values and save. A confirmation message appeared in the web gui, i think, which i accepted. 

In this moment i had 2 syncthing nodes connected, visible between each other, but nothing was being sync yet. 

For that in my phone i added the camera folder (it was already added) and then enable the raspberry. Another dialog appeared in the web gui, asking for confirmation. Upon agree, it asked about the location where i was going to use for the syncing, and yes, you guessed it, i set `/home/emmanuel/fotos` as that location. Once that configuration was saved, the syncronization began, and all my fotos were being shared in my raspberry.

Sources:
- https://pimylifeup.com/raspberry-pi-syncthing/
- https://apt.syncthing.net/
- https://github.com/syncthing/syncthing-android