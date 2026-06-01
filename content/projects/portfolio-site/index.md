---
title: "개발자 포트폴리오 사이트 (Hugo Blox)"
date: 2026-05-31
summary: "Hugo Blox로 만든 개인 포트폴리오 사이트 — 정적 파일만으로 운영하면서, NotebookLM 기반 AI 블로그 digest 자동화·렌더링 버그 우회까지 직접 해결한 프로젝트."
tags:
  - Side Project
  - Automation
  - Full-Stack
tech_stack:
  - Hugo Blox
  - Hugo (extended)
  - Tailwind CSS v4
  - Go (modules)
  - Python
  - NotebookLM (nlm CLI)
  - Puppeteer / Node
  - Mermaid
  - Plotly
  - Claude Code
featured: true
status: "Active"
role: "Solo Developer"
duration: "진행 중"
team_size: 1
links:
  # TODO: 실제 저장소 URL로 교체 (공개 레포가 없으면 이 블록 제거)
  - type: github
    url: https://github.com/kevincms/portfolio
    label: Code
highlights:
  - "테마 .html 수정 0 — 정적 파일(.yaml/.md)만으로 사이트 운영"
  - "NotebookLM 기반 AI 블로그 digest 자동화 3종 (1,300+건 처리)"
  - "테마 렌더 버그(mermaid oklch)·Cloudflare 차단 등 직접 규명·우회"
---

Hugo Blox(`dev-portfolio` 템플릿)로 개인 포트폴리오 사이트를 만들고 운영한다. 단순 템플릿 적용에 그치지 않고, **테마를 수정하지 않는다는 제약** 아래에서 AI 블로그 자동화·시각화·렌더링 버그 우회까지 직접 해결한 것이 핵심이다.

## [TL;DR] 한눈에 보기

- **무엇** — Hugo Blox 기반 개인 포트폴리오. 프로필·프로젝트·블로그·기술 문서(docs)를 블록과 콘텐츠 타입으로 구성
- **원칙** — 테마는 Go 모듈로 가져오므로 **`.html`을 건드리지 않고** `.yaml`/`.md` 정적 파일만으로 모든 것을 구현 (유지보수·업데이트 충돌 최소화)
- **핵심 성과** — NotebookLM(`nlm` CLI)으로 Anthropic·Hugging Face·OpenAI 블로그를 자동 요약하는 **digest 파이프라인 3종**(누적 1,300+건)과, 테마 한계를 정적 파일 내에서 우회한 다수의 트러블슈팅
- **스택** — Hugo Blox · Tailwind v4(테마) · Python · NotebookLM CLI · Puppeteer · Mermaid · Plotly · Claude Code

## [Stack] 기술 스택

- **Hugo Blox / Hugo (extended)** — 블록형 정적 사이트. 콘텐츠는 전부 Markdown/YAML, 테마는 Go 모듈
- **Tailwind CSS v4** — 테마 스타일 (직접 작성보다 테마 변수·인라인 스타일 활용)
- **Python** — digest 자동화 스크립트(수집·요약·빌드)와 추세 분석(`build_theme_trends.py`)
- **NotebookLM (`nlm` CLI)** — 블로그 본문 AI 요약 생성 (플랜별 하루 query 한도 = quota)
- **Puppeteer / Node** — Cloudflare로 막힌 본문 fetch 우회, 렌더 검증(headless 캡처)
- **Mermaid · Plotly** — 블로그 digest의 추세 시각화(타임라인·추세선 차트)
- **Claude Code** — 전 과정의 작업 환경(WSL ↔ Windows 연동)

## [Why] 만들게 된 이유

- 이력서 한 장으로는 담기 어려운 **프로젝트의 맥락과 과정**을 보여줄 공간이 필요했다.
- 직접 프론트엔드를 처음부터 짜기보다, 검증된 테마(Hugo Blox) 위에서 **콘텐츠와 자동화에 집중**하는 편이 효율적이라 판단했다.
- 다만 "템플릿만 깔았다"로 끝내지 않기 위해, **테마를 수정하지 않는다는 제약**을 스스로 걸었다. 제약 안에서 원하는 기능을 구현하는 과정이 곧 엔지니어링 역량을 보여주는 소재가 됐다.

## [How] 어떻게 만들었나

### 1. 블록 / 콘텐츠 타입으로 구조 설계

홈은 `landing` 타입 한 장에 블록(`dev-hero`·`portfolio`·`tech-stack`·`collection` 등)을 쌓고, `/blog/`·`/projects/`·`/docs/`는 각자 다른 **콘텐츠 타입**(레이아웃)으로 분리했다. 특히 기술 메모는 사이드바가 있는 `docs` 타입에 정리한다.

### 2. AI 블로그 digest 자동화 (핵심)

Anthropic·Hugging Face·OpenAI의 블로그 전체를 수집해 NotebookLM으로 자동 요약하는 파이프라인을 만들었다.

| Digest | 처리 규모 | 특이 난관 |
|---|---|---|
| Anthropic | 356건 (News/Research/Engineering) | 시간순 추세 분석 + 시각화 |
| Hugging Face | 785건 (100% 완료) | 대량 처리 / quota 분할 |
| OpenAI | 228건 (진행 중) | Cloudflare 차단, 태그=sitemap 멤버십, 발행일=RSS |

- **수집 → 요약 → 빌드** 단계 스크립트화. 요약문은 고유명사·AI 용어는 영어 유지, 번역어는 한글로 통일
- OpenAI는 본문이 Cloudflare로 막혀 **Puppeteer + 실제 Chrome**(`--disable-blink-features=AutomationControlled`)으로 우회

### 3. 테마 한계를 정적 파일로 우회

- **mermaid 렌더 버그** — Tailwind v4의 `oklch` 색을 테마 스크립트가 `rgb(oklch())`로 잘못 감싸 다이어그램이 전부 깨짐. 본문 인라인 `<script>`로 유효 색 재초기화 + 재실행해 **테마 수정 없이** 해결 (+ 드래그 팬·줌·새 탭 기능 추가)
- **블로그 카드 표지** — 표지 없으면 빈 카드 → AI 생성 배경(Gemini) + 공식 로고 합성으로 `featured.png` 제작
- **WSL ↔ Windows 워크플로** — hugo 서버는 Windows에서, 작업은 WSL에서. 랜덤 포트를 PID로 역추적하고 Puppeteer로 렌더 검증

## [결과] 자세히 살펴본다면

- **사이트 구성**: 홈(블록 10개) + 프로젝트 아카이브 + 블로그(digest 3종 + 회고) + 기술 문서(docs)
- **자동화 산출**: AI digest 누적 1,300+건 요약, 추세 분석 시각화 포함
- **운영 원칙 유지**: 모든 기능을 `.yaml`/`.md`로 구현 — 테마 업데이트 시 충돌 위험 최소화

<!-- TODO: 라이브 사이트 캡처/스크린샷을 여기에 inline grid로 배치 (context.md § 콘텐츠 페이지 작성 참고) -->

## [회고] 배운 점

1. **제약이 곧 설계다** — "테마를 수정하지 않는다"는 제약이 오히려 블록·콘텐츠 타입·숏코드 같은 테마 본래 메커니즘을 깊이 이해하게 만들었다.
2. **버그는 재현에서 풀린다** — mermaid가 깨지던 원인을 headless 브라우저 콘솔로 포착하고 jsdom으로 재현해서야 정확한 우회를 찾았다. "왜 깨지는지"를 끝까지 본 것이 핵심.
3. **AI 도구의 역할 분담** — NotebookLM은 사실 추출, Claude는 구조·렌더링. 한 도구에 다 시키지 않고 나눈 것이 안정성과 품질을 같이 끌어올렸다.

## [Next] 향후 계획

- [ ] OpenAI digest 요약 마무리 (남은 96건) + `📈 Trends` 섹션 추가
- [ ] 본인 데이터 정비 (`me.yaml` bio·skills·experience, 홈 캐치프레이즈)
- [ ] 프로젝트 카드 본문·스크린샷 보강
- [ ] 트러블슈팅 기록을 **docs/blog로 공개** — 아래 참고

## 더 읽기

이 프로젝트에서 다룬 내용은 사이트 안에 더 자세히 정리되어 있다.

- 📘 기술 문서: [`/docs/`](/docs/) — Docker 등 하우투 정리 (HugoBlox 블록 가이드·mermaid 우회 등 추가 예정)
- ✍️ 블로그 digest: [`/blog/`](/blog/) — Anthropic · Hugging Face · OpenAI 자동 요약
