# MS-R1 — Run Android in Docker

## Overview
MS-R1 is an ARM-based mini PC sharing the same architecture as many Android devices. Running Android inside Docker on MS-R1 gives better compatibility than traditional x86 emulators. This short guide shows how to prepare the environment and run Android containers.

---

## Prerequisites
- MS-R1 with a Debian/Ubuntu-based OS (commands assume Debian/Ubuntu).
- Root privileges (use `sudo`).
- If you already installed Proxmox VE (PVE) on MS-R1, skip the PVE-specific preparation steps noted below.
- `unzip` installed.

---

## Prepare Docker environment for Android

1. Become root:
    ```bash
    sudo -i
    ```

2. (Only for systems that need legacy iptables) Set iptables to legacy backend to avoid Docker networking issues:
    ```bash
    update-alternatives --set iptables /usr/sbin/iptables-legacy
    update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
    ```
    Note: Skip this if you already configured PVE or your distro uses nftables-compatible Docker builds.

3. Install Docker:
    ```bash
    apt update
    apt install -y docker.io unzip
    ```

4. Verify Docker is running:
    ```bash
    docker ps
    # Expected (no containers): "CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS    PORTS   NAMES"
    ```

5. Optional: Allow your user to run Docker without sudo:
    ```bash
    # Replace <your-user> with your username
    usermod -aG docker <your-user>
    # Log out and back in (or reboot) for the group change to take effect
    ```

---

## Download Docker Android package
Currently there are pre-built Docker images. The build process may be open sourced later.

### Download "MS-R1-Docker-Android-2025-10.zip"
Link:
https://drive.google.com/file/d/1ZmzPTqV8TrBDLLztjCfDC1WhnGQs7WaJ/view?usp=sharing

### Import docker image
Upload or copy `MS-R1-Docker-Android-2025-10.zip` to your MS-R1 and unzip it:
```bash
unzip MS-R1-Docker-Android-2025-10.zip
```
Using scripts to import docker image
```bash
cd MS-R1-Docker-Android-2025-10
chmod +x *.sh
./load_super_image.sh sky1_evb-14-userdebug-super.img.tgz
```
If there have any errors like `Error: No such image: cix_android:14` `Error: No such image: 14` ignore it.

## Start Docker Android

```bash
./run_android.sh -c 0 -d 0 -m 0 -v 14
```
You can run mutiple Android Docker
For example
```bash
# First Android container 
run_android.sh -c 0 -d 0 -m 0 -v 14
# Second Android container 
run_android.sh -c 1 -d 2 -m 0 -v 14
# Third Android container 
run_android.sh -c 2 -d 4 -m 0 -v 14
```


## Connect to Android use "Escrcpy" and enjoy it

Download lastest Escrcpy by your system and architecture and install it. 
[https://github.com/viarotel-org/escrcpy/releases/](https://github.com/viarotel-org/escrcpy/releases/)
For example:  
| ARCH and System | Download File Name |
|----------|--------------|
| Windows X86 PC | [Escrcpy-1.32.1-win-setup-x64.exe](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-win-setup-x64.exe)|
| Old X86 Mac |  [Escrcpy-1.32.1-mac-x64.dmg](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-mac-x64.dmg) | 
| ARM based Mac | [Escrcpy-1.32.1-mac-arm64.dmg](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-mac-arm64.dmg) | 

In connect menu input your ip addr. 
Port = 10000 + (Container numer)
For example:

| Android contatiner Num | Port |
|----------|--------------|
| 0 | 10000|
| 1 |  10001 | 
| 2 | 10002 |