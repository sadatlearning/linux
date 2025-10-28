##  complete your **PXE setup** with a **Kickstart file** for a **fully automated Rocky Linux installation**.

This will let you boot a client via PXE → auto-install Rocky → reboot — **no manual input required**.

---

## 🧩 Step 9: Create a Kickstart File

Let’s create `/var/www/html/ks/rocky9.cfg`

```bash
sudo mkdir -p /var/www/html/ks
sudo vi /var/www/html/ks/rocky9.cfg
```

Paste the following:

```bash
#version=RHEL9
# Rocky Linux 9 Automated Kickstart

# Install OS instead of upgrade
install

# Installation source (served by your PXE/HTTP server)
url --url="http://192.168.1.10/rocky9"

# Language, Keyboard, Timezone
lang en_US.UTF-8
keyboard us
timezone Asia/Kolkata --isUtc

# Network settings (DHCP by default)
network --bootproto=dhcp --device=eth0 --activate
hostname=rocky-auto-install.localdomain

# Root password (hashed)
rootpw --iscrypted $6$7nlkJ1kTzT$T6nM76MRpKlHz9T3vYPOzQoK60xI.Z/KZ3pBQc0kPKNn7lf7D1iRfPaT8ZWQdQmZ1sbnJpo8zV4ENw1lG08cX0
# (You can generate your own with: python3 -c "import crypt; print(crypt.crypt('yourpassword', crypt.mksalt(crypt.METHOD_SHA512)))")

# Skip GUI
text
skipx

# Disk partitioning (automated - wipes all data!)
zerombr
clearpart --all --initlabel
autopart --type=lvm

# SELinux, firewall, and services
selinux --enforcing
firewall --enabled --service=ssh

# Bootloader
bootloader --location=mbr --boot-drive=sda

# Do not prompt for confirmation
reboot

# Packages
%packages
@core
@standard
chrony
net-tools
curl
wget
vim
%end

# Post-installation script
%post
echo "PXE Auto Install Completed on $(date)" > /root/pxe_install.log
%end
```

---

## 🌐 Step 10: Make It Accessible

```bash
sudo chmod 644 /var/www/html/ks/rocky9.cfg
```

Then verify you can open it from a browser:

```
http://192.168.1.10/ks/rocky9.cfg
```

---

## 🧭 Step 11: Modify PXE Boot Menu to Use Kickstart

Edit `/var/lib/tftpboot/pxelinux.cfg/default`:

```bash
DEFAULT menu.c32
PROMPT 0
TIMEOUT 100
ONTIMEOUT rocky

MENU TITLE PXE Boot Menu

LABEL rocky
  MENU LABEL Auto Install Rocky Linux 9
  KERNEL http://192.168.1.10/rocky9/images/pxeboot/vmlinuz
  APPEND initrd=http://192.168.1.10/rocky9/images/pxeboot/initrd.img inst.repo=http://192.168.1.10/rocky9 inst.ks=http://192.168.1.10/ks/rocky9.cfg console=tty0 console=ttyS0,115200n8
```

> The `inst.ks=` parameter points to your Kickstart file URL.

---

## 🧠 Step 12: Test the Full Workflow

1. Boot a VM or physical system via PXE.
2. The system should automatically:

   * Get an IP from DHCP
   * Fetch PXE boot menu
   * Load kernel/initrd from HTTP
   * Pull the Kickstart file
   * Install Rocky Linux automatically
   * Reboot to the fresh OS

---

## 🧰 Optional Additions

You can extend Kickstart for:

* Automatic **user creation**
* **SSH key injection**
* Custom **package sets**
* Post-install **ansible/puppet bootstrap**

Example snippet:

```bash
user --name=devops --password=$6$abc123xyz$yoursecurehash --iscrypted --groups=wheel
sshkey --username=devops "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC..."
```

---

## ✅ Verification Checklist (Final)

| Component    | Check                               |
| ------------ | ----------------------------------- |
| DHCP         | Clients get IP                      |
| TFTP         | Serves `pxelinux.0` / `grubx64.efi` |
| HTTP         | Serves `rocky9/` and `ks/`          |
| PXE Menu     | Appears on client boot              |
| Auto Install | Works end-to-end                    |
| Reboot       | Boots clean OS                      |

---

Would you like me to show you a **version of the Kickstart file** that sets up a **pre-configured DevOps/Server environment** (with Docker, Git, and SSH ready after PXE install)?
That’s perfect if you want PXE to auto-deploy lab or CI/CD nodes.
