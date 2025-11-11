# MS-R1 운영 체제 재설치 가이드

## 소개

MS-R1은 표준 UEFI BIOS를 사용하며, 모든 UEFI 호환 시스템 ISO를 부팅할 수 있습니다. CPU 드라이버는 아직 메인라인 Linux 커널에 완전히 병합되지 않았지만, MINISFORUM의 공식 시스템 이미지가 최적의 사용자 경험을 제공합니다.

> **참고**: 드라이버가 메인라인 Linux 커널에 병합되면, 일반 x86 PC와 동일하게 어떤 운영 체제도 사용할 수 있습니다.

## 시스템 요구사항

- 최소 40GB 저장 공간
- 시스템 설치용 USB 드라이브 또는 SSD
- 시스템 이미지를 다운로드할 수 있는 인터넷 연결

## 설치 단계

### 1. 시스템 이미지 다운로드

최신 버전: `MS-R1-2025101202-0003301.7z`

다운로드 링크: [Google Drive Link](https://drive.google.com/drive/folders/1FzjO50RYqKi-BIOYH2wduRYeeju-MRIU?usp=drive_link)

### 2. 시스템 디스크에 이미지 쓰기

#### Windows 사용자

1. `balenaEtcher` 다운로드 및 설치
2. 시스템 이미지 압축 해제
3. `balenaEtcher`를 사용하여 시스템 디스크에 이미지 쓰기

#### Linux 사용자

1. 시스템 이미지 압축 해제
2. `dd` 명령어를 사용하여 시스템 디스크에 이미지 쓰기

### 3. 시스템 접속

기본 로그인 정보:

- 사용자 이름: `mini`
- 비밀번호: `mini`

## 추가 자료

### 오픈 소스 코드

시스템 소스 코드는 GitHub에서 확인 가능합니다: [minisforum-cix-p1-repo](https://github.com/orgs/minisforum-cix-p1-repo)

### 관련 문서

- [MS-R1 기본 가이드](../PlayBook/MS-R1-BaseGuide.md)
- [PVE 설치 방법](../PlayBook/MS-R1-How-To-Install-PVE.md)
- [Docker를 통한 안드로이드 실행 방법](../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md)

> **경고**  
> 시스템 설치를 진행하기 전에 모든 중요한 데이터를 반드시 백업하십시오.
