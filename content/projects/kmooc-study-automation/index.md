---
title: "K-MOOC 강의 학습 자동화 시스템"
date: 2026-05-21
summary: "K-MOOC 강의를 효율적으로 소화하기 위한 자동화 파이프라인 — LMS 수집 → 자막 변환 → NotebookLM 가공 → 주차별 학습자료(요약/인포그래픽/HTML) 생성."
tags:
  - Toy Project
  - Automation
tech_stack:
  - Python
  - Cowork
  - Chrome MCP
  - NotebookLM (nlm CLI)
  - Whisper
  - Claude Code
  - Mermaid
featured: true
status: "Active"
role: "Solo Developer"
duration: "약 2주"
team_size: 1
highlights:
  - "3개 코스 / 약 101개 차시 자동 처리"
  - "수집·변환·가공·시각화 4단계 파이프라인"
  - "NotebookLM 추출 ↔ Claude 시각화 분업 구조"
---

K-MOOC 강의 학습 시간을 단축하려고 LMS 자료를 자동 수집·요약·시각화하는 파이프라인을 직접 만들었다. 3개 코스 / 약 101개 차시를 처리해 주차별 학습자료(요약·인포그래픽·HTML)를 자동 생성한다.

## [TL;DR] 한눈에 보기

- **무엇을 만들었나** — K-MOOC LMS의 강의 자막·슬라이드를 자동 수집하고, NotebookLM으로 요약·시각화 데이터를 추출해 주차별 통합 학습자료(`summary.md` · `infographic.png` · `study.html`)를 생성하는 파이프라인
- **처리 규모** — 3개 코스, 약 101개 차시, 산출물 67개 (요약 19 + HTML 32 + 인포그래픽 16)
- **사용 도구** — Python · Cowork · Chrome MCP · NotebookLM CLI(`nlm`) · Whisper · Claude Code · Mermaid

## [Stack] 기술 스택

- **Python** — VTT → TXT 변환, 매니페스트 빌드, 주차별 처리 스크립트 전반
- **Cowork** — Windows Chrome을 원격 제어하는 Linux 샌드박스 작업 환경 (수집 단계)
- **Chrome MCP** — LMS 자막·메타 자동 수집 (로그인 세션 쿠키 우회)
- **NotebookLM CLI (`nlm`)** — 강의 자료 요약·인포그래픽 자동 생성
- **Whisper (`large-v3`)** — 자막 미제공 차시 한국어 전사 fallback
- **Claude Code** — 가공 단계 작업 환경 + HTML 템플릿 렌더링
- **Mermaid** — 흐름·순환 구조 강의에 인터랙티브 다이어그램 임베드

## [Why] 만들게 된 이유

SK 하이닉스 지원을 준비하면서 반도체 공정, 스마트팩토리, 공장 내 IT 직무의 역할 등 도메인 지식이 부족하다는 것을 인지했다. K-MOOC 강의를 통해 보완하기로 했지만, 단순히 영상을 처음부터 끝까지 시청하는 방식은 시간 대비 효율이 너무 낮았다.

- 한 코스 = 수십 차시 × 각각 자막과 슬라이드가 흩어져 있음
- 시험에 나올 만한 핵심 개념·용어·비교 포인트가 영상 곳곳에 분산
- 검색·재방문이 어려운 동영상 매체 특성

영상을 통째로 듣는 대신 **"구조화된 요약물 + 시각화"** 로 빠르게 훑고, 필요한 부분만 깊게 보는 방식으로 학습 효율을 끌어올리고자 했다. 부족한 도메인 지식을 IT 역량으로 메우는 시도이기도 했다.

이 무렵 본 두 영상이 도구 선택의 결정적인 자극이 됐다.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 1rem; margin: 1.5rem 0;">
  <a href="https://www.youtube.com/watch?v=XiZO5ehF758" target="_blank" rel="noopener">
    <img src="https://img.youtube.com/vi/XiZO5ehF758/hqdefault.jpg" alt="영상 1: Claude Code + NotebookLM CLI 워크플로우" style="width: 100%; border-radius: 0.5rem; display: block;" />
  </a>
  <a href="https://www.youtube.com/watch?v=BeSKY9jVwnQ" target="_blank" rel="noopener">
    <img src="https://img.youtube.com/vi/BeSKY9jVwnQ/hqdefault.jpg" alt="영상 2: 같은 주제, 썸네일은 MCP지만 본문은 CLI" style="width: 100%; border-radius: 0.5rem; display: block;" />
  </a>
</div>

- **영상 1 (왼쪽)** — Claude Code + NotebookLM CLI 워크플로우. 두 도구를 묶는 방식을 처음 알게 됐고, "MCP보다 CLI가 효율적"이라는 주장에 공감했다.
- **영상 2 (오른쪽)** — 같은 주제의 다른 영상. 썸네일에 "MCP 연동"이 적혀 있어 처음엔 보지 않았는데, 나중에 확인해보니 본문은 결국 같은 CLI 조합을 추천하고 있었다 — 본문과 썸네일이 따로 노는 셈.

"마침 풀고 싶던 학습 효율 문제가 있었고, 마침 알맞은 도구 조합을 알게 됐다"는 타이밍이 이 프로젝트를 실제로 시작하게 만들었다.

## [How] 어떻게 만들었나

### [구조] 전체 구조

```
┌─ ① 수집 ─────────┐   ┌─ ② 변환 ──────┐   ┌─ ③ 가공 ─────────┐   ┌─ ④ 시각화 ───────┐
│ Chrome MCP      │   │ VTT → 타임    │   │ NotebookLM CLI   │   │ Claude          │
│ LMS 자막·PDF    │ → │ 스탬프 보존   │ → │ (nlm)           │ → │ HTML 템플릿     │
│ 자동 다운로드   │   │ TXT 변환      │   │ 통합 4섹션 쿼리  │   │ + Mermaid       │
└─────────────────┘   └───────────────┘   └──────────────────┘   └─────────────────┘
       ↓                      ↓                     ↓                      ↓
    .vtt + .pdf           .txt (인용용)        _raw/*.json           summary.md
                                                                  infographic.png
                                                                  study.html
```

### [절차] 단계별 처리

**① 수집 — Chrome MCP**

K-MOOC LMS는 로그인 세션 쿠키가 있어야 자막 엔드포인트에 접근 가능하다. Cowork Linux 샌드박스에서는 Windows Chrome 쿠키에 직접 접근할 수 없으므로, Chrome MCP를 통해 브라우저 안에서 `fetch(..., { credentials: 'include' })`로 우회. 코스 페이지 DOM에서 차시 메타를 추출하고, 각 차시 `viewer.php`에서 UUIDv4를 뽑아 `subtitle.php` 호출 → VTT 자막 일괄 수집.

**② 변환 — VTT → TXT**

`.vtt`는 NotebookLM이 지원하지 않는 포맷이므로 `[HH:MM:SS] 텍스트` 형식의 타임스탬프 보존 TXT로 변환. 시점 단위 인용이 가능해져 후속 요약에서 "몇 분 몇 초에 나온 개념"인지 추적 가능.

**③ 가공 — NotebookLM CLI(`nlm`)**

주차당 노트북 1개를 생성하고 차시별 PDF + TXT를 소스로 업로드. 4섹션(개념 위계 / 핵심 용어 / 비교표 / Key Takeaways)을 한 번에 받는 통합 프롬프트를 던져 JSON 응답을 받는다. 각 섹션 헤더(`=== SECTION N: ... ===`)를 정확히 박아두어 후처리에서 정규식으로 분리할 수 있게 설계.

NotebookLM은 MCP 연동도 가능하지만 **CLI를 선택**했다. MCP 방식은 Claude가 매 호출마다 응답 전체를 컨텍스트로 받아들이기 때문에 **토큰 소비가 사이클마다 누적**되어 수십 차시를 반복 처리하기엔 비효율적이었다. 게다가 응답 자체도 느리다. 반면 `nlm` CLI는 NotebookLM과 직접 통신하므로 Claude 컨텍스트를 거치지 않고, 로그인 인증만 한 번 통과해두면 Python 스크립트로 묶어 배치 처리 — 토큰 부담 없이 가볍게 돌아간다. 이번 작업의 규모에는 CLI 쪽이 압도적으로 적합했다.

**④ 시각화 — Claude로 단일 HTML 렌더링**

NotebookLM의 내부 마인드맵·인포그래픽은 외부로 가져오면 깨지거나 단순해진다. 그래서 **추출은 NotebookLM, 시각화는 Claude**로 역할을 분리. CSS 변수 기반 단일 파일 HTML 템플릿에 4섹션을 채워넣고, 흐름·순환 구조가 핵심인 주차에만 Mermaid 다이어그램(줌·팬·새 탭 보기 가능)을 임베드.

### [트러블] 시행착오

자동화하면서 마주친 주요 문제들:

| 문제 | 해결 |
|---|---|
| Chrome MCP `javascript_tool` 출력 한도 ≈ 2 KB | 자막 결과를 `window.__results`에 누적 → 순수 JS로 ZIP STORE(무압축) 직접 구현해 Blob URL 다운로드로 우회 |
| LMS에 자막 미등록인 차시 (코스 1 idx 39) | mp4 직접 다운로드 후 Whisper `large-v3`로 한국어 전사. `manifest_subtitles.json`에 `source: "whisper:large-v3"`로 표기 |
| 초기 `.md` 출력은 구조화된 강의 정리에 시각·인터랙션 한계 | [강의 정리에 .html이 더 적합하다는 영상](https://www.youtube.com/watch?v=CLI95vLlkxY) 을 보고 `.html`로 전환. 위계 트리(접고 펴기), 카드 그리드, 가로 스크롤 비교표, 줌·팬 다이어그램 등 표현 자유도 ↑ |
| NotebookLM 일일 quota (`RESOURCE_EXHAUSTED`) | `retry_queries.py`로 60→120→240→480초 백오프 자동 재시도. 한도 풀린 뒤 자동 재개 |
| 인포그래픽 일일 한도 3개/계정 | 코스별 배치 분할 + 익일 이어처리 |
| Mermaid 다이어그램 임베드 기준 모호 | 콘텐츠 특성에 따른 판단 매트릭스 작성 — 순환/시퀀스/다단계 분기일 때만 추가, 횡적 분류·시간순 비교는 표·트리로 충분 |

## [결과] 자세히 살펴본다면

코스별 산출물 현황 (2026-05-24 기준):

| 코스 | 차시 | PDF | VTT | 요약 (`summary.md`) | HTML (`study.html`) | 인포그래픽 |
|---|---|---|---|---|---|---|
| AI 스마트팩토리 기초 | 48 / 48 | 48 | 48 | 14 / 14 | 13 / 14 | 13 / 14 |
| 반도체 공정의 기초 | 10 / 10 | 10 | 10 | 5 / 5 | 5 / 5 | 3 / 5 |
| 스마트팩토리 | 43 / 43 | 14주차 | 43 | (생략) | 14 / 14 | 0 / 14 |
| **합계** | **101** | — | **101** | **19** | **32** | **16** |

- 자막 누락 차시 1개는 Whisper 전사로 보강 (180 cues, 약 11분 29초)
- 8개 주차에 Mermaid 인터랙티브 다이어그램(줌·팬·새 탭) 적용
- 코스마다 CSS `--accent` 변수만 바꿔 색상 차별화 (코스 1·2 인디고, 코스 3 시안)

영상 1코스를 전부 시청하면 수십 시간이 걸리던 것을, 자동 생성된 HTML 학습자료를 훑고 필요한 부분만 영상으로 돌아가는 방식으로 학습 시간을 크게 단축할 수 있었다.

## [회고] 배운 점

1. **"추출 ↔ 시각화" 분업 구조의 가치** — NotebookLM은 자료에서 사실을 환각 없이 뽑는 데 강하고, Claude는 주어진 구조를 보기 좋게 렌더링하는 데 능하다. 두 역할을 한 도구에 다 시키지 않고 분리한 것이 안정성과 결과물 품질을 동시에 끌어올렸다.
2. **외부 의존을 최소화하면 자동화가 단단해진다** — JSZip CDN이 K-MOOC 페이지 CSP에 막혀 동작하지 않았을 때, 라이브러리 우회 대신 ZIP STORE 포맷을 직접 구현. 학습자료 HTML도 단일 파일·외부 의존 0을 원칙으로 두어 어디서든 열린다 (Mermaid 사용 주차만 CDN 1개).
3. **도메인 갭을 IT 역량으로 메우는 패턴** — 부족한 지식을 그대로 시간 들여 메우는 대신, 학습 자체를 자동화하는 도구를 만들어 효율을 끌어올린다. 부수적으로 도구를 만드는 과정에서 도메인 지식까지 깊어지는 이중 효과가 있었다.
4. **표준 폴더 구조와 매니페스트의 위력** — `_subtitles/` · `_converted/` · `_raw/` 같은 언더스코어 prefix로 전처리 산출물을 분리해두니 코스가 늘어도 같은 스크립트가 그대로 동작. 표준화 덕에 코스 3 추가가 코스 1 대비 훨씬 빨랐다.

## [Next] 향후 계획

- [ ] 옛 경로 가정으로 짜인 스크립트(`process_week.py` · `run_all_weeks.py`) 리팩토링 — 신규 코스 추가 시 즉시 사용 가능하도록
- [ ] 학습 결과물 중 잘 정리된 주차를 블로그로 공개 — 도구의 산출 품질을 보여줄 수 있는 쇼케이스
- [ ] 다른 MOOC 플랫폼(Coursera·edX 등)으로 확장 가능성 검토
- [ ] 새 코스 추가 절차 자동화 — 현재는 매니페스트 생성·폴더 분산이 반자동
