---
title: Linux Docker Caddy 설치
description: >-
  Linux(Debian) 환경에서 Docker와 Portainer를 활용해 Caddy 웹 서버를 설치하고, Caddyfile 전역 설정 및 역방향 프록시/파일 서버 구축 방법을 안내합니다.
date: 2026-10-02 19:30:00 +0900
categories: [Linux, Docker]
tags: [
  raspberry pi,
  linux,
  debian,
  docker,
  portainer,
  caddy,
]
media_subpath: '/posts/20261002/2'
---

## Caddy 설치

### 1. 작업 디렉토리 생성

Caddy 설정 파일을 저장할 폴더 생성.

```bash
mkdir -p ~/docker/datas/caddy/data                 # caddy 내부 데이터 저장소
mkdir -p ~/docker/datas/caddy/config               # caddy 설정 저장소
mkdir -p ~/docker/datas/caddy/server/file-server   # (선택) caddy 파일 서버
mkdir -p ~/docker/configs/caddy                    # Caddyfile 저장 위치

cd ~/docker/configs/caddy                          # 디렉토리 이동
```

### 2. Caddyfile 생성 및 수정

`Caddyfile` 파일 경로

`/home/<사용자>/docker/configs/caddy/Caddyfile`{: .filepath}

```text
{
  # 1. SSL/TLS 인증서 발급용 이메일 주소
  email mail@example.com

  # 2. 첫 번째 인증서 발급 기관 (주 인증 기관: ZeroSSL)
  # Caddy가 도메인 인증서를 발급받을 때 1순위로 시도하는 ACME 서버 엔드포인트입니다.
  cert_issuer acme {
    dir https://acme.zerossl.com/v2/DV90
  }

  # 3. 두 번째 인증서 발급 기관 (1차 백업: Let's Encrypt)
  # ZeroSSL 발급 실패(서버 장애, 요청 제한 등) 시 2순위로 자동으로 전환되어 발급을 시도합니다.
  cert_issuer acme {
    dir https://acme-v02.api.letsencrypt.org/directory
  }

  # 4. 세 번째 인증서 발급 기관 (2차 백업: SSL.com)
  # 앞선 두 기관 모두 실패할 경우 마지막으로 전환되어 발급을 시도합니다.
  cert_issuer acme {
    dir https://acme.ssl.com/ssl20/certificate
  }
}

# (선택) 파일 서버
file.example.com {
  root * /srv/file-server
  file_server {
    browse
    index off
  }
}

example.com {
  reverse_proxy localhost:8080
}
```

---

## Caddy Stack 추가

이전에 설치한 [Portainer](../1-linux-docker-portainer-install)를 활용하여 Stack을 쉽게 추가할 수 있다.

![step_1](step_1.png)
![step_2](step_2.png)

1. 좌측 `local` Docker에서 `Stacks` 클릭.
2. 우측 상단에 `Add stack` 클릭.
3. Stack `Name` 설정.
4. compose 입력.
5. 화면 하단 스크롤해서 `Deploy the stack` 클릭하여 완료.

```yaml
services:
  caddy:
    container_name: caddy
    image: caddy:latest
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
    ports:
      - "80:80"     # HTTP
      - "443:443"   # HTTPS
    volumes:
      # Caddy 내부 데이터 저장소
      - /home/<사용자>/docker/datas/caddy/data:/data

      # Caddy 설정 저장소
      - /home/<사용자>/docker/datas/caddy/config:/config

      # (선택) 파일 서버
      - /home/<사용자>/docker/datas/caddy/server/file-server:/srv/file-server

      # 메인 설정 파일
      - /home/<사용자>/docker/configs/caddy/Caddyfile:/etc/caddy/Caddyfile
```

## 결과 확인

![caddy-url](caddy-url.png)
![caddy-file-server](caddy-file-server.png)

---

## 참고 자료

- [**Docker Image Caddy**][docker-image-caddy]

---

_`Last updated: 2026-10-02`_{: .right}

[docker-image-caddy]: https://hub.docker.com/_/caddy
