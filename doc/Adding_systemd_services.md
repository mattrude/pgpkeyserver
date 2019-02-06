---
layout: default
title: Adding systemd services on a SKS-Keyserver
permalink: /guides/systemd-services/
---

# Adding systemd services for SKS-Keyserver on Ubuntu 18.04

This page will walk you threw installing the sks-keyserver systemd services on a Ubuntu 18.04 LTS, thou it will most likely work with any systemd server.

<div class="alert alert-warning" role="alert">
<b>Note:</b> This page assumes your sks daemon is located at <code>/usr/local/bin/sks</code> and your key directory is <code>/var/lib/sks</code>, if yours is in a diffrent location, you will need to update the <code>sks-db.service</code> and <code>sks-recon.service</code> files.
</div>

The systemd services provided on this page will auto start the sks daemon when the server is booted up, and will restart the daemon if it dies for some reason.

## User Setup

First confirm you have the `debian-sks` user and group added to your system.  If you do not, you may add them with the following command:

```bash
adduser --system --group --home /var/lib/sks --disabled-login debian-sks
```

## Systemd Services Install

Now, download the needed service files and add them to your `/etc/systemd/system/` directory.  Note, these config assume the sks daemon lives at `/usr/local/bin/sks` if yours is in a diffrent spot, you will need to update both files to point to your install locaction.

**/etc/systemd/system/sks-db.service**
```bash
[Unit]
Description=SKS-Keyserver DB Instance
After=network.target

[Service]
Type=simple
User=debian-sks
Group=debian-sks
WorkingDirectory=/var/lib/sks
ExecStart=/usr/local/bin/sks db
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

**/etc/systemd/system/sks-recon.service**
```bash
[Unit]
Description=SKS-Keyserver Recon Instance
Before=network.target
Wants=network.target

[Service]
Type=simple
User=debian-sks
Group=debian-sks
WorkingDirectory=/var/lib/sks
ExecStart=/usr/local/bin/sks recon
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

## Systemd Services Setup

After you have the services installed, you may enable the services.

```bash
systemctl daemon-reload
systemctl enable sks-db.service
systemctl enable sks-recon.service
```

## Managing the SKS-Keyserver via Systemd

### Starting the SKS-Keyserver

```bash
systemctl start sks-db sks-recon
```

### Restarting the SKS-Keyserver

```bash
systemctl restart sks-db sks-recon
```

### Stopping the SKS-Keyserver

```bash
systemctl stop sks-db sks-recon
```

### Checking the status of the SKS-Keyserver

```bash
systemctl status sks-db sks-recon
```

Sample output when the service is running:
```
● sks-db.service - SKS-Keyserver DB Instance
   Loaded: loaded (/etc/systemd/system/sks-db.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-02-05 20:55:22 UTC; 1h 45min ago
 Main PID: 8445 (sks)
    Tasks: 1
   Memory: 1016.3M
      CPU: 15min 5.827s
   CGroup: /system.slice/sks-db.service
           └─8445 /usr/local/bin/sks db

● sks-recon.service - SKS-Keyserver Recon Instance
   Loaded: loaded (/etc/systemd/system/sks-recon.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-02-05 20:55:21 UTC; 1h 45min ago
 Main PID: 8442 (sks)
    Tasks: 1
   Memory: 131.7M
      CPU: 19.833s
   CGroup: /system.slice/sks-recon.service
           └─8442 /usr/local/bin/sks recon
```
