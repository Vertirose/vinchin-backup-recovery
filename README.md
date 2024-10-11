# Vinchin Backup and Recovery Software

Vinchin Backup & Restore adalah perangkat lunak untuk pencadangan dan pemulihan data di lingkungan virtualisasi, seperti VMware, Hyper-V, dan KVM. Dengan fitur pencadangan efisien, pemulihan fleksibel, dan antarmuka pengguna yang ramah, Vinchin memungkinkan manajer TI untuk melindungi data secara otomatis dan mengelola proses pencadangan dengan mudah. Perangkat lunak ini membantu perusahaan menjaga keberlangsungan bisnis dengan mengurangi risiko kehilangan data akibat bencana.

## Tech Stack
- [Vinchin](https://www.vinchin.com/) - Main Packages for backup and recovery software
- [Proxmox](https://www.proxmox.com/en/) - Virtual Environment
- [Rocky Linux](https://rockylinux.org/) - Vinchin guest operating system

## Installation Guide

Untuk tatacara pembuatan Vinchin Backup and Recovery Software dapat dilihat di docs yang sudah di buat, pada repositori ini, akan berfokus paada bagaimana agent installation pada sisi server dilaksanakan

## Agent Installation Guide
This is Agent installation guide for installing Vinchin Agent on Proxmox

### Prerequisite
install package pada link berikut
> ```
> [Agent Package File]
> 
> https://tinyurl.com/agent-package-file
> ```

### Package Installation
install package yang sudah di install sebelumnya
```
dpkg -i *agent-package-file*
```

### Troubleshooting
*jika terdapat sebuah error yakni kvm_backup_service dan kvm_virt_server maka dapat melakukan beberapa langkah berikut*\
\
check isi dari directory ***/opt/vinchin***
```
ls /opt/vinchin

...

output: ceph  kvm_backup_service  kvm_virt_server  lib  openstack_backup_watch_dog.sh
```
jika hasil sudah sesuai maka kita akan melakukan modifikasi untuk file kvm_backup_service.service dan kvm_virt_server.service dengan mengedit file beriku
```
vim / nano /etc/systemd/system/kvm_backup_service.service
```
&
```
vim / nano /etc/systemd/system/kvm_virt_server.service
```
***kvm_backup_service.service***
> ```
> 
> [Unit]
> Description=Vinchin KVM Backup Service
> After=network.target
> 
> [Service]
> Type=simple
> ExecStart=/opt/vinchin/kvm_backup_service
> Restart=on-failure
> 
> [Install]
> WantedBy=multi-user.target
> 
>```

***kvm_virt_server.service***
> ```
> 
> [Unit]
> Description=Vinchin KVM Virtualization Server Service
> After=network.target
> 
> [Service]
> Type=simple
> ExecStart=/opt/vinchin/kvm_virt_server
> Restart=on-failure
> 
> [Install]
> WantedBy=multi-user.target
>
> ```
reload systemd daemon dengan perintah berikut
```
systemctl daemon-reload
```
enable service yang sudah dibuat
```
systemctl enable kvm_backup_service.service
systemctl enable kvm_virt_server.service
```
start service
```
systemctl start kvm_backup_service.service
systemctl start kvm_virt_server.service
```
verifikasi hasil
```
systemctl status kvm_backup_service.service
systemctl status kvm_virt_server.service
```
untuk verifikasi bisa install ulang agent packagenya kemabali
```
dpkg -i *agent-package-file*
```
---
*jika terdapat error bisa melakukan perintah berikut*
```
dpkg -r *agent-package-file*
```
dan install ulang kembali agent package
```
dpkg -i *agent-package-file*
```
dan restart service yang sudah dibuat juga sebelumnya
```
systemctl restart kvm_backup_service.service
systemctl restart kvm_virt_server.service
```
