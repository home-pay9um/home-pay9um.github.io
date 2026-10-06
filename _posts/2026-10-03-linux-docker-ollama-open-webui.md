---
title: Linux [Docker, Portainer] ollama, openWebUI 설치
description: >-
  Linux(Debian) 환경에서 Docker와 Portainer를 활용해 Ollama 및 Open-WebUI 컨테이너를 구축하고 실행하는 방법을 정리합니다.
date: 2026-10-03 22:30:00 +0900
categories: [Linux, Docker]
tags: [
  raspberry pi,
  linux,
  debian,
  docker,
  portainer,
  ollama,
  open-webui,
]
---

## ollama, openWebUI 설치

### 1. 디렉토리 생성

```bash
mkdir -p ~/docker/datas/ollama/data       # ollama 데이터 저장소
mkdir -p ~/docker/datas/open-webui/data   # openWebUI 데이터 저장소
```

---

## Stack 추가

[**Caddy를 설치할 때와 마찬가지**](../2-linux-docker-caddy-install#caddy-stack-추가)로 똑같이 진행하면 된다.

```yaml
services:
  ollama:
    container_name: ollama
    image: ollama/ollama:latest
    restart: unless-stopped
    ports:
      - "11434:11434"
    volumes:
      - /home/<사용자>/docker/datas/ollama/data:/root/.ollama
  open-webui:
    container_name: open-webui
    image: ghcr.io/open-webui/open-webui:main
    restart: unless-stopped
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
    volumes:
      - /home/<사용자>/docker/datas/open-webui/data:/app/backend/data
    depends_on:
      - ollama
```

**다운로드 하는 데 시간이 조금 걸린다.**

다운로드가 완료되면 [**localhost:3000**](localhost:3000)로 접속하면 된다.

> 로컬 환경으로 접속이 어려운 경우 [**portainer**](../1-linux-docker-portainer-install#1-ip-주소-접속) 접속할 때와 같은 방법으로 접속.
{: .prompt-info}

---

## 모델 추천

> 라즈베리파이 5 (8GB) 기준 모델 추천
>
> **llama 3.2 (1b / 3b):** Meta 범용 모델로 균형 잡힌 대화 성능.
>
> **gemma 3 (1b / 4b):** Google 경량 모델로 속도가 매우 빠름.
>
> **qwen 2.5 / 3 (1.5b / 3b):** 한국어 이해도가 상대적으로 높은 모델.
{: .prompt-info }

---

_`Last updated: 2026-10-03`_{: .right}