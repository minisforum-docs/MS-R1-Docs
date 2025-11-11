# MS-R1에 PVE를 설치하는 방법

## 소개

이 문서는 MS-R1의 Debian 12 시스템에 PVE 8.3을 설치하는 방법을 설명합니다.
설치 중에는 반드시 네트워크 케이블이 연결되어 있어야 합니다. 본 가이드는 인터넷 연결이 이미 가능한 상태를 전제로 합니다.

## 설치 방법

### 1. 환경 준비

#### 1.1 root 비밀번호 설정

모든 단계는 root 권한이 필요합니다.  
root 사용자 모드로 전환:

```bash
sudo -i
```

root 비밀번호 설정:

```bash
passwd root
```

PVE 웹 관리자는 root 계정을 사용하여 로그인합니다. root 비밀번호가 반드시 설정되어 있어야 합니다:

```bash
passwd root
```

#### 1.2 `sshd` 설치 및 root 로그인 허용

```bash
apt update
apt install ssh
# root 로그인 허용 (추가, 덮어쓰기 아님)
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
systemctl restart ssh
```

### 2. 저장소 추가

#### 2.1 PVE ARM 커뮤니티 저장소 소스 추가

```bash
curl -L https://mirrors.lierfang.com/pxcloud/lierfang.gpg -o /etc/apt/trusted.gpg.d/lierfang.gpg
echo "deb https://mirrors.lierfang.com/pxcloud/pxvirt bookworm main" >/etc/apt/sources.list.d/pxvirt-sources.list
apt update
```

다른 커뮤니티 미러도 있습니다. 가장 빠른 것을 사용하시면 됩니다:

- 중국: https://mirrors.lierfang.com
- 독일: https://de.mirrors.lierfang.com
- Cloudflare: https://mirrors.apqa.cn
- 네덜란드: https://apt.dedi.zone/pxcloud/pxvirt
- 일본: https://mirrors.homelabproject.cc

### 3. 네트워크 구성 및 PVE용 hosts 수정

> **경고**
>
> 1. `192.168.32.116` 은 예시 IP 주소입니다.
> 2. 반드시 자신의 IP 주소로 교체하세요.

#### 3.1 PVE를 위한 /etc/hosts 수정

`ip addr show` 명령으로 인터페이스 이름과 IP를 확인한 다음, `/etc/hosts`를 편집합니다:

```bash
nano /etc/hosts
```

예시 내용:

```
127.0.0.1       localhost
192.168.32.116 mini-localhost
127.0.1.1       mini-localhost
```

호스트 IP와 호스트명이 포함하여 추가했는지 다시한번 확인해주세요(예: `192.168.32.116 mini-localhost`).

#### 3.2 NetworkManager 비활성화 및 ifupdown2 사용

> **참고**
> 네트워크 관리자를 변경하여 PVE 네트워크 구성만 사용하도록 설정해주세요. 원한다면 데비안의 NetworkManager를 유지할 수 있지만, 이 경우 네트워크 변경은 데스크톱 네트워크 패널을 통해 수행해야 합니다.
>
> **경고**
> 만약 이 단계를 이해하지 못하셨다면, 3.2를 건너뛰어 주세요.

PVE는 ifupdown2를 사용합니다. NetworkManager를 비활성화하고 ifupdown2를 설치합니다:

```bash
systemctl disable NetworkManager
systemctl stop NetworkManager
apt update
apt install ifupdown2 -y
rm -f /etc/network/interfaces.new
```

현재 네트워크 상태를 확인합니다:

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

위의 `eth1`에 IP 주소가 있습니다. 내 IP는 `192.168.32.116`, 게이트웨이는 `192.168.32.1`입니다.
가상 브리지를 생성하여 VM이 호스트 네트워크를 공유할 수 있도록 네트워크 설정 파일을 만듭니다.

!!!note
네트워크 장치 이름은 `eth0`, `eth1`로 표시될 수도 있고, 시스템 패키지 업그레이드 후 `enp1s0`, `enp49s0`와 같이 표시될 수도 있습니다.
실제 장치 이름에 따라 수정해야 합니다.

```bash
nano /etc/network/interfaces
```

예시 내용:

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

변경 사항을 적용하려면 재부팅하세요.

> **경고**
> 이 단계 이후 네트워크는 고정 IP로 설정됩니다. 사용 중인 물리적 포트를 확인하고 구성에 확신이 들 때까지 네트워크 배선을 변경하지 마십시오. 잘못된 네트워크 설정은 해당 기기에 접근할 수 없게 만들 수 있습니다.

### 4. PVE 소프트웨어 설치

#### 4.1 PVE 패키지 설치

```bash
apt remove cix-openssl || true
apt install rsyslog
modprobe fuse
apt update && apt install proxmox-ve
```

주의사항:

1. `cix-openssl` 패키지가 있으면 제거하세요.
2. `fuse` 커널 모듈을 미리 로드해주세요.
3. 설치 중 이메일 설정이나 kexec-tools 관련 질문이 나오면 "구성 안 함" 또는 "No"를 선택하세요.

#### 4.2 네트워킹 전 커널 모듈 로드 서비스 추가

네트워크 서비스 전에 필요한 모듈이 로드되도록 systemd 유닛을 생성합니다:

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

#### 4.3 그래픽 인터페이스 비활성화 (선택)

그래픽 데스크탑이 필요하지 않다면 다음 명령으로 비활성화할 수 있습니다:

```bash
systemctl set-default multi-user.target   # 그래픽 인터페이스 비활성화
# 다시 활성화하려면:
# systemctl set-default graphical.target
```

### 5. 재부팅 및 PVE VM 시스템 사용

모든 변경 사항을 적용하기 위해 재부팅합니다:

```bash
reboot
```

### 6. 참고 사항 및 팁:

- 코어 그룹: "0-1,10-11"은 big 코어, "6-9"는 middle 코어, "2-5"는 small 코어입니다. VM의 vCPU에서 big/middle 코어와 small 코어를 혼합하면 부팅 문제가 생길 수 있으므로 VM 생성 후 코어 친화도(affinity)를 설정하십시오.
- 코어 주파수 및 OpenSSL AES-128-GCM 점수 (예시):

| 코어 번호 | 아키텍처    | 주파수  | OpenSSL AES-128-GCM 점수 |
| --------- | ----------- | ------- | ------------------------ |
| 0         | Cortex-A720 | 2.6 GHz | 2,564,396.37 k           |
| 1         | Cortex-A720 | 2.6 GHz | 2,554,669.74 k           |
| 2         | Cortex-A520 | 1.8 GHz | 564,560.75 k             |
| 3         | Cortex-A520 | 1.8 GHz | 564,230.87 k             |
| 4         | Cortex-A520 | 1.8 GHz | 566,136.16 k             |
| 5         | Cortex-A520 | 1.8 GHz | 566,869.85 k             |
| 6         | Cortex-A720 | 2.4 GHz | 2,258,687.32 k           |
| 7         | Cortex-A720 | 2.4 GHz | 2,254,432.94 k           |
| 8         | Cortex-A720 | 2.3 GHz | 2,154,856.45 k           |
| 9         | Cortex-A720 | 2.3 GHz | 2,157,428.74 k           |
| 10        | Cortex-A720 | 2.5 GHz | 2,451,182.93 k           |
| 11        | Cortex-A720 | 2.5 GHz | 2,451,466.92 k           |
