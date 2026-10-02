---
title: Linux Docker Portainer 설치
description: >-
  Linux(Debian) 환경에서 Docker Compose를 활용해 Portainer(Community Edition)를 설치하고 웹 UI 접속 및 초기 로그인 방법을 안내합니다.
date: 2026-10-02 18:30:00 +0900
categories: [Linux, Docker]
tags: [
  raspberry pi,
  linux,
  debian,
  docker,
  portainer,
]
media_subpath: '/posts/20261002/1'
---

## Portainer 설치

### 1. 작업 디렉토리 생성

Portainer 설정 파일을 저장할 폴더 생성.

```bash
mkdir -p ~/docker/datas/portainer/data    # portainer 데이터 저장 위치
mkdir -p ~/docker/configs/portainer       # portainer-compose.yaml 저장 위치

cd ~/docker/configs/portainer             # 디렉토리 이동
```

### 2. portainer-compose.yaml 파일 다운로드

`curl` 명령어를 사용하여 compose 파일 다운로드.

```bash
curl -L https://downloads.portainer.io/ce-sts/portainer-compose.yaml -o portainer-compose.yaml
```

### 3. portainer-compose.yaml 파일 수정

다운로드한 파일의 볼륨 경로 등을 설정에 맞춰 수정.

> Portainer 데이터 저장 위치(볼륨 경로)를 올바르게 지정해야 함.

```bash
services:
  portainer:
    container_name: portainer
    image: portainer/portainer-ce:lts
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /home/<사용자>/docker/datas/portainer/data:/data
    ports:
      - 9443:9443
      - 8000:8000  # Remove if you do not intend to use Edge Agents

networks:
  default:
    name: portainer_network
```

### 4. 배포

`portainer-compose.yaml` 파일이 있는 디렉토리에서 아래 명령어를 실행하여 배포.

```bash
docker compose -f portainer-compose.yaml up -d
```

### 5. 확인

Docker Compose가 필요한 리소스를 생성하고 Portainer를 배포한다.

아래 명령을 실행하여 Portainer 서버 컨테이너가 시작되었는지 확인.

```bash
docker ps
```

---

## Portainer 로그인

### 1. IP 주소 접속

설치가 완료되면 웹 브라우저를 열고 다음 주소로 이동하여 Portainer 서버 인스턴스에 로그인.

로컬 환경에서 접속
: [**https://localhost:9443**](https://localhost:9443)

디스플레이가 없는 환경일 경우
: 같은 네트워크망 내의 다른 기기(PC, 스마트폰 등) 웹 브라우저에서 아래 주소로 접속
: `https://<Portainer 기기 IP>:9443` (예: `https://192.168.0.10:9443`)

> 서버 터미널에서 `hostname -I` 명령어를 실행하여 IP 주소를 확인할 수 있다.
{: .prompt-info }

### 2. 사용자 생성

![portainer-signup_1](portainer-signup_1.png)

IP 접속에 성공하면 사용자 생성 화면이 뜬다.

`Username`과 `Password`를 입력하면 되는데 맨 마지막에 `Setup token`이 있다.

저 토큰은 아래 명령어를 통해 확인이 가능하다.

```bash
docker logs portainer --tail 50
```

```bash
# 아래처럼 토큰이 발급된다.
==========================

setup_token=<token>

Paste it into the setup screen, or send it in the X-Setup-Token header.
Start with --no-setup-token to disable.

========================== 
```

> 토큰 생성 후 5분 내로 입력하지 않으면 재시도를 해야 하므로 빠르게 입력하자.
{: .prompt-info }

![portainer-signup_2](portainer-signup_2.png)

이 부분은 당장 중요한 것이 아니라면 `skip`.

![portainer](portainer.png)

**접속 화면**이 보이면 끝.

---

## 참고 자료

- [**Install Portainer CE with Docker on Linux**][install-portainer-ce-with-docker-on-linux]

---

_`Last updated: 2026-10-02`_{: .right}

[install-portainer-ce-with-docker-on-linux]: https://docs.portainer.io/start/install-ce/server/docker/linux
