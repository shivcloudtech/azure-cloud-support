Module 1: Compute
## Goal
Create, connect to, and troubleshoot Linux and Windows virtual machines in Azure using the portal and Azure CLI (Bash), including disks and snapshots.
## What I built
- Ubuntu VM via the portal (password authentication, auto-shutdown)
- Ubuntu VM via Azure CLI with nginx and an open port 80
- A Windows Server VM reached over RDP
- A data disk (attached, formatted, mounted) and an OS disk snapshot
- VM lifecycle operations: stop, deallocate, start, resize, run-command
## Steps
### Portal VM

![screenshots](screenshots/1.png)
![screenshots](screenshots/2.png)
![screenshots](screenshots/3.png)
![screenshots](screenshots/4.png)
![screenshots](screenshots/5.png)
![screenshots](screenshots/6.png)
![screenshots](screenshots/7.png)
![screenshots](screenshots/8.png)
![screenshots](screenshots/9.png)
![screenshots](screenshots/10.png)
![screenshots](screenshots/11.png)
![screenshots](screenshots/12.png)
![screenshots](screenshots/13.png)
![screenshots](screenshots/14.png)
![screenshots](screenshots/15.png)

Verify Azure VM connection in cloud shell(Bash):-

![screenshots](screenshots/16.png)
![screenshots](screenshots/17.png)

Verify connection through Run command:-

![screenshots](screenshots/18.png)
Run Commands runs a command on the VM without connecting.

Verify connection through serial console:-

![screenshots](screenshots/19.png)
serial console is how you reach a VM when SSH or networking is broken.

Connect it from your own computer's terminal using Windows Powershell:-
![screenshots](screenshots/20.png)


### CLI VM
Create VM:

![screenshots](screenshots/23.png)

VM list:

![screenshots](screenshots/24.png)

Test Connection:

![screenshots](screenshots/25.png)
![screenshots](screenshots/26.png)
![screenshots](screenshots/27.png)

### Install Nginx using Azure Cloud Shell:

Login to VM server:

![screenshots](screenshots/Ng1.png)

Update Ubuntu server:

![screenshots](screenshots/ng2.png)

Install Nginx:

![screenshots](screenshots/ng3.png)

Start, Enable Nginx and check status:

![screenshots](screenshots/ng4.png)

Test locally to see HTML output:

![screenshots](screenshots/ng5.png)

Curl public Ip:

![screenshots](screenshots/ng6.png)
Failed because Ubuntu VM cannot establish a connection to public IP on port 80.

Check whether Nginx listens on port 80:

![screenshots](screenshots/ng7.png)
This means Nginx is listening for incoming HTTP connections.

Add Azure firewall settings for TCP port 80:

![screenshots](screenshots/ng8_allowport80.png)
![screenshots](screenshots/ng9.png)

Tests access through the VM's public IP:-

![screenshots](screenshots/ng10.png)

Check IP in browser:

![screenshots](screenshots/ng11.png)

### Windows VM

Create Windows VM:
![screenshots](screenshots/winVM.png)

Connect through RDP:
![screenshots](screenshots/winVM1.png)

Open powershell inside VM and test host:
![screenshots](screenshots/winVM2.png)

### SSH KEYS

###How to find SSH Keys in Azure Portal:

![screenshots](screenshots/SSH1.png)
![screenshots](screenshots/SSH2.png)
![screenshots](screenshots/SSH3.png)
Here public key is stored.

###How to find SSH Keys in Azure CLI:

![screenshots](screenshots/SSH4.png)

###How to upload private SSH Key in Azure CLI:

![screenshots](screenshots/SSH5.png)
![screenshots](screenshots/SSH6.png)
![screenshots](screenshots/SSH7.png)

###How to login to VM server in Azure CLI:

First give permission to private key to access and then login to VM server.
![screenshots](screenshots/SSH8.png)

###How to generate key pair:

Generate key-pair can be done in any terminal like Windows Powershell, Azure cloud shell.
Creation in windows Powershell.
Create file path and key-pair:
![screenshots](screenshots/SSH9_generate key pair.png)

Login to VM:

![screenshots](screenshots/SSH10_login_to_vm.png)

Update Public key:
Because i have generated new key pair.

![screenshots](screenshots/SSH11_update_public_key.png)

Note: Reset password can be done in many cases:
1. You forget your VM password.
2. You lose your private key.(In that case you need to generate public and private key again and update both).
3. You need to reset any configuration.
4. You want to opt for password authentication instead of SSH keys.

Upload Private key:
Open file explorer:

![screenshots](screenshots/SSH12_openFileExplorer.png)

Make directory of .ssh(if not have already) and give permissions.
![screenshots](screenshots/SSH13_uploadPrivateKey.png)

Move private key into .ssh folder and give permissions.
Login to VM server
![screenshots](screenshots/SSH14_logintoVMthroughCloudShell.png)

Creation in cloud shell:
Same procedure to follow.

![screenshots](screenshots/SSH14_generateKeyPairThroughCloudShellAndLoginToVM.png)


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

