---
title: "Fileserver LCX in Proxmox"
date: 2023-07-21T11:14:51+02:00
draft: false  
tags: 
    - Proxmox
    - How-To
    - Debian
    - Linux
    - LCX
---
# Einleitung
Nachfolgend ist die Installation eines Fileserver mit SMB Freigabe innerhalb einer LCX auf Debian Basis in Proxmox beschrieben.

# Voraussetzungen
1. Promox Server mit eingerichtetem Daten-Pool (z.B. ZFS)
2. Ein Debian LXC Template

## Vorbereitung eines ZFS Datasets
1. In Proxmox Shell den folgenden Befehl eingeben zur Erstellung eines ZFS Datasets:
```
zfs create <pool name>/<dataset name>

```
Die Kontrolle der richtigen Anlegung geht mit dem folgenden Befehl:
```
zfs list

```
# Installation Container
1. Unpriviligierten Debian LXC installieren und Nesting aktivieren. Die Standardwerte mit  512MiB RAM und 8GiB sind ausreichend. Den Container nicht automatisch starten lassen.
2. ID des Containers vermerken
3. In der Shell des Proxmox Hosts den folgenden Befehl mit entsprechender ID eingeben:
```
nano /etc/pve/lxc/<ID>.conf

```
Hier muss der Eintrag gesetzt werden:
```
mp0: /<zfs pool name>/<dataset name>,mp=/mnt/<interner Mountpoint>,backup=1

```
4. Nun kann der Container gestartet werden.

# Installation Fileserver
1. Installation Cockpit
```
apt update
apt upgrade
apt install cockpit --no-install-recommends

```
2. Installation der zusätzlichen Addons für Cockpit
```
wget https://github.com/45Drives/cockpit-file-sharing/releases/download/v3.3.4/cockpit-file-sharing_3.3.4-1focal_all.deb
wget https://github.com/45Drives/cockpit-navigator/releases/download/v0.5.10/cockpit-navigator_0.5.10-1focal_all.deb
wget https://github.com/45Drives/cockpit-identities/releases/download/v0.1.12/cockpit-identities_0.1.12-1focal_all.deb
apt install ./*.deb
```
#Optional root User erlauben für Anmeldung
```

nano /etc/cockpit/disallowed-users
//hier den Root user entfernen bzw. auskommentieren
```
3. Anmeldung an Cockpit
Die Anmeldung erfolgt über die IP des Containers mit dem Port "9090"
4. Erstellung User
Unter Identities können neue Benutzer und Gruppen erstellt werden. Wichtig: Für SMB Shares werden für die Benutzer zwei Passwörter benötigt. Eines für die Shell und eines für Samba.
![Cockpit User creation](/images/cockpit-user-creation.png)
5. Erstellung Share
Nun kann mit dem eingebundenen Mountpoint eine Freigabe für den erstellten Benutzer erstellt werden.
