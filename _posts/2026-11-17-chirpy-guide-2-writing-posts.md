---
title: Chirpy 블로그 가이드 2 - 포스트 작성 규칙과 Front Matter
description: >-
  새 글을 쓸 때 따르는 파일 이름 규칙, Front Matter 템플릿과 필드별 의미, 카테고리·태그 운영 기준, 발행 전 체크리스트와 커밋 방법을 정리합니다.
date: 2026-11-17 00:11:00 +0900
categories: [블로그, 가이드]
tags: [
  chirpy,
  jekyll,
  front matter,
  guide,
]
---

> 새 글을 쓸 때는 이 글의 템플릿을 복사해서 시작하고, 발행 전에 체크리스트를 한 번 훑는다.

이 글은 가이드 시리즈의 두 번째 글이다. 블로그의 구조와 배포 흐름은 [1편](/posts/chirpy-guide-1-project-structure/)에서 다뤘다.

---

## 1. 작성 흐름 한눈에 보기

1. 이미지가 있다면 미디어 저장소에 먼저 올리고 push한다. ([4편](/posts/chirpy-guide-4-images-and-media/))
2. `_posts/`에 `YYYY-MM-DD-slug.md` 파일을 만든다.
3. 아래의 템플릿을 붙여 넣고 Front Matter를 채운다.
4. 본문을 작성한다. ([3편](/posts/chirpy-guide-3-markdown-syntax/))
5. `bundle exec jekyll serve`로 미리보기를 확인한다.
6. 다른 글로 가는 링크를 넣었다면 `bash tools/test.sh`로 검사한다.
7. 커밋하고 push한다. 배포는 자동으로 진행된다.

---

## 2. 파일 이름과 위치

글 파일은 `_posts/`{: .filepath} 바로 아래에 다음 형식으로 만든다.

```text
YYYY-MM-DD-slug.md
```
{: .nolineno }

예를 들어 `2026-11-17-linux-docker-install.md`{: .filepath}와 같다. 여기서 날짜 뒤의 `linux-docker-install` 부분을 **슬러그(slug)** 라고 한다.

### 2.1 슬러그 규칙

- **영문 소문자, 숫자, 하이픈(`-`)만** 사용한다. 한글, 공백, 대문자, 밑줄은 쓰지 않는다.
- 글의 내용을 알 수 있는 단어 2~5개로 짓는다.
- 같은 주제의 연재는 공통 접두어를 붙인다. (예: `chirpy-guide-1-...`, `linux-docker-...`)

### 2.2 슬러그가 곧 URL이다

이 블로그의 글 주소는 `/posts/슬러그/` 형식이다. **날짜는 주소에 들어가지 않는다.**

| 파일 이름 | 글 주소 |
| :-- | :-- |
| `2026-11-17-linux-docker-install.md` | `/posts/linux-docker-install/` |
| `2026-12-01-linux-docker-install.md` | `/posts/linux-docker-install/` ← 위와 충돌 |

여기서 세 가지 규칙이 나온다.

- 슬러그는 **블로그 전체에서 유일**해야 한다. 날짜가 달라도 슬러그가 같으면 같은 주소가 되어 한쪽이 덮어써진다.
- **발행한 글의 슬러그는 바꾸지 않는다.** 주소가 바뀌어 외부에서 걸린 링크와 검색 결과가 모두 깨진다. 제목(`title`)은 주소와 무관하므로 언제든 바꿔도 된다.
- 파일 이름의 날짜와 Front Matter의 `date`는 같은 날짜로 맞춘다.

---

## 3. Front Matter 템플릿

Front Matter는 파일 맨 위의 `---`와 `---` 사이에 적는 YAML 형식의 설정이다.

### 3.1 기본형

모든 글에 공통으로 쓰는 형태이다. 그대로 복사해서 값만 바꾼다.

```yaml
---
title: 글 제목
description: >-
  글의 핵심을 한두 문장으로 요약합니다. 홈 목록과 글 제목 아래, 검색 결과에 그대로 노출됩니다.
date: 2026-11-17 18:00:00 +0900
categories: [상위 카테고리, 하위 카테고리]
tags: [
  tag one,
  tag two,
]
---
```

### 3.2 필요할 때만 추가하는 옵션

```yaml
media_subpath: '/posts/20261117'   # 이 글의 이미지 폴더
math: true                         # 수식을 쓸 때
mermaid: true                      # Mermaid 다이어그램을 쓸 때
pin: true                          # 홈 상단에 고정할 때
image:                             # 미리보기(대표) 이미지를 넣을 때
  path: preview.png
  alt: 이미지 설명
```

> `layout`은 적지 않는다. `_config.yml`에서 `_posts/`의 모든 글에 `layout: post`가 기본값으로 지정되어 있다.
{: .prompt-tip }

---

## 4. 필드 레퍼런스

| 필드 | 필요 | 기본값 | 설명 |
| :-- | :--: | :-- | :-- |
| `title` | 필수 | — | 글 제목. 페이지의 `h1`이 된다 |
| `date` | 필수 | 파일 이름의 날짜 | 발행 시각. 시간대(`+0900`)까지 적는다 |
| `description` | 권장 | 본문 앞 200자 | 글 요약문 |
| `categories` | 권장 | 없음 | 최대 2단계의 분류 |
| `tags` | 권장 | 없음 | 키워드. 개수 제한 없음 |
| `media_subpath` | 선택 | 없음 | 이미지 경로 앞에 붙는 폴더 |
| `image` | 선택 | 없음 | 미리보기 이미지 (`path`, `alt`, `lqip`) |
| `math` | 선택 | `false` | 수식 렌더링(MathJax) 사용 |
| `mermaid` | 선택 | `false` | Mermaid 다이어그램 사용 |
| `pin` | 선택 | `false` | 홈 상단 고정 |
| `toc` | 선택 | `true` | 우측 목차 표시 |
| `comments` | 선택 | `true` | 댓글 영역 표시 |
| `author`, `authors` | 선택 | 사이트 소유자 | `_data/authors.yml`에 등록한 작성자 ID |
| `render_with_liquid` | 선택 | `true` | `false`면 본문의 Liquid 태그를 해석하지 않는다 |
| `hidden` | 선택 | `false` | `true`면 홈 목록에서 제외한다 |
| `published` | 선택 | `true` | `false`면 빌드에서 완전히 제외한다 |

### 4.1 title

대부분 따옴표 없이 쓰면 된다. 다만 YAML 문법과 겹치는 경우에는 큰따옴표로 감싼다.

```yaml
title: Linux [Docker] Portainer 설치       # 중간의 대괄호는 괜찮다
title: "[Linux] ufw 설정"                  # 대괄호로 시작하면 따옴표 필요
title: "Docker: 설치부터 실행까지"          # 콜론 뒤에 공백이 오면 따옴표 필요
title: "#1 시작하기"                       # #으로 시작하면 따옴표 필요
```
{: .nolineno }

### 4.2 description

- `>-`는 여러 줄을 한 줄로 이어 붙이는 YAML 표기이다. 이 방식으로 쓰면 콜론이나 따옴표가 들어가도 문제가 없다.
- 글 제목 아래, 홈 목록 카드, 관련 글 카드, RSS, 검색엔진과 SNS 미리보기에 노출된다.
- 생략하면 본문 앞부분 200자가 자동으로 쓰인다. 인용문이나 코드로 시작하는 글은 어색해지므로 **직접 쓰는 것을 원칙으로 한다.**
- 한두 문장으로 쓰고, 본문과 달리 "~합니다"체로 통일한다.

### 4.3 date

```yaml
date: 2026-11-17 18:00:00 +0900
```
{: .nolineno }

- `+0900`은 한국 표준시를 뜻한다. 빠뜨리지 않는다.
- 홈 목록은 `date`의 최신순으로 정렬된다. 같은 날 여러 글을 올릴 때는 시각으로 순서를 조절한다.
- 수정일은 따로 적지 않는다. `_plugins/posts-lastmod-hook.rb`가 Git 이력을 읽어, **커밋이 2개 이상인 글**에 수정일을 자동으로 붙인다. 글 상단의 수정일과 우측의 최근 업데이트 목록이 이 값으로 만들어진다.

> `date`가 빌드 시점보다 미래이면 그 글은 **빌드에서 제외된다.**
>
> 시간이 지난다고 저절로 발행되지도 않는다. 그 시각 이후에 배포가 다시 실행되어야 글이 나타난다. 예약 발행 기능이 아니므로 `date`는 항상 현재보다 과거로 적는다.
{: .prompt-warning }

### 4.4 categories

```yaml
categories: [Linux, Docker]     # [상위, 하위]
categories: [Linux]             # 상위만 써도 된다
```
{: .nolineno }

- 최대 2개까지 쓰며, **순서가 곧 계층**이다. 첫 번째가 상위, 두 번째가 하위 카테고리이다.
- 카테고리 탭에서 상위 → 하위의 트리로 표시된다.
- 하나의 이름은 한 위치에서만 쓴다. 어떤 글에서는 상위로, 다른 글에서는 하위로 쓰면 트리가 꼬인다.
- 하위 카테고리의 이름은 가급적 블로그 전체에서 겹치지 않게 한다. 카테고리 페이지는 상위와 무관하게 이름만으로 글을 모으기 때문에, 서로 다른 상위 아래에 같은 이름의 하위가 있으면 한 페이지에 섞인다.
- **표기를 정확히 통일한다.** `Linux`와 `linux`는 서로 다른 카테고리로 집계되지만 주소는 둘 다 `/categories/linux/`가 되어 충돌한다.
- 새 카테고리를 만들기 전에 카테고리 탭에서 기존 이름을 먼저 확인하고, 있으면 그대로 복사해서 쓴다.

### 4.5 tags

```yaml
tags: [
  data structure,
  linked list,
  python,
]
```
{: .nolineno }

- **영문 소문자**로 쓴다. 여러 단어는 공백으로 구분한다. (`data structure` → `/tags/data-structure/`)
- 한 줄에 하나씩 적고 끝에 쉼표를 붙인다. 나중에 태그를 추가하거나 뺄 때 변경 내역이 깔끔하게 남는다.
- 넓은 주제 1~2개와 구체적인 주제 2~4개 정도로 구성한다.
- 같은 개념에는 **항상 같은 태그**를 쓴다. 태그는 글 하단의 관련 글 추천에 쓰이기 때문이다.

| 일치 항목 | 점수 |
| :-- | :--: |
| 같은 태그 1개 | 1점 |
| 같은 카테고리 1개 | 0.5점 |

점수가 높은 글 3개가 관련 글로 추천된다. `linked list`와 `linked-list`처럼 표기가 흔들리면 서로 다른 태그가 되어 추천에서 빠진다.

> 카테고리는 글이 들어갈 **서랍**이고, 태그는 글에 붙이는 **키워드**이다.
>
> 글 하나는 서랍 한 곳에만 들어가지만 키워드는 여러 개 붙을 수 있다.
{: .prompt-info }

### 4.6 media_subpath, image

이미지와 관련된 필드이다. 자세한 규칙은 [4편](/posts/chirpy-guide-4-images-and-media/)에서 다룬다.

```yaml
media_subpath: '/posts/20261117'
image:
  path: preview.png
  alt: 이미지 설명
```
{: .nolineno }

- `media_subpath`는 미디어 저장소의 `posts/YYYYMMDD` 폴더를 가리킨다. 같은 날 글이 여러 개면 `/posts/20261117/1`, `/posts/20261117/2`처럼 나눈다.
- 이미지가 하나도 없는 글에는 적지 않는다.

### 4.7 math, mermaid

수식이나 다이어그램을 쓰는 글에만 `true`로 적는다. 관련 스크립트는 이 값이 있는 글에서만 로드되므로, 쓰지 않는 글에는 넣지 않는다. 문법은 [3편](/posts/chirpy-guide-3-markdown-syntax/)에서 다룬다.

### 4.8 pin, hidden, toc, comments

| 필드 | 동작 |
| :-- | :-- |
| `pin: true` | 홈 목록 맨 위에 고정한다. 고정된 글끼리는 최신순으로 정렬된다 |
| `hidden: true` | 홈 목록에서만 뺀다. 주소로 직접 접근하거나 카테고리·태그·아카이브를 통해서는 볼 수 있다 |
| `toc: false` | 해당 글의 목차를 끈다. 목차는 본문에 `h2`나 `h3`가 있을 때만 표시된다 |
| `comments: false` | 해당 글의 댓글을 끈다. 사이트에 댓글 시스템이 연결되어 있을 때만 의미가 있다 |

### 4.9 author, authors

적지 않으면 `_config.yml`의 `social.name`이 작성자로 표시된다. 혼자 쓰는 블로그이므로 평소에는 필요 없다.

다른 사람의 글을 싣거나 공동 작성할 때는 `_data/authors.yml`에 작성자를 등록한 뒤 ID를 적는다.

```yaml
author: author_id                  # 한 명
authors: [author_a, author_b]      # 여러 명
```
{: .nolineno }

### 4.10 render_with_liquid

Jekyll은 본문에 있는 Liquid 태그(중괄호와 퍼센트 기호로 이루어진 템플릿 문법)를 먼저 실행한 뒤 마크다운을 변환한다. 그래서 Liquid 문법을 **설명하려고 적은 코드**까지 실행되어 버리는 문제가 생긴다.

`render_with_liquid: false`를 넣으면 그 글의 본문에서는 Liquid를 해석하지 않는다. Liquid나 Jekyll 템플릿 코드를 예제로 보여 주는 글에만 넣는다. 반대로 영상 임베드처럼 Liquid 태그가 실제로 **실행되어야 하는** 글에는 넣으면 안 된다.

---

## 5. 본문 구성 규칙

### 5.1 제목 계층

- 본문의 제목은 `##`(h2)부터 시작한다. `#`(h1)은 글 제목이 차지하므로 본문에서 쓰지 않는다.
- `##` → `###` → `####` 순서로 내려가며 단계를 건너뛰지 않는다.
- 목차에는 `h2`~`h4`가 표시된다.
- 섹션 번호(`## 1. ...`)는 붙여도 되고 안 붙여도 되지만 한 글 안에서는 통일한다.

### 5.2 문체와 구성

- 본문은 "~한다", "~이다"의 평서형으로 쓴다. (`description`만 "~합니다"체)
- 첫 부분에서 이 글이 무엇을 다루는지 밝힌다. 핵심 한 문장을 인용구(`>`)로 먼저 보여 주는 것도 좋다.
- 큰 섹션(`##`) 사이에는 수평선(`---`)을 넣어 구분한다.
- 코드 블록에는 **반드시 언어를 지정**한다.
- 출처가 있는 글은 마지막에 `## 참고 자료` 섹션을 두고 링크를 모은다.

### 5.3 전체 골격

````markdown
---
title: 글 제목
description: >-
  글의 핵심을 한두 문장으로 요약합니다.
date: 2026-11-17 18:00:00 +0900
categories: [상위 카테고리, 하위 카테고리]
tags: [
  tag one,
  tag two,
]
---

> 이 글의 핵심을 한 문장으로 적는다.

도입 문단. 무엇을, 왜 다루는지 적는다.

---

## 1. 첫 번째 주제

본문.

### 1.1 세부 항목

```bash
echo "코드 블록에는 언어를 지정한다"
```

---

## 2. 두 번째 주제

본문.

---

## 참고 자료

- [**문서 제목**][ref-1]

---

_`Last updated: 2026-11-17`_{: .right}

[ref-1]: https://example.com
````

> 맨 아래의 `Last updated` 줄은 선택 사항이다.
>
> 수정일은 테마가 글 상단에 자동으로 표시해 주므로 없어도 된다. 직접 적는다면 글을 고칠 때마다 함께 갱신해야 한다.
{: .prompt-info }

---

## 6. 초안과 비공개 처리

아직 공개하고 싶지 않은 글은 다음 방법으로 관리한다.

| 방법 | 사용법 | 로컬에서 보기 |
| :-- | :-- | :-- |
| 초안 폴더 | `_drafts/`{: .filepath}에 날짜 없이 `slug.md`로 저장 | `bundle exec jekyll serve --drafts` |
| 비공개 표시 | Front Matter에 `published: false` | `bundle exec jekyll serve --unpublished` |

두 방법 모두 배포되는 사이트에는 포함되지 않는다.

> 사이트에 나타나지 않을 뿐이고, push하면 **파일 자체는 공개 저장소에서 누구나 볼 수 있다.**
>
> 공개되면 안 되는 내용은 초안이라도 push하지 않는다.
{: .prompt-danger }

---

## 7. 발행 전 체크리스트

- [ ] 파일 이름이 `YYYY-MM-DD-slug.md` 형식이고, 슬러그가 기존 글과 겹치지 않는다
- [ ] `date`가 현재 시각보다 과거이고 `+0900`이 붙어 있다
- [ ] `description`을 직접 작성했다
- [ ] `categories`와 `tags`의 표기가 기존 글과 일치한다 (대소문자, 띄어쓰기)
- [ ] 이미지를 미디어 저장소에 push했고, 로컬 미리보기에서 모두 보인다
- [ ] 수식을 썼다면 `math: true`, 다이어그램을 썼다면 `mermaid: true`를 넣었다
- [ ] 모든 코드 블록에 언어를 지정했다
- [ ] 제목이 `##`부터 시작하고, 목차가 의도한 대로 보인다
- [ ] 다른 글로 가는 링크가 있다면 `bash tools/test.sh`를 통과했다

---

## 8. 커밋과 발행

```bash
git add _posts/2026-11-17-linux-docker-install.md
git commit -m "docs: add post linux-docker-install"
git push origin master
```

push하면 GitHub Actions가 빌드와 배포를 진행한다. 저장소의 _Actions_ 탭에서 `Build and Deploy`가 성공했는지 확인한 뒤 사이트를 확인한다.

### 8.1 커밋 메시지 형식

이 저장소에는 커밋 메시지를 검사하는 훅(`.husky/commit-msg`{: .filepath}, commitlint)이 걸려 있다. **[Conventional Commits][conventional-commits] 형식이 아니면 커밋 자체가 거부된다.**

```text
<type>: <subject>
```
{: .nolineno }

| 상황 | type | 예시 |
| :-- | :-- | :-- |
| 새 글 작성 | `docs` | `docs: add post linux-docker-install` |
| 글 수정 | `docs` | `docs: update post linux-docker-install` |
| 설정 변경 | `chore` | `chore: update site config` |
| 테마 기능 추가 | `feat` | `feat: add custom sidebar link` |
| 테마 버그 수정 | `fix` | `fix: correct image caption style` |
| 스타일만 변경 | `style` | `style: adjust code block font size` |

- `type` 뒤에는 콜론과 공백 한 칸을 둔다.
- `subject`를 영어로 쓸 때는 소문자로 시작한다. (`docs: Add post`는 거부된다)
- `subject`는 한글로 써도 된다. (`docs: 도커 설치 글 추가`)

---

## 9. 발행한 글을 고칠 때

| 하려는 일 | 방법 | 주의 |
| :-- | :-- | :-- |
| 내용 수정 | 고치고 커밋·push | 수정일이 자동으로 표시된다 |
| 제목 변경 | `title`만 수정 | 주소는 바뀌지 않는다 |
| 주소 변경 | 하지 않는다 | 슬러그를 바꾸면 기존 링크가 모두 깨진다 |
| 글 삭제 | 파일 삭제 후 커밋·push | 그 글로 연결된 내부 링크가 남아 있으면 배포가 실패한다 |
| 발행 시각 변경 | `date` 수정 | 홈 목록에서의 순서가 바뀐다 |

---

## 참고 자료

- [**Jekyll Docs: Posts**][jekyll-posts]
- [**Jekyll Docs: Front Matter**][jekyll-front-matter]
- [**Conventional Commits**][conventional-commits]

---

_`Last updated: 2026-11-17`_{: .right}

[jekyll-posts]: https://jekyllrb.com/docs/posts/
[jekyll-front-matter]: https://jekyllrb.com/docs/front-matter/
[conventional-commits]: https://www.conventionalcommits.org/ko/v1.0.0/
