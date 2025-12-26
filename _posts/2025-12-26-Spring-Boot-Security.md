---
title: 튜토리얼
description: 앞으로 이렇게 포스트를 작성하면 됩니다.
date: 2025-12-26 21:00:00 +09:00
categories: [
    튜토리얼
]
tags: [
    마크다운
]
---

## 제목

# H1 - 제목
{: .mt-4 .mb-0 }

## H2 - 제목
{: data-toc-skip='' .mt-4 .mb-0 }

### H3 - 제목
{: data-toc-skip='' .mt-4 .mb-0 }

#### H4 - 제목
{: data-toc-skip='' .mt-4 }

## 문장
Quisque egestas convallis ipsum, ut sollicitudin risus tincidunt a. Maecenas interdum malesuada egestas. Duis consectetur porta risus, sit amet vulputate urna facilisis ac. Phasellus semper dui non purus ultrices sodales. Aliquam ante lorem, ornare a feugiat ac, finibus nec mauris. Vivamus ut tristique nisi. Sed vel leo vulputate, efficitur risus non, posuere mi. Nullam tincidunt bibendum rutrum. Proin commodo ornare sapien. Vivamus interdum diam sed sapien blandit, sit amet aliquam risus mattis. Nullam arcu turpis, mollis quis laoreet at, placerat id nibh. Suspendisse venenatis eros eros.

## 리스트

### 순서 목록
1. 순서1
2. 순서2
3. 순서3

### 순서 없는 목록
- 순서1
  - 순서2
    - 순서3

### 할 일 목록
- [ ] 작업
  - [x] Step 1
  - [ ] Step 2
  - [ ] Step 3

### 설명 목록
설명1
: 간단한 설명입니다.

설명2
: 설명2 입니다.

### 블록
> _블록 라인_ 을 보여줍니다.

## 프롬프트
> `TIP` 프롬프트.
{: .prompt-tip }

> `INFO` 프롬프트.
{: .prompt-info }

> `WARNING` 프롬프트.
{: .prompt-warning }

> `DANGER` 프롬프트.
{: .prompt-danger }

## 테이블
<!-- | ID   | PASSWORD  | EMAIL             |
| :--- | :-------- | ----------------: |
| 1    | abcd      | example@gmail.com |
| 2    | 1234      | lee222@naver.com  |
| 3    | xyz       | kakao55@kakao.com | -->

## 링크
<https://file-pay9um.kro.kr>

## 각주
각주1[^footnote], 각주2[^fn-nth-2].

## 인라인 코드
`인라인 코드` 예제.

## 파일 경로
파일 경로 `/src/main/java/example.java`{: .filepath}.

## 코드 블록

### 평범
```text
평범한 코드 블록.
```

### 특정 언어
```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

### 특정 파일명
```sass
@import
  "colors/light-typography",
  "colors/dark-typography";
```
{: file='_sass/jekyll-theme-chirpy.scss'}

## 이미지

### 기본 + 캡션
![Desktop View](/mockup.png){: width="972" height="589" }
_전체화면 및 가운데 정렬(캡션)_

### 왼쪽 정렬
![Desktop View](/mockup.png){: width="972" height="589" .w-75 .normal}

### 좌측 배치
![Desktop View](/mockup.png){: width="972" height="589" .w-50 .left}
사진을 좌측으로 배치합니다.

<div style="clear: both;"></div>

### 우측 배치
![Desktop View](/mockup.png){: width="972" height="589" .w-50 .right}
사진을 우측으로 배치합니다.

<div style="clear: both;"></div>

### Dark/Light 모드 & 그림자
아래 이미지는 테마 설정에 따라 다크 모드와 라이트 모드가 전환되는 것을 보여줍니다. 그림자가 있는 것을 확인 할 수 있습니다.

![light mode only](/devtools-light.png){: .light .w-75 .shadow .rounded-10 w='1212' h='668' }
![dark mode only](/devtools-dark.png){: .dark .w-75 .shadow .rounded-10 w='1212' h='668' }

## 비디오
{% include embed/youtube.html id='Balreaj8Yqs' %}

## 역방향 각주
[^footnote]: 각주1
[^fn-nth-2]: 각주2
