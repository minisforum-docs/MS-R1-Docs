# How to install PVE into MS-R1

## Intro
You can install PVE 8.3 into MS-R1's Debian 12 system.  
Please ensure that the network cable is connected during installation. This guide assumes already have Internet connection is available.

## How To Install

### 1. Prepare your environment

#### 1.1 Set the root password
Every step in this playbook should have root permission.  
Change to root user mode
```bash
sudo -i
```
Set root password
```bash
passwd root
```

PVE needs the root account to log in to the web admin. Ensure the root password is set:
```bash
passwd root
```


#### 1.2 Install `sshd` and allow root login
```bash
apt update
apt install ssh
# Allow root login (append, don't overwrite)
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
systemctl restart ssh
```

### 2. Add repo

#### 2.1 Add PVE ARM community repo source
```bash
curl -L https://mirrors.lierfang.com/pxcloud/lierfang.gpg -o /etc/apt/trusted.gpg.d/lierfang.gpg
echo "deb https://mirrors.lierfang.com/pxcloud/pxvirt bookworm main" >/etc/apt/sources.list.d/pxvirt-sources.list
apt update
```

There are other community mirrors; choose the fastest for you:
- China: https://mirrors.lierfang.com
- Germany: https://de.mirrors.lierfang.com
- Cloudflare: https://mirrors.apqa.cn
- Netherlands: https://apt.dedi.zone/pxcloud/pxvirt
- Japan: https://mirrors.homelabproject.cc

### 3. Configure networking and modify hosts for PVE

> **Warning**
> 1. All occurrences of the IP address `192.168.32.116` are examples.  
> 2. Replace them with your own IP address.

#### 3.1 Modify /etc/hosts for PVE
Get your interface name and IP with `ip addr show`, then edit `/etc/hosts`:
```bash
nano /etc/hosts
```
Example contents:
```
127.0.0.1       localhost
192.168.32.116 mini-localhost
127.0.1.1       mini-localhost
```
Make sure you added the line with your host IP and hostname (example: `192.168.32.116 mini-localhost`).

#### 3.2 Disable NetworkManager and use ifupdown2
> **Warning**
> Enabling full vmbr networking functionality in Proxmox VE (PVE) requires migrating the network configuration to ifupdown2. Otherwise, you will not be able to add any network devices within PVE.


PVE uses ifupdown2. Disable NetworkManager and install ifupdown2:
```bash
systemctl disable NetworkManager
systemctl stop NetworkManager
apt update
apt install ifupdown2 -y
rm -f /etc/network/interfaces.new
```

Check current networking:
```bash
root@cix-localhost:~# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 38:05:25:30:bf:e5 brd ff:ff:ff:ff:ff:ff
    altname eth0
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 38:05:25:30:bf:e6 brd ff:ff:ff:ff:ff:ff
    altname eth1
    inet 192.168.32.116/24 brd 192.168.32.255 scope global dynamic noprefixroute eth1
       valid_lft 5741sec preferred_lft 5741sec
    inet6 fe80::8574:50d4:6212:23f2/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
root@cix-localhost:~# ip route
default via 192.168.32.1 dev eth1 proto dhcp src 192.168.32.116 metric 100 
192.168.32.0/24 dev eth1 proto kernel scope link src 192.168.32.116 metric 100

```
There have a ip address on `eth1`. My ip address is `192.168.32.116`, my gateway is `192.168.32.1`
Create networking config file. (Create virtual bridge let VMs can share host networkign)

!!!note
    Sometime, network device name show as `eth0` `eth1`, but sometimes or you upgrade your system package, it will show as `enp1s0` `enp49s0`.
    You need to base it on your actual situation.

```bash
nano /etc/network/interfaces
```
Example contents:
```
auto lo
auto vmbr0
iface vmbr0 inet static
    address 192.168.32.116/24
    gateway 192.168.32.1
    bridge-ports eth1
    bridge-stp off
    bridge-fd 0
```

Reboot to apply changes.

> **Warning**
> After this step the network is configured statically. Note the physical port used and avoid changing network wiring until you are sure of the configuration; incorrect network settings can make the machine inaccessible.

### 4. Install PVE software

#### 4.1 Install PVE packages
```bash
apt remove cix-openssl || true
apt install rsyslog
modprobe fuse
apt update && apt install proxmox-ve
```

Notes:
1. Remove `cix-openssl` if present.
2. Load the `fuse` kernel module.
3. When prompted: choose "no configuration" for email and "No" for kexec-tools if asked.

#### 4.2 Add a service to load kernel modules before networking
Create a systemd unit so required modules are loaded before network services:
```bash
tee /etc/systemd/system/load-beforenet.service << 'EOF'
[Unit]
Description=Load Bridge Module
DefaultDependencies=no
Before=network.target
Before=pve-cluster.service

[Service]
Type=oneshot
ExecStart=/sbin/modprobe -a bridge fuse
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
EOF

systemctl enable load-beforenet.service
systemctl start load-beforenet.service
```

#### 4.3 Disable graphical interface (optional)
If you do not need a graphical desktop:
```bash
systemctl set-default multi-user.target   # disable graphical interface
# To re-enable:
# systemctl set-default graphical.target
```

### 5. Reboot and enjoy your PVE VMs system
Reboot the host to ensure everything is applied:
```bash
reboot
```

### 6. Notes and tips:
- Core groups: cores "0-1,10-11" are big cores; "6-9" are middle cores; "2-5" are small cores. Mixing big/middle with small cores in a VM vCPU can cause boot problems — set core affinity after creating VMs.
- Core frequency and OpenSSL AES-128-GCM scores (example):

| Core Num | Architecture | Frequency | OpenSSL AES-128-GCM Score |
|----------|--------------|-----------|---------------------------|
| 0  | Cortex-A720 | 2.6 GHz | 2,564,396.37 k |
| 1  | Cortex-A720 | 2.6 GHz | 2,554,669.74 k |
| 2  | Cortex-A520 | 1.8 GHz | 564,560.75 k |
| 3  | Cortex-A520 | 1.8 GHz | 564,230.87 k |
| 4  | Cortex-A520 | 1.8 GHz | 566,136.16 k |
| 5  | Cortex-A520 | 1.8 GHz | 566,869.85 k |
| 6  | Cortex-A720 | 2.4 GHz | 2,258,687.32 k |
| 7  | Cortex-A720 | 2.4 GHz | 2,254,432.94 k |
| 8  | Cortex-A720 | 2.3 GHz | 2,154,856.45 k |
| 9  | Cortex-A720 | 2.3 GHz | 2,157,428.74 k |
| 10 | Cortex-A720 | 2.5 GHz | 2,451,182.93 k |
| 11 | Cortex-A720 | 2.5 GHz | 2,451,466.92 k |
