Module 1: Compute
## Goal
Create, connect to, and troubleshoot Linux and Windows virtual machines in Azure using the portal and Azure CLI (Bash), including disks and snapshots.
## What I built
- Ubuntu VM via the portal (SSH key, auto-shutdown)
- Ubuntu VM via Azure CLI with nginx and an open port 80
- A Windows Server VM reached over RDP
- A data disk (attached, formatted, mounted) and an OS disk snapshot
- VM lifecycle operations: stop, deallocate, start, resize, run-command
## Steps
### Portal VM
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
![screenshots](screenshots/1.png)
### CLI VM
Script: [create-linux-vm.sh](scripts/create-linux-vm.sh)
![CLI create](screenshots/04-cli-vm-create.png)
![VM list](screenshots/05-vm-list-status.png)
![Inspection](screenshots/06-linux-inspection.png)
![nginx](screenshots/07-nginx-browser.png)
### Windows VM
![RDP](screenshots/08-windows-rdp.png)
### Disks and snapshot
![Data disk](screenshots/09-data-disk-mounted.png)
![Snapshot](screenshots/10-snapshot.png)
![Resize and run-command](screenshots/11-vm-resize-and-run-command.png)
## Troubleshooting log
| # | What I did | Symptom | Cause | How I found it | Fix |
|---|---|---|---|---|---|
| 1 | Removed the SSH NSG rule | [real error] | Port 22 blocked by NSG | rule list, /dev/tcp port test | `az vm open-port --port 22` |
| 2 | SSH as wrong user | [real error] | Wrong username or key | Compared user and key | Used `azureuser`, or reset key with `az vm user update` |
| 3 | Key permissions 644 | [real error] | Private key readable by others | Read the warning | `chmod 400` |
| 4 | Deallocated the VM | [real error] | VM was not running | `az vm list -d` showed deallocated | `az vm start` |
| 5 | Rebooted VM | [real output] | Mount not in /etc/fstab | `df -h`, `lsblk` | Added a UUID entry to fstab |
| 6 | Filled the data disk | [real error] | Disk 100% full | `df -h`, `du -sh` | Deleted the file or grew the disk |
| 7 | Used an invalid size | [real error] | Size does not exist or is unavailable | `az vm list-skus` | Chose a valid size |
Screenshots: ![1](screenshots/12-err1-ssh-timeout.png) ![1 fixed](screenshots/13-err1-fixed.png) ![2](screenshots/14-err2-permission-denied.png) ![3](screenshots/15-err3-unprotected-key.png) ![4](screenshots/16-err4-vm-deallocated.png) ![5](screenshots/17-err5-disk-not-mounted.png) ![6](screenshots/18-err6-disk-full.png) ![7](screenshots/19-err7-invalid-size.png)
## What I learned
- Stopped vs deallocated: only deallocated stops compute billing.
- A timeout usually means the network blocks traffic. A refusal or permission error means the machine answered.
- Disks and snapshots still cost money when the VM is off.
- Mounts need an fstab entry to survive a reboot.
## Cleanup
Deleted `rg-m1-compute`. Cost after cleanup: [number or screenshot].
![Cleanup](screenshots/20-cleanup-and-cost.png)

