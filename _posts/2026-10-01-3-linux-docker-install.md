---
title: Linux 도커(Docker) 설치
description: >-
  Linux(Debian) 환경에서 Docker 공식 저장소 등록 및 엔진 설치 방법과 함께, sudo 없이 실행 설정, 부팅 시 자동 시작 및 패키지 제거 방법을 안내합니다.
date: 2026-10-01 20:00:00 +0900
categories: [Linux, Docker]
tags: [
  raspberry pi,
  linux,
  debian,
  docker,
]
---

## Docker 설치

### 1. Docker 공식 APT 저장소 등록

```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

> `Debian testing` 또는 `Kali Linux`와 같은 파생 배포판을 사용하는 경우, 버전 코드네임을 출력하도록 되어있는 이 명령어 부분을 대체해야 할 수도 있다.
>
> ```bash
> $(. /etc/os-release && echo "$VERSION_CODENAME")
> ```
>
> 이 부분을 `trixie`와 같이 해당하는 Debian 릴리스의 코드네임으로 변경.
{: .prompt-info }

### 2. Docker 패키지 설치

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

> 설치 후 Docker가 실행 중인지 확인.
>
> ```bash
> sudo systemctl status docker
> ```
>
> Docker가 실행되고 있지 않으면 수동으로 시작.
>
> ```bash
> sudo systemctl start docker
> ```
{: .prompt-info }

### 3. 설치 확인

```bash
sudo docker run hello-world
```

이 명령어는 테스트 이미지를 다운로드하고 컨테이너에서 실행한다.

컨테이너가 실행되면 확인 메시지를 출력하고 종료된다.

```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

> 대충 이런 식으로 `Hello from Docker!` 메시지가 뜬다면 성공.

---

## sudo 없이 Docker 실행

> 보안 주의
> : `docker` 그룹 구성원은 Docker를 통해 root 수준 권한을 얻을 수 있다.
>
> 권한 분리가 필요한 서버라면 그룹에 추가하지 말고 `sudo` 유지.
{: .prompt-tip }

### 1. 그룹 생성

```bash
sudo groupadd docker
```

### 2. 사용자를 그룹에 추가

```bash
sudo usermod -aG docker $USER
```

### 3. 로그아웃 후 로그인

> 가상 머신에서 Linux를 실행하는 경우, 변경 사항을 적용하기 위해 가상 머신을 다시 시작해야 할 수 있다.

아래 명령어를 실행하여 그룹 변경 사항을 적용할 수도 있다.

```bash
newgrp docker
```

### 4. 확인

```bash
docker run hello-world
```

---

## Docker 부팅 시 자동 시작

### 1. 부팅 시 자동으로 시작

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

### 2. 부팅 시 자동으로 시작 중지

```bash
sudo systemctl disable docker.service
sudo systemctl disable containerd.service
```

---

## Docker Engine 제거

### 1. Docker 관련 패키지 제거

Docker Engine, CLI, containerd 및 Docker Compose 패키지를 제거한다.

```bash
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

### 2. 구성 파일 삭제

`images`, `containers`, `volumes` 또는 사용자 지정 구성 파일은 자동으로 삭제되지 않는다.

모든 정보를 삭제하려면 아래 명령어를 실행한다.

```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

### 3. 소스 리스트 및 키링(keyrings) 제거

```bash
sudo rm /etc/apt/sources.list.d/docker.sources
sudo rm /etc/apt/keyrings/docker.asc
```

---

## 참고 자료

- [**Install Docker Engine on Debian**][install-debian]
- [**Linux post-installation steps for Docker Engine**][post-installation-steps]

---

_`Last updated: 2026-10-02`_{: .right}

[install-debian]: https://docs.docker.com/engine/install/debian
[post-installation-steps]: https://docs.docker.com/engine/install/linux-postinstall/
