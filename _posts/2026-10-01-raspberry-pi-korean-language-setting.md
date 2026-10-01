---
title: 라즈베리 파이 한글 설정 (깨짐 해결 포함)
description: >-
  라즈베리 파이 OS 환경에서 한글 깨짐 문제를 해결하기 위한 폰트 설치, raspi-config 및 GUI 기반의 로케일·시간대·키보드 설정 절차 가이드입니다.
date: 2026-10-01 15:00:00 +0900
categories: [라즈베리 파이, 기본 설정]
tags: [
  raspberry pi,
  linux,
  debian,
]
media_subpath: '/posts/20261001'
---

## 1. 한글 폰트 설치

```bash
sudo apt install fonts-unfonts-core
```

## 2. 한글 설정 (raspi-config)

![raspi-config](raspi-config.png)

`Localisation Options` 선택.

### L1. Locale

![L1-Locale](L1-Locale.png)

`Locale` 선택.

> `Locale`: 시스템 메시지, 메뉴 등의 표시 언어
{: .prompt-tip }

![L1-check-en](L1-check-en.png)
![L1-check-ko](L1-check-ko.png)

`en_US`, `ko_KR` 2개 선택(스페이스바).

![L1-set-locale](L1-set-locale.png)

**본인이 원하는** 기본 언어 설정.

> 기본 언어는 영어(en_US)를 추천하긴 하지만 사용하기 편한 쪽으로 선택.

### L2. Timezone

![L2-Timezone](L2-Timezone.png)

`Timezone` 선택.

![L2-area](L2-area.png)

`Asia` 선택.

![L2-zone](L2-zone.png)

`Seoul` 선택.

### L3. Keyboard

![L3-Keyboard](L3-Keyboard.png)

`Keyboard` 선택하면 설정창이 잠시 닫히면서 자동으로 키보드 설정.
: 만약 제대로 키도브가 잡히지 않을 경우 [직접 설정](#keyboard).

### L4. WLAN Country

![L4-WLAN-Country](L4-WLAN-Country.png)

`WLAN Country` 선택.

![L4-country](L4-country.png)

`KR Korean (South)` 선택.

![L4-set](L4-set.png)

성공.

### 결과

설정을 마치고 `<Finish>`를 눌러 아래와 같이 나오면 재시작.

![success](success.png)

한글 깨짐 확인하기.

![result](result.png)

---

## 3. 한글 설정 (GUI)

![GUI](GUI.png)

### Locale

![GUI-Locale](GUI-Locale.png)

### Timezone

![GUI-Timezone](GUI-Timezone.png)

### Keyboard

![GUI-Keyboard](GUI-Keyboard.png)

### WLAN Country

![GUI-WiFi-Country](GUI-WiFi-Country.png)

---

_`Last updated: 2026-10-01`_{: .right}
