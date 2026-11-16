---
title: Chirpy 블로그 가이드 5 - 사이트 설정과 커스터마이징
description: >-
  _config.yml의 주요 설정과 초기 설정 체크리스트, 사이드바 탭·연락처·프로필 이미지·파비콘·댓글을 바꾸는 방법, 테마의 스타일과 스크립트를 고칠 때의 원칙을 정리합니다.
date: 2026-11-17 00:14:00 +0900
categories: [블로그, 가이드]
tags: [
  chirpy,
  jekyll,
  configuration,
  guide,
]
---

> 글이 아니라 **블로그 자체**를 바꾸고 싶을 때 읽는 글이다. 어느 파일을 고치면 무엇이 바뀌는지를 정리한다.

블로그의 전체 구조는 [1편](/posts/chirpy-guide-1-project-structure/)에서 다뤘다. 이 글은 그중에서 설정과 관련된 파일들을 자세히 살펴본다.

---

## 1. 무엇을 바꾸려면 어디를 고치는가

| 바꾸려는 것 | 고칠 곳 |
| :-- | :-- |
| 블로그 제목, 부제, 설명, 주소 | `_config.yml` |
| 화면에 표시되는 언어 | `_config.yml`의 `lang` |
| 프로필 이미지 | 미디어 저장소의 `profile.jpg` |
| 파비콘 | `assets/img/favicons/` |
| 사이드바의 탭 | `_tabs/` |
| 정보(About) 페이지의 내용 | `_tabs/about.md` |
| 사이드바 아래의 연락처 아이콘 | `_data/contact.yml` |
| 글 아래의 공유 버튼 | `_data/share.yml` |
| 댓글 | `_config.yml`의 `comments` |
| 방문 통계 | `_config.yml`의 `analytics` |
| 화면에 표시되는 문구 | `_data/locales/` |
| 색상, 글꼴, 간격 | `_sass/` 또는 `assets/css/jekyll-theme-chirpy.scss` |
| 페이지의 구성과 동작 | `_layouts/`, `_includes/`, `_javascript/` |

> `_config.yml`을 고친 뒤에는 로컬 서버를 **재시작**해야 반영된다. 다른 파일과 달리 설정 파일은 자동으로 다시 읽지 않는다.
{: .prompt-tip }

---

## 2. _config.yml 주요 설정

### 2.1 사이트 기본 정보

| 키 | 설명 |
| :-- | :-- |
| `lang` | 화면 문구의 언어. `_data/locales/`의 파일 이름과 같아야 한다. 한국어는 `ko-KR` |
| `timezone` | 시간대. `Asia/Seoul` |
| `title` | 블로그 제목. 사이드바와 브라우저 탭에 표시된다 |
| `tagline` | 제목 아래의 부제 |
| `description` | 검색엔진과 RSS에 쓰이는 블로그 설명 |
| `url` | 사이트 주소. 끝에 `/`를 붙이지 않는다 |
| `baseurl` | 하위 경로로 서비스할 때만 쓴다. 이 블로그는 빈 값이다 |

### 2.2 작성자와 소셜 정보

| 키 | 설명 |
| :-- | :-- |
| `github.username` | 사이드바의 GitHub 아이콘이 연결되는 계정 |
| `twitter.username` | 사이드바의 X(Twitter) 아이콘이 연결되는 계정 |
| `social.name` | 글의 작성자 이름, 하단의 저작권 표시에 쓰인다 |
| `social.email` | 사이드바의 메일 아이콘이 연결되는 주소 |
| `social.links` | 프로필 주소 목록. **첫 번째 주소**가 글의 작성자 이름과 하단 저작권 표시에 링크로 걸린다 |

### 2.3 화면과 미디어

| 키 | 설명 |
| :-- | :-- |
| `theme_mode` | 비워 두면 시스템 설정을 따르고 전환 버튼이 표시된다. `light`나 `dark`로 고정할 수도 있다 |
| `cdn` | 미디어 저장소의 주소. 모든 이미지 경로 앞에 붙는다 ([4편](/posts/chirpy-guide-4-images-and-media/)) |
| `avatar` | 사이드바의 프로필 이미지 경로. `cdn`이 앞에 붙는다 |
| `social_preview_image` | 미리보기 이미지가 없는 페이지를 공유할 때 쓰이는 기본 썸네일 |
| `toc` | 글의 목차를 전체적으로 켜고 끈다 |
| `paginate` | 홈 목록의 한 페이지에 표시할 글의 개수 |

### 2.4 부가 기능

| 키 | 설명 |
| :-- | :-- |
| `comments.provider` | 댓글 시스템. `disqus`, `utterances`, `giscus` 중 하나. 비워 두면 댓글이 꺼진다 |
| `analytics` | 방문 통계. Google Analytics, GoatCounter, Umami, Matomo, Cloudflare, Fathom을 지원한다 |
| `pageviews.provider` | 글의 조회수 표시. `goatcounter`만 지원한다 |
| `webmaster_verifications` | 검색엔진의 사이트 소유 확인 코드 |
| `pwa.enabled` | 사이트를 앱처럼 설치할 수 있게 한다 |
| `pwa.cache.enabled` | 오프라인에서도 볼 수 있도록 방문한 페이지를 캐시한다 |
| `actions.edit_post` | 글 아래에 "이 글 편집" 링크를 표시한다 |

> `_config.yml`에서 `# The following options are not recommended to be modified` 주석 **아래쪽**은 건드리지 않는다.
>
> 특히 `defaults`의 `permalink: /posts/:title/`을 바꾸면 모든 글의 주소가 바뀐다.
{: .prompt-danger }

---

## 3. 초기 설정 체크리스트

포크 후에 해야 하는 설정을 모았다. 체크된 항목은 이미 끝난 것이고, 나머지는 필요할 때 진행한다.

- [x] 블로그 제목, 부제, 설명 입력
- [x] `url`, `timezone` 설정
- [x] `github.username`, `social.name`, `social.email` 입력
- [x] 미디어 저장소 연결 (`cdn`, `avatar`)
- [x] 파비콘 교체
- [x] `lang`을 `ko-KR`로 변경 (화면 문구를 한국어로 표시)
- [x] `social.links`를 실제 프로필 주소로 교체
- [ ] `_tabs/about.md`에 소개 글 작성
- [ ] `_data/authors.yml`의 예제 작성자 정리
- [ ] 댓글 시스템 연결
- [ ] 방문 통계 연결
- [ ] 검색엔진 등록 (`webmaster_verifications`)

---

## 4. 사이드바 탭

사이드바의 탭은 `_tabs/`{: .filepath} 폴더의 파일 하나하나에 대응한다.

| 파일 | 순서 | 내용 |
| :-- | :--: | :-- |
| `categories.md` | 1 | 카테고리 목록 (자동 생성) |
| `tags.md` | 2 | 태그 목록 (자동 생성) |
| `archives.md` | 3 | 연도별 글 목록 (자동 생성) |
| `about.md` | 4 | 소개 페이지 (직접 작성) |

### 4.1 정보(About) 페이지 작성

`_tabs/about.md`{: .filepath}의 Front Matter 아래에 마크다운으로 내용을 적는다. 글과 같은 문법을 쓸 수 있다.

```markdown
---
icon: fas fa-info-circle
order: 4
---

안녕하세요. 이 블로그는 ...
```
{: file="_tabs/about.md" }

### 4.2 탭 추가

`_tabs/`{: .filepath}에 파일을 하나 만들면 탭이 추가된다. 파일 이름이 주소가 된다.

```markdown
---
title: Projects
icon: fas fa-code
order: 5
---

진행한 프로젝트를 소개한다.
```
{: file="_tabs/projects.md" }

| 항목 | 설명 |
| :-- | :-- |
| 파일 이름 | 주소가 된다. `projects.md` → `/projects/` |
| `title` | 탭에 표시되는 이름 |
| `icon` | [Font Awesome][fontawesome]의 아이콘 클래스 |
| `order` | 탭의 순서. 숫자가 작을수록 위에 놓인다 |

---

## 5. 연락처 아이콘과 공유 버튼

### 5.1 연락처 아이콘

사이드바 맨 아래의 아이콘들은 `_data/contact.yml`{: .filepath}에서 관리한다.

```yaml
- type: github
  icon: "fab fa-github"

- type: email
  icon: "fas fa-envelope"
  noblank: true

- type: linkedin
  icon: "fab fa-linkedin"
  url: "https://www.linkedin.com/in/사용자이름"
```
{: file="_data/contact.yml" }

- `github`, `twitter`, `email`은 `_config.yml`의 값을 읽어 주소를 자동으로 만든다. `_config.yml`에 해당 값이 비어 있으면 아이콘이 표시되지 않는다.
- `rss`는 블로그의 피드 주소(`/feed.xml`)로 자동 연결된다.
- 그 밖의 종류는 `url`을 직접 적는다.
- 쓰지 않는 항목은 지우거나 `#`으로 주석 처리한다.
- `noblank: true`는 링크를 새 탭이 아니라 현재 탭에서 연다.

### 5.2 공유 버튼

글 아래의 공유 버튼은 `_data/share.yml`{: .filepath}에서 관리한다. 쓰지 않는 플랫폼은 지우고, 주석으로 준비되어 있는 플랫폼은 주석을 풀어서 추가한다.

---

## 6. 프로필 이미지와 파비콘

### 6.1 프로필 이미지

사이드바의 프로필 이미지는 미디어 저장소의 `home-pay9um.github.io/profile.jpg`{: .filepath}이다. 같은 이름으로 덮어쓰고 push하면 바뀐다.

- 정사각형 이미지를 쓴다. 원형으로 잘려서 표시된다.
- 파일 이름을 바꾸려면 `_config.yml`의 `avatar`도 함께 고친다.
- 덮어쓴 직후에는 캐시 때문에 몇 분 동안 예전 이미지가 보일 수 있다.

### 6.2 파비콘

브라우저 탭에 표시되는 아이콘이다. `assets/img/favicons/`{: .filepath}에 들어 있다.

1. 512 x 512px 이상의 정사각형 이미지(PNG, JPG, SVG)를 준비한다.
2. [Real Favicon Generator][favicon-generator]에 이미지를 올리고 기본 설정 그대로 생성한다.
3. 내려받은 압축 파일을 풀고, 그 안의 `site.webmanifest`{: .filepath}를 **삭제한다.**
4. 남은 이미지 파일(`.png`, `.ico`, `.svg`)을 `assets/img/favicons/`{: .filepath}에 덮어쓴다.

> 3번을 빠뜨리지 않는다.
>
> 이 블로그의 `site.webmanifest`는 `_config.yml`의 값을 읽어 내용을 채우는 템플릿이다. 생성기가 만든 파일로 덮어쓰면 PWA 설정이 깨진다.
{: .prompt-warning }

---

## 7. 댓글

댓글은 꺼져 있는 상태이다. GitHub 계정으로 댓글을 다는 [giscus][giscus]를 기준으로 연결 방법을 정리한다.

1. 블로그 저장소의 _Settings_ 에서 **Discussions** 기능을 켠다.
2. [giscus 앱][giscus-app]을 블로그 저장소에 설치한다.
3. [giscus.app][giscus]에서 저장소 이름을 입력하고 댓글이 쌓일 Discussions 카테고리를 고른다.
4. 화면에 표시되는 `data-repo-id`와 `data-category-id` 값을 `_config.yml`에 옮겨 적는다.

```yaml
comments:
  provider: giscus
  giscus:
    repo: home-pay9um/home-pay9um.github.io
    repo_id: 여기에_data-repo-id_값
    category: Announcements
    category_id: 여기에_data-category-id_값
    mapping: pathname
    lang: ko
```
{: file="_config.yml" }

- 연결하면 모든 글에 댓글 영역이 생긴다.
- 특정 글에서만 끄려면 그 글의 Front Matter에 `comments: false`를 넣는다.
- 댓글은 글의 주소(`pathname`)를 기준으로 연결된다. 글의 슬러그를 바꾸면 기존 댓글과의 연결이 끊어진다.

---

## 8. 스타일과 스크립트 고치기

포크 방식이므로 테마의 모든 파일을 직접 고칠 수 있다. 다만 **고친 파일이 많을수록 원본 업데이트를 병합할 때 충돌이 늘어난다.** 아래의 순서대로, 가능한 한 위쪽 방법으로 해결한다.

### 8.1 스타일을 조금 덧붙일 때

`assets/css/jekyll-theme-chirpy.scss`{: .filepath}의 맨 아래에 추가한다. 원본 파일의 본문을 건드리지 않고 덧붙이는 방식이라 충돌이 가장 적다.

```scss
/* append your custom style below */

.post-desc {
  font-size: 1.05rem;
}
```
{: file="assets/css/jekyll-theme-chirpy.scss" }

### 8.2 색상이나 레이아웃을 본격적으로 바꿀 때

`_sass/`{: .filepath}의 해당 파일을 직접 고친다.

| 경로 | 내용 |
| :-- | :-- |
| `_sass/themes/_light.scss`, `_dark.scss` | 라이트·다크 모드의 색상 변수 |
| `_sass/abstracts/_variables.scss` | 글꼴, 크기, 간격 같은 공통 변수 |
| `_sass/base/_typography.scss` | 본문의 글자 스타일 |
| `_sass/base/_syntax.scss` | 코드 블록의 스타일 |
| `_sass/layout/` | 사이드바, 상단 바, 하단 영역 |
| `_sass/pages/` | 홈, 글, 카테고리 등 페이지별 스타일 |

SCSS는 Jekyll이 빌드할 때 함께 변환하므로 저장만 하면 로컬 미리보기에 반영된다.

### 8.3 동작을 바꿀 때

`_javascript/`{: .filepath}의 파일을 고친 뒤에는 **반드시 다시 빌드**해야 한다. 브라우저가 실제로 실행하는 것은 원본이 아니라 `assets/js/dist/`{: .filepath}의 빌드 결과물이기 때문이다.

```bash
npm install     # 최초 1회
npm run build   # assets/js/dist/*.min.js 재생성
```

원본 파일과 빌드 결과물을 **함께 커밋**한다.

### 8.4 화면의 문구를 바꿀 때

`_data/locales/`{: .filepath}에서 `_config.yml`의 `lang`과 같은 이름의 파일을 고친다. 예를 들어 `lang: ko-KR`일 때 목차의 제목을 바꾸려면 다음과 같이 한다.

```yaml
panel:
  lastmod: 최근 업데이트
  trending_tags: 인기 태그
  toc: 목차
```
{: file="_data/locales/ko-KR.yml" }

### 8.5 고칠 때의 원칙

- 설정(`_config.yml`, `_data/`, `_tabs/`)으로 해결되는지 먼저 확인한다.
- 테마 파일을 고칠 때는 **글과 섞지 말고 별도의 커밋**으로 남긴다. 나중에 무엇을 왜 고쳤는지 추적할 수 있어야 원본을 병합할 때 판단할 수 있다.
- 커밋 메시지의 종류를 구분한다. 기능 추가는 `feat`, 버그 수정은 `fix`, 모양만 바꾼 것은 `style`이다. ([2편](/posts/chirpy-guide-2-writing-posts/) 참고)
- 고친 뒤에는 `bash tools/test.sh`로 빌드와 링크 검사를 통과하는지 확인한다.

---

## 9. 기본으로 제공되는 기능

따로 설정하지 않아도 동작하는 기능들이다.

| 기능 | 설명 |
| :-- | :-- |
| 검색 | 상단의 검색창에서 글의 제목과 본문을 검색한다 |
| RSS | `/feed.xml`에서 피드를 제공한다 |
| 사이트맵 | `/sitemap.xml`이 자동으로 만들어진다 |
| SEO 메타 태그 | 글의 `title`, `description`, `image`로 검색엔진과 SNS용 정보를 만든다 |
| 다크 모드 | 사이드바 아래의 버튼으로 전환한다 |
| 목차 | 글의 `h2`~`h4`로 자동 생성한다 |
| 읽는 시간 | 글의 분량으로 계산해 표시한다 |
| 수정일 | Git 이력으로 계산해 표시한다 |
| 관련 글 | 태그와 카테고리가 겹치는 글 3개를 추천한다 |
| 인기 태그 | 글이 많은 태그 10개를 우측에 표시한다 |
| 최근 업데이트 | 최근에 쓰거나 고친 글 5개를 우측에 표시한다 |
| PWA | 사이트를 앱처럼 설치하고 오프라인에서 본다 |

---

## 참고 자료

- [**Chirpy: Getting Started**][chirpy-start]
- [**Chirpy: Customize the Favicon**][chirpy-favicon]
- [**jekyll-seo-tag 사용법**][seo-tag]
- [**giscus**][giscus]

---

_`Last updated: 2026-11-17`_{: .right}

[chirpy-start]: https://chirpy.cotes.page/posts/getting-started/
[chirpy-favicon]: https://chirpy.cotes.page/posts/customize-the-favicon/
[seo-tag]: https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/usage.md
[fontawesome]: https://fontawesome.com/search?o=r&m=free
[favicon-generator]: https://realfavicongenerator.net/
[giscus]: https://giscus.app/ko
[giscus-app]: https://github.com/apps/giscus
