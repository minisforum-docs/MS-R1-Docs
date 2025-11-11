# MS-R1 — Docker에서 안드로이드 실행하기

## 개요

MS-R1은 많은 안드로이드 기기와 동일한 아키텍처를 사용하는 ARM 기반 미니 PC입니다. MS-R1에서 Docker를 통해 안드로이드를 실행하면 기존 x86 에뮬레이터보다 더 나은 호환성을 경험할 수 있습니다. 이 간단 가이드는 환경을 준비하고 안드로이드 컨테이너를 실행하는 방법을 설명합니다.

---

## 사전 준비사항

- Debian/Ubuntu 기반 OS가 설치된 MS-R1 (명령어 Debian/Ubuntu 기준).
- 루트 권한 (`sudo` 사용).
- 만약 MS-R1에 Proxmox VE(PVE)를 이미 설치했다면 아래의 PVE 관련 준비 단계를 생략할 수 있습니다.
- `unzip` 설치.

---

## 안드로이드용 Docker 환경 준비

1. 루트 권한 획득:

   ```bash
   sudo -i
   ```

2. (레거시 iptables가 필요한 시스템 전용) Docker 네트워크 문제 예방을 위한 iptables 레거시 모드 설정:

   ```bash
   update-alternatives --set iptables /usr/sbin/iptables-legacy
   update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
   ```

   참고: 이미 PVE를 구성했거나 nftables 호환 Docker 빌드를 사용하는 경우 이 단계를 건너뛰어주세요.

3. Docker 설치:

   ```bash
   apt update
   apt install -y docker.io unzip
   ```

4. Docker 실행 확인:

   ```bash
   docker ps
   # 예상 출력 (컨테이너 없음): "CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS    PORTS   NAMES"
   ```

5. 선택 사항: sudo 없이 Docker 명령 실행 권한 부여:

   ```bash
   # <your-user> 본인 사용자명으로 변경
   usermod -aG docker <your-user>
   # 그룹 변경을 위해 로그아웃 후 다시 로그인해주세요. (또는 재부팅)
   ```

---

## Docker 안드로이드 패키지 다운로드

현재 사전 빌드된 Docker 이미지가 제공됩니다. 빌드 프로세스는 이후 오픈소스로 공개될 수도 있습니다.

### "MS-R1-Docker-Android-2025-10.zip" 다운로드

링크:
https://drive.google.com/file/d/1ZmzPTqV8TrBDLLztjCfDC1WhnGQs7WaJ/view?usp=sharing

### Docker 이미지 가져오기

`MS-R1-Docker-Android-2025-10.zip` 파일을 MS-R1에 업로드하거나 복사한 후 압축 해제해주세요:

```bash
unzip MS-R1-Docker-Android-2025-10.zip
```

스크립트를 사용하여 Docker 이미지를 불러옵니다

```bash
cd MS-R1-Docker-Android-2025-10
chmod +x *.sh
./load_super_image.sh sky1_evb-14-userdebug-super.img.tgz
```

`Error: No such image: cix_android:14`, `Error: No such image: 14` 등의 오류가 표시되더라도 무시해도 됩니다.

## Docker 안드로이드 실행

```bash
./run_android.sh -c 0 -d 0 -m 0 -v 14
```

여러 개의 안드로이드 Docker 컨테이너를 동시에 실행할 수 있습니다.
예시:

```bash
# 첫 번째 안드로이드 컨테이너
run_android.sh -c 0 -d 0 -m 0 -v 14
# 두 번째 안드로이드 컨테이너
run_android.sh -c 1 -d 2 -m 0 -v 14
# 세 번째 안드로이드 컨테이너
run_android.sh -c 2 -d 4 -m 0 -v 14
```

## Escrcpy를 이용해 안드로이드에 접속하기

시스템 및 아키텍처에 맞는 최신 Escrcpy를 다운로드하고 설치해주세요.
[https://github.com/viarotel-org/escrcpy/releases/](https://github.com/viarotel-org/escrcpy/releases/)
예시:
| 아키텍처 및 시스템 | 다운로드 파일명 |
|----------|--------------|
| Windows X86 PC | [Escrcpy-1.32.1-win-setup-x64.exe](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-win-setup-x64.exe)|
| 구형 X86 Mac | [Escrcpy-1.32.1-mac-x64.dmg](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-mac-x64.dmg) |
| ARM 기반 Mac | [Escrcpy-1.32.1-mac-arm64.dmg](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-mac-arm64.dmg) |

연결 메뉴에서 MS-R1의 IP 주소를 입력합니다.
포트 = 10000 + (컨테이너 번호)
예시:

| 안드로이드 컨테이너 번호 | 포트  |
| ------------------------ | ----- |
| 0                        | 10000 |
| 1                        | 10001 |
| 2                        | 10002 |
