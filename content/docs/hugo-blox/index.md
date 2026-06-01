---
title: Hugo & Hugo Blox
weight: 5
date: 2026-05-31
summary: 이 사이트의 토대인 정적 사이트 생성기 Hugo와, 그 위의 블록형 빌더 Hugo Blox 개념 정리.
categories:
  - Web
tags:
  - Hugo
  - Hugo Blox
  - Static Site
---

이 포트폴리오 사이트가 올라가 있는 두 도구를 정리한다. **Hugo**는 토대(엔진), **Hugo Blox**는 그 위에서 페이지를 블록으로 조립하게 해주는 빌더다.

## Hugo란?

**Hugo**는 Go로 만든 **정적 사이트 생성기**(Static Site Generator, SSG)다. Markdown으로 쓴 콘텐츠와 템플릿을 미리 HTML로 변환해 두고, 그 결과물을 그대로 서빙한다.

| 특징 | 의미 |
|---|---|
| **정적(static)** | 요청마다 서버에서 페이지를 만들지 않고, 미리 만든 HTML을 그대로 전달 → 빠르고 안전, 호스팅 저렴 |
| **빠른 빌드** | Go 기반이라 수천 페이지도 수 초 내 빌드 |
| **Markdown 중심** | 글은 `.md`, 설정·데이터는 `.yaml`/`.toml`로 관리 |
| **테마 시스템** | 레이아웃(`.html` 템플릿)을 테마로 분리. 콘텐츠와 디자인이 깔끔히 분리됨 |

> 동적 서버(예: WordPress)와 달리 DB가 없다. 콘텐츠가 곧 파일이라 Git으로 버전 관리되고, 빌드 결과는 어떤 정적 호스팅(Netlify·GitHub Pages·Vercel 등)에도 올릴 수 있다.

## Hugo Blox란?

**Hugo Blox**(구 Wowchemy)는 Hugo 위에서 동작하는 **블록형 사이트 빌더**다. Hugo의 템플릿을 직접 짜지 않고도, 미리 만들어진 "**블록**"을 YAML에 쌓아 페이지를 구성한다.

- 테마를 **Go 모듈**(`HugoBlox/kit/modules/blox`)로 가져온다 → 테마 자체는 건드리지 않고 버전만 업데이트
- 페이지 = 블록의 조합. 콘텐츠·설정은 전부 Markdown/YAML
- 포트폴리오·문서·블로그용 템플릿 제공 (이 사이트는 `dev-portfolio` 템플릿)

### 페이지를 만드는 두 단위 — 블록 vs 콘텐츠 타입

Hugo Blox에서 페이지를 조립하는 단위는 두 가지다. 헷갈리기 쉬우니 구분해 둔다.

| | **블록 (Block)** | **콘텐츠 타입 (Content Type)** |
|---|---|---|
| 정체 | 한 페이지에 쌓는 **섹션 부품** | 폴더가 통째로 갖는 **페이지 레이아웃** |
| 선언 | `_index.md`의 `sections:` 배열 | 폴더명 (`content/<타입>/`) |
| 예시 | `dev-hero`, `portfolio`, `tech-stack`… | `landing`, `docs`(사이드바), `blog` |
| URL | 같은 페이지 내 앵커 (`/#projects`) | 독립 페이지 (`/docs/`, `/blog/`) |

즉 **홈페이지 한 장은 `landing` 타입**이고 그 안을 블록으로 채우며, 지금 보고 있는 이 **문서 페이지는 `docs` 타입**(좌측 사이드바 레이아웃)이다.

### 본문에서 쓰는 숏코드(shortcode)

블록 외에, 글 본문 안에서는 **숏코드**로 풍부한 요소를 넣는다 — `chart`(차트), `video`, `callout`(강조 박스), `mermaid`(다이어그램), `math`(수식), `cards` 등. 모두 Markdown 안에서 호출한다.

## 이 사이트가 Hugo Blox를 택한 이유

- **콘텐츠에 집중** — 프론트엔드를 처음부터 짜는 대신 검증된 테마 위에서 글·프로젝트·자동화에 시간을 쓴다.
- **정적 파일만으로 운영** — 모든 것을 `.yaml`/`.md`로 구현하고 테마 `.html`은 건드리지 않는다 → 테마 업데이트 시 충돌 위험이 적고 유지보수가 단순하다.
- **Git 친화적** — 콘텐츠가 곧 파일이라 변경 이력이 그대로 남는다.

## 참고

- Hugo 공식 문서: <https://gohugo.io/>
- Hugo Blox: <https://hugoblox.com/>
