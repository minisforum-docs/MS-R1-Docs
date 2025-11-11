# MS-R1 — Docker에서 Jellyfin 실행하기

## 개요

MS-R1은 ARM Immortalis-G720 MC10 GPU가 통합된 ARM V9 아키텍처 기반 CIX CP8180 프로세서를 탑재하고 있습니다.
CP8180는 AV1,H265,H264 영상 디코딩과 H265,H265 영상 인코딩을 지원합니다.

Jellyfin은 오픈소스 미디어 서버 소프트웨어로, 개인이 보유한 영화, TV 프로그램, 음악, 사진 등을 여러 기기로 관리, 정리, 스트리밍할 수 있습니다. Plex 또는 Emby와 유사하지만 완전히 무료이며 자가 호스팅 방식으로 동작합니다.

이 PlayBook을 통해 하드웨어 인코딩 및 디코딩이 지원되는 Jellyfin을 실행할 수 있습니다.

## 사전 준비사항

- 대상 머신에 Docker 및 Docker Compose가 설정되어 있어야 합니다. (참조: ../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md — "Prepare Docker environment" 단계).
- 대상에 복사해야 할 파일: `jellyfin-cix.tar.gz` and `docker-compose.yml`.
- 호스트 측 미디어 디렉터리 (예시: `C:\DATA\Media`).
- `docker-compose` 설치(`apt install docker-compose`)

## 제공 파일

다음 위치에서 다운로드해주세요:

- jellyfin-cix.tar.gz — CP8180 코덱 라이브러리가 내장된 사전 빌드된 Jellyfin 이미지
- docker-compose.yml — 장치 및 볼륨 마운트를 구성한 설정 파일

## 단계

1. 파일을 대상 MS-R1 호스트에 복사 (예: C:\DATA\jellyfin).

2. Docker 이미지 로드:

   ```bash
   gunzip -c jellyfin-cix.tar.gz | docker load
   # 확인
   docker images | grep jellyfin
   ```

3. 컨테이너 실행:

   ```bash
   # from the directory with docker-compose.yml
   docker-compose up -d
   # 확인
   docker ps
   ```

4. 클라이언트에서 Jellyfin 접속:

   - http://<호스트-IP>:8096 접속 (예: http://192.168.1.100:8096)

5. 호스트 경로에 미디어 추가 (예시):

   - 미디어를 C:\DATA\Media에 저장 (컨테이너 내부에 /Media로 마운트됨)
   - 권장: 컨테이너 사용자(보통 UID 1000)와 권한 일치 여부 확인:

   ```powershell
   # Windows: Docker Desktop / WSL 백엔드에서 파일이 접근 가능해야 합니다
   # Linux 호스트:
   sudo chown -R 1000:1000 /DATA/Media
   ```

6. Jellyfin 웹 UI에서 `/Media`를 라이브러리 경로로 추가하고 미디어 스캔 실행

## 문제 해결

- Docker Compose 실행 시 장치 오류 발생:
  Error response from daemon: error gathering device information while adding custom device "/dev/video2": no such file or directory

  - `docker-compose.yml`의 `devices:` 항목에서 존재하지 않는 장치 엔트리 제거
  - `docker-compose up -d` 재실행

- 컨테이너 실행 및 포트 바인딩 확인:
  ```bash
  docker ps --filter "name=jellyfin"
  ```

## 참고

- 하드웨어 디코딩/인코딩 지원은 이미지에 포함된 CP8180 드라이버 및 `docker-compose`에 지정된 장치 매핑에 따라 달라집니다.
- 추가로 호스트 디렉터리를 마운트해야 할 경우, `docker-compose.yml`의 `volumes:` 항목을 수정한 후 스택을 재시작해주세요.

```bash
docker-compose up -d
```
