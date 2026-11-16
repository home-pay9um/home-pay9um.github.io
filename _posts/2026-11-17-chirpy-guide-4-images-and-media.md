---
title: Chirpy 블로그 가이드 4 - 이미지와 미디어
description: >-
  이미지를 별도의 미디어 저장소에 올리고 글에서 불러오는 절차와 주소가 만들어지는 규칙, 캡션·크기·위치·다크 모드 같은 이미지 옵션, 영상과 오디오를 넣는 방법을 정리합니다.
date: 2026-11-17 00:13:00 +0900
categories: [블로그, 가이드]
tags: [
  chirpy,
  jekyll,
  image,
  guide,
]
media_subpath: '/posts/20260826'
image:
  path: https://raw.githubusercontent.com/home-pay9um/assets/refs/heads/main/home-pay9um.github.io/commons/devices-mockup.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: 여러 기기에서 본 Chirpy 테마 화면
render_with_liquid: false
---

> 이 블로그의 이미지는 블로그 저장소가 아니라 **별도의 미디어 저장소**에 있다. 이미지를 넣을 때는 항상 "먼저 올리고, 그다음에 글에서 부른다"는 순서를 지킨다.

글의 파일 이름과 Front Matter는 [2편](/posts/chirpy-guide-2-writing-posts/)에서, 본문 문법은 [3편](/posts/chirpy-guide-3-markdown-syntax/)에서 다뤘다.

---

## 1. 미디어가 저장되는 곳

이미지는 `home-pay9um/assets` 저장소에 올리고, 블로그는 `_config.yml`의 `cdn` 설정을 통해 그 저장소의 파일을 불러온다.

```yaml
cdn: "https://raw.githubusercontent.com/home-pay9um/assets/refs/heads/main/home-pay9um.github.io"
avatar: "/profile.jpg"
```
{: file="_config.yml" }

미디어 저장소의 구조는 다음과 같다.

```text
assets/                              # home-pay9um/assets 저장소 (main 브랜치)
└── home-pay9um.github.io/           # ← cdn 설정이 가리키는 위치
    ├── profile.jpg                  # 사이드바의 프로필 이미지
    ├── commons/                     # 여러 글이 함께 쓰는 이미지
    └── posts/
        ├── 20260826/                # 글 하나에 폴더 하나 (YYYYMMDD)
        ├── 20261001/
        └── 20261002/
            ├── 1/                   # 같은 날 글이 여러 개면 번호로 나눈다
            └── 2/
```
{: .nolineno }

| 장점 | 단점 |
| :-- | :-- |
| 블로그 저장소가 가벼워지고 빌드가 빨라진다 | 이미지를 먼저 push해야 글에서 보인다 |
| 글의 변경 이력과 이미지의 변경 이력이 섞이지 않는다 | 저장소 두 개를 따로 관리해야 한다 |
| 이미지 경로를 짧게 쓸 수 있다 | 같은 이름으로 덮어쓰면 캐시 때문에 한동안 예전 이미지가 보인다 |

---

## 2. 이미지 주소가 만들어지는 규칙 {#url-rule}

본문에 적은 경로는 빌드할 때 다음 규칙으로 최종 주소가 된다.

```text
최종 주소 = cdn + media_subpath + / + 본문에 적은 경로
```
{: .nolineno }

`cdn` 값을 `{cdn}`이라고 줄여서 쓰면 다음과 같다.

| 본문에 적은 경로 | `media_subpath` | 최종 주소 |
| :-- | :-- | :-- |
| `portainer.png` | `/posts/20261002/1` | `{cdn}/posts/20261002/1/portainer.png` |
| `/commons/devices-mockup.png` | 없음 | `{cdn}/commons/devices-mockup.png` |
| `/commons/devices-mockup.png` | `/posts/20261002/1` | `{cdn}/posts/20261002/1/commons/devices-mockup.png` ← 잘못된 주소 |
| `https://example.com/a.png` | 무관 | `https://example.com/a.png` 그대로 |

정리하면 다음과 같다.

1. `https://`로 시작하는 **전체 주소는 변환하지 않고 그대로** 쓴다.
2. 그 밖의 **모든 경로에는 `cdn`이 붙는다.** 블로그 저장소 안의 `/assets/img/...`도 예외가 아니므로, 블로그 저장소에 넣은 이미지는 경로만으로 부를 수 없다.
3. `media_subpath`를 지정하면 그 글의 **모든 경로**에 붙는다. 앞에 `/`를 붙여도 마찬가지이다.
4. 따라서 `media_subpath` 폴더 **밖**의 이미지를 쓰려면 전체 주소로 적는다.

이 규칙은 본문 이미지뿐 아니라 미리보기 이미지(`image.path`), LQIP 파일, 영상·오디오 파일의 경로에도 똑같이 적용된다.

---

## 3. 이미지를 추가하는 절차

### 3.1 미디어 저장소에 올리기

미디어 저장소의 `home-pay9um.github.io/posts/YYYYMMDD/`{: .filepath} 폴더에 이미지를 넣고 push한다.

```bash
# assets 저장소에서 실행한다
git add home-pay9um.github.io/posts/20261117
git commit -m "update home-pay9um.github.io/posts/20261117"
git push origin main
```

### 3.2 글에서 불러오기

Front Matter에 폴더를 지정하고, 본문에서는 **파일 이름만** 적는다.

```markdown
---
media_subpath: '/posts/20261117'
---

![도커 설치 확인 화면](docker-hello-world.png)
```
{: .nolineno }

### 3.3 파일 준비 규칙

- 파일 이름은 **영문 소문자, 숫자, 하이픈**만 쓴다. (`docker-hello-world.png`) 공백과 한글은 쓰지 않는다.
- 내용을 알 수 있는 이름을 붙인다. `1.png`, `캡처.png` 같은 이름은 나중에 구분할 수 없다.
- 스크린샷은 PNG, 사진은 JPG를 쓴다.
- 지나치게 큰 이미지는 줄여서 올린다. 본문 폭이 넓지 않으므로 가로 1,200~1,600px이면 충분하다.

> 파일 이름의 **대소문자가 정확히 일치**해야 한다.
>
> Windows는 `Result.png`와 `result.png`를 같은 파일로 취급하지만 GitHub는 다른 파일로 본다. 대소문자가 틀리면 이미지가 보이지 않는다. 처음부터 소문자로만 짓는 것이 가장 안전하다.
{: .prompt-warning }

> 이미지를 **같은 이름으로 덮어써서 교체**하면 몇 분 동안 예전 이미지가 계속 보인다. GitHub가 파일을 캐시하기 때문이다.
>
> 급하게 바꿔야 한다면 파일 이름을 바꿔서 올리고 글의 경로도 함께 고친다.
{: .prompt-info }

---

## 4. 기본 문법

```markdown
![대체 텍스트](파일이름.png)
```
{: .nolineno }

- 대체 텍스트는 이미지가 뜨지 않을 때 대신 표시되고 화면 낭독기가 읽어 주는 설명이다. 비워 두지 말고 이미지의 내용을 적는다.
- 본문의 이미지는 클릭하면 크게 볼 수 있다.
- 이미지는 화면에 보일 때 불러오며(lazy loading), 불러오는 동안에는 반짝이는 자리 표시가 나타난다.

이 글은 `media_subpath: '/posts/20260826'`을 지정해 두었기 때문에, 아래의 예시들은 모두 파일 이름만 적은 것이다.

> 이 가이드의 예시 이미지는 예전 예제 글의 폴더(`posts/20260826`)에 있던 것을 그대로 쓴다. 새 글을 쓸 때는 글의 날짜에 맞는 폴더를 새로 만든다.
{: .prompt-info }

---

## 5. 캡션

이미지 **바로 다음 줄**에 기울임 글씨를 적으면 이미지 아래의 캡션이 된다. 사이에 빈 줄을 두면 캡션이 되지 않는다.

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png)
_Chirpy 테마의 데스크톱 화면_
```
{: .nolineno }

![Chirpy 테마의 데스크톱 화면](mockup.png)
_Chirpy 테마의 데스크톱 화면_

---

## 6. 크기

### 6.1 원본 크기 알려 주기

이미지의 가로·세로 픽셀 값을 적어 두면 이미지가 뜨기 전에 자리를 미리 잡아 두므로, 불러오는 동안 글이 밀리지 않는다.

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png){: width="972" height="589" }
```
{: .nolineno }

`width`는 `w`로, `height`는 `h`로 줄여 쓸 수 있다.

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" }
```
{: .nolineno }

- 값은 **이미지 파일의 실제 크기**를 적는다. 비율이 실제와 다르면 미리 잡아 둔 자리와 이미지의 크기가 어긋나서, 적지 않은 것과 마찬가지로 글이 밀린다.
- SVG 이미지는 `width`를 반드시 적어야 표시된다.

### 6.2 화면에 보이는 폭 줄이기

본문 폭에 대한 비율로 줄이려면 `w-25`, `w-50`, `w-75` 클래스를 붙인다.

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 }
```
{: .nolineno }

![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 }

---

## 7. 위치

기본은 가운데 정렬이다. 클래스로 위치를 바꿀 수 있다.

> 위치를 지정한 이미지에는 캡션을 붙이지 않는다.
{: .prompt-warning }

### 7.1 왼쪽 정렬

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 .normal }
```
{: .nolineno }

![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 .normal }

### 7.2 왼쪽에 띄우기

이미지가 왼쪽에 놓이고 글이 오른쪽으로 흐른다.

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 .left }
이미지 바로 다음 줄부터 글을 이어서 적는다.
```
{: .nolineno }

![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 .left }
이미지를 왼쪽에 띄우면 글이 이미지의 오른쪽을 따라 흐른다. 이미지 옆에 설명을 나란히 두고 싶을 때 쓴다. 글이 이미지보다 짧으면 다음에 오는 내용이 이미지 옆으로 딸려 올라올 수 있으므로, 이미지 높이를 채울 만큼 충분한 분량의 글과 함께 쓰는 것이 좋다. 화면이 좁은 모바일에서는 이미지와 글이 모두 좁아져 읽기 불편해질 수 있다는 점도 고려한다. 스크린샷처럼 글자가 들어 있는 이미지는 띄우지 말고 기본 정렬로 크게 보여 주는 편이 낫다. 띄우기는 아이콘이나 작은 그림처럼 줄어들어도 알아볼 수 있는 이미지에 어울린다.

### 7.3 오른쪽에 띄우기

```markdown
![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 .right }
이미지 바로 다음 줄부터 글을 이어서 적는다.
```
{: .nolineno }

![Chirpy 테마의 데스크톱 화면](mockup.png){: w="972" h="589" .w-50 .right }
이미지를 오른쪽에 띄우면 글이 이미지의 왼쪽을 따라 흐른다. 왼쪽에 띄울 때와 마찬가지로 충분한 분량의 글과 함께 써야 배치가 어색해지지 않는다. 글이 이미지보다 짧으면 다음에 오는 내용이 이미지 옆으로 딸려 올라올 수 있다. 화면이 좁은 모바일에서는 이미지와 글이 모두 좁아져 읽기 불편해질 수 있다는 점도 고려한다. 스크린샷처럼 글자가 들어 있는 이미지는 띄우지 말고 기본 정렬로 크게 보여 주는 편이 낫다. 띄우기는 아이콘이나 작은 그림처럼 줄어들어도 알아볼 수 있는 이미지에 어울린다.

---

## 8. 다크 모드와 라이트 모드

테마 모드에 따라 다른 이미지를 보여 줄 수 있다. 이미지를 두 장 준비해서 각각 `light`, `dark` 클래스를 붙인다.

```markdown
![라이트 모드의 개발자 도구](devtools-light.png){: .light w="1212" h="668" }
![다크 모드의 개발자 도구](devtools-dark.png){: .dark w="1212" h="668" }
```
{: .nolineno }

![라이트 모드의 개발자 도구](devtools-light.png){: .light w="1212" h="668" }
![다크 모드의 개발자 도구](devtools-dark.png){: .dark w="1212" h="668" }

사이드바 왼쪽 아래의 모드 전환 버튼을 누르면 이미지가 바뀌는 것을 확인할 수 있다.

- 파일 이름은 `이름-light.png`, `이름-dark.png`로 짝을 맞춘다.
- 배경이 흰색인 도표나 다이어그램은 다크 모드에서 눈이 부시므로 두 장을 준비하는 것이 좋다.
- 일반 스크린샷이나 사진은 한 장으로 충분하다.

---

## 9. 그림자, 테두리, 둥근 모서리

```markdown
![라이트 모드의 개발자 도구](devtools-light.png){: .light .shadow .rounded-10 w="1212" h="668" }
![다크 모드의 개발자 도구](devtools-dark.png){: .dark .shadow .rounded-10 w="1212" h="668" }
```
{: .nolineno }

![라이트 모드의 개발자 도구](devtools-light.png){: .light .shadow .rounded-10 w="1212" h="668" }
![다크 모드의 개발자 도구](devtools-dark.png){: .dark .shadow .rounded-10 w="1212" h="668" }

| 클래스 | 효과 | 어울리는 이미지 |
| :-- | :-- | :-- |
| `.shadow` | 이미지 주위에 그림자 | 프로그램 창을 찍은 스크린샷 |
| `.border` | 얇은 테두리 | 배경이 본문 색과 비슷해서 경계가 보이지 않는 이미지 |
| `.rounded-10` | 모서리를 둥글게 | 창 스크린샷, 카드 형태의 이미지 |

---

## 10. 이미지 속성 요약

| 속성 | 예시 | 효과 |
| :-- | :-- | :-- |
| `w`, `h` | `w="972" h="589"` | 원본 크기를 알려 주어 레이아웃 밀림 방지 |
| `.w-25`, `.w-50`, `.w-75` | `.w-50` | 본문 폭의 25·50·75%로 표시 |
| `.normal` | — | 왼쪽 정렬 |
| `.left`, `.right` | — | 왼쪽·오른쪽에 띄우고 글을 옆으로 흐르게 함 |
| `.light`, `.dark` | — | 해당 모드에서만 표시 |
| `.shadow` | — | 그림자 |
| `.border` | — | 테두리 |
| `.rounded-10` | — | 둥근 모서리 |
| `lqip` | `lqip="파일 또는 base64"` | 불러오는 동안 보여 줄 저화질 이미지 |

속성은 이미지 **바로 뒤에 공백 없이** `{: ... }`로 붙이고, 여러 개를 공백으로 구분해 함께 쓴다.

```markdown
![설명](파일이름.png){: w="972" h="589" .w-75 .shadow .rounded-10 }
```
{: .nolineno }

---

## 11. 미리보기 이미지

글 맨 위와 홈 목록의 카드에 표시되는 대표 이미지이다. 글의 링크를 SNS나 메신저에 공유할 때 보이는 썸네일로도 쓰인다. 이 글의 맨 위에 있는 이미지가 그 예이다.

### 11.1 기본 사용법

```yaml
---
media_subpath: '/posts/20261117'
image:
  path: preview.png
  alt: 이미지 설명
---
```
{: .nolineno }

- 크기는 **1200 x 630px**(비율 1.91 : 1)로 만든다. 비율이 다르면 잘려서 표시된다.
- `alt`에 적은 글은 이미지 아래의 캡션으로도 표시된다.
- `media_subpath`가 있으면 `path`에 파일 이름만 적는다.
- 미리보기 이미지는 선택 사항이다. 모든 글에 넣을 필요는 없다.

경로만 필요하면 한 줄로 줄여 쓸 수 있다.

```yaml
---
image: preview.png
---
```
{: .nolineno }

### 11.2 media_subpath 밖의 이미지 쓰기

이 글은 `media_subpath`가 `/posts/20260826`인데 미리보기 이미지는 `commons` 폴더에 있다. [2장의 규칙](#url-rule)에 따라 이런 경우에는 전체 주소를 적어야 한다.

```yaml
---
media_subpath: '/posts/20260826'
image:
  path: https://raw.githubusercontent.com/home-pay9um/assets/refs/heads/main/home-pay9um.github.io/commons/devices-mockup.png
  alt: 여러 기기에서 본 Chirpy 테마 화면
---
```
{: .nolineno }

### 11.3 LQIP

LQIP(Low Quality Image Placeholder)는 원본 이미지가 뜨기 전에 먼저 보여 주는 아주 작은 흐린 이미지이다. 필수는 아니다.

```yaml
---
image:
  path: preview.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAA...
  alt: 이미지 설명
---
```
{: .nolineno }

`lqip`에는 작은 이미지 파일의 경로나 base64로 인코딩한 값을 적는다. 본문 이미지에도 쓸 수 있다.

```markdown
![설명](파일이름.png){: lqip="파일이름-lqip.webp" }
```
{: .nolineno }

---

## 12. 영상과 오디오

영상과 오디오는 Liquid의 `include` 태그로 넣는다.

> 이 장의 태그를 실제로 쓰는 글에는 `render_with_liquid: false`를 **넣으면 안 된다.** Liquid가 태그를 실행해야 플레이어가 만들어지기 때문이다.
>
> 반대로 이 글은 태그를 예시로 보여 주기만 하므로 `render_with_liquid: false`를 넣었다.
{: .prompt-warning }

### 12.1 영상·음악 플랫폼

```liquid
{% include embed/youtube.html id='영상_ID' %}
```
{: .nolineno }

파일 이름(`youtube`) 자리에 플랫폼 이름을, `id`에 주소에서 뽑아낸 ID를 넣는다.

| 플랫폼 | 주소의 형태 | `id`에 넣는 부분 |
| :-- | :-- | :-- |
| `youtube` | `https://www.youtube.com/watch?v=H-B46URT4mg` | `H-B46URT4mg` |
| `twitch` | `https://www.twitch.tv/videos/1634779211` | `1634779211` |
| `bilibili` | `https://www.bilibili.com/video/BV1Q44y1B7Wf` | `BV1Q44y1B7Wf` |
| `spotify` | `https://open.spotify.com/track/3OuMIIFP5TxM8tLXMWYPGV` | `3OuMIIFP5TxM8tLXMWYPGV` |

Spotify는 옵션을 더 받는다.

```liquid
{% include embed/spotify.html id='트랙_ID' compact=1 %}
{% include embed/spotify.html id='트랙_ID' dark=1 %}
```
{: .nolineno }

- `compact=1` : 작은 플레이어로 표시
- `dark=1` : 다크 테마로 고정

### 12.2 영상 파일

```liquid
{% include embed/video.html src='demo.mp4' %}
```
{: .nolineno }

`src`의 경로에도 [2장의 주소 규칙](#url-rule)이 적용된다. 쓸 수 있는 옵션은 다음과 같다.

| 옵션 | 설명 |
| :-- | :-- |
| `poster='poster.png'` | 재생 전에 보여 줄 이미지 |
| `title='설명'` | 영상 아래에 표시되는 캡션 |
| `autoplay=true` | 자동 재생 |
| `loop=true` | 반복 재생 |
| `muted=true` | 음소거 상태로 시작 |
| `types='ogg\|mov'` | 같은 폴더에 있는 다른 형식의 파일. 세로 막대로 구분한다 |

모든 옵션을 쓴 예시이다. 여러 줄로 나누어 적을 수 있다.

```liquid
{%
  include embed/video.html
  src='demo.mp4'
  types='ogg|mov'
  poster='poster.png'
  title='데모 영상'
  autoplay=true
  loop=true
  muted=true
%}
```
{: .nolineno }

> 영상 파일은 용량이 크다. GitHub 저장소는 큰 파일을 올리고 재생하기에 적합하지 않으므로, 긴 영상은 YouTube 같은 플랫폼에 올리고 12.1의 방법으로 넣는다.
{: .prompt-tip }

### 12.3 오디오 파일

```liquid
{% include embed/audio.html src='demo.mp3' %}
```
{: .nolineno }

| 옵션 | 설명 |
| :-- | :-- |
| `title='설명'` | 플레이어 아래에 표시되는 캡션 |
| `types='ogg\|wav\|aac'` | 같은 폴더에 있는 다른 형식의 파일 |

```liquid
{%
  include embed/audio.html
  src='demo.mp3'
  types='ogg|wav|aac'
  title='데모 오디오'
%}
```
{: .nolineno }

---

## 13. 문제가 생겼을 때

| 증상 | 원인 | 해결 |
| :-- | :-- | :-- |
| 이미지가 깨져서 보인다 | 미디어 저장소에 push하지 않았다 | push 후 새로고침 |
| | 파일 이름의 대소문자나 확장자가 다르다 | 저장소의 실제 파일 이름과 비교 |
| | `media_subpath`에 오타가 있다 | 폴더 이름과 비교 |
| 로컬 미리보기에서만 보이지 않는다 | 로컬에서도 미디어 저장소에서 불러온다 | 위와 같다 |
| 이미지를 바꿨는데 예전 것이 보인다 | GitHub와 브라우저의 캐시 | 몇 분 기다리거나 파일 이름을 바꾼다 |
| 캡션이 본문처럼 보인다 | 이미지와 캡션 사이에 빈 줄이 있다 | 빈 줄 제거 |
| 이미지 옆으로 다음 내용이 딸려 올라온다 | `.left`, `.right`로 띄운 이미지 옆의 글이 짧다 | 글을 늘리거나 띄우기를 쓰지 않는다 |
| 영상 태그가 글자 그대로 보인다 | `render_with_liquid: false`가 들어 있다 | 해당 옵션 제거 |

---

## 참고 자료

- [**Chirpy: Writing a New Post**][chirpy-write]
- [**Chirpy: Text and Typography**][chirpy-typo]

---

_`Last updated: 2026-11-17`_{: .right}

[chirpy-write]: https://chirpy.cotes.page/posts/write-a-new-post/
[chirpy-typo]: https://chirpy.cotes.page/posts/text-and-typography/
