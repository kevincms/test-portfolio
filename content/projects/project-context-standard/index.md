---
title: "Project Context Standard — 멀티세션 AI 프로젝트 문서 표준"
date: 2026-07-06
summary: "Claude Code·Codex와 장기 프로젝트를 이어갈 때 규칙·진행 상태·명세가 세션 사이에서 무너지지 않도록, project context를 3개 memory 레이어와 재사용 가능한 템플릿으로 설계하고 실제 프로젝트에 검증한 작업."
draft: false
tags:
  - Side Project
  - AI Engineering
  - Developer Tools
tech_stack:
  - Markdown
  - Claude Code
  - Codex
  - LLM Wiki
  - Git
featured: true
status: "Completed"
role: "Solo Designer"
duration: "2026.06 – 2026.07 (설계 3주)"
team_size: 1
highlights:
  - "procedural·episodic·semantic 3개 memory 레이어로 project context 정의"
  - "base 6파일 + 프로젝트 유형별 overlay + 도구별 add-on, 총 26개 템플릿"
  - "21개 설계·실증 세션을 거쳐 12개 프로젝트에 적용"
---

Claude Code나 Codex와 장기 프로젝트를 진행할 때 가장 먼저 무너진 것은 코드가 아니라 **프로젝트를 설명하는 맥락**이었다. 한 세션 안에 끝나지 않는 작업에서 매번 같은 구조를 다시 설명했고, `context.md` 한 장에 규칙·진행 상태·설계가 섞이면서 문서는 점점 길고 부정확해졌다.

이 문제를 해결하기 위해 세션 사이에 유지할 문서 집합을 **Project Context**로 정의하고, 문서의 역할·이름·배치·갱신 방법을 교차 프로젝트 표준으로 설계했다. 결과물은 단순한 문서 작성법이 아니라 새 프로젝트에 복사해 적용할 수 있는 템플릿과 세션 시작·종료 절차다.

## [TL;DR] 한눈에 보기

- **문제** — 긴 프로젝트에서 세션이 바뀔 때마다 규칙·결정·진행 상태가 유실되고, 하나의 핸드오프 문서를 반복 재작성하면서 세부 맥락이 침식됨
- **정의** — Project context는 장기 프로젝트를 위해 유지하는 영속 문서 집합 전체이며, 내부를 procedural·episodic·semantic memory로 구분
- **설계** — 공통 base 6파일, knowledge-vault·code-repo overlay, 도구별 세션 명령 add-on, control·artifact 평면 배치 규칙
- **검증** — 2026년 6월 16일부터 7월 6일까지 설계 본류 19세션과 초기 배포 실증 2세션을 거쳐 표준을 확정하고, 이후 총 12개 프로젝트에 적용
- **핵심 원칙** — 크기가 아니라 **읽는 시점**, 복사가 아니라 **정본과 포인터**, 전면 재작성이 아니라 **증분 갱신**

## [Why] 왜 만들었나

처음에는 `context.md` 하나로 직전 세션과 다음 할 일을 넘겼다. 작을 때는 충분했지만 프로젝트가 길어지자 네 가지 문제가 드러났다.

1. **세션 재시작 비용** — 새 세션마다 이미 결정한 구조와 제약을 다시 찾고 설명해야 했다.
2. **Context collapse** — 문서를 통째로 요약·재작성할수록 구체적인 예외와 결정 근거가 조금씩 사라졌다.
3. **서로 다른 실행 위치** — WSL에서 에이전트를 실행하는 경로와 Windows 드라이브의 실제 결과물 경로가 다른 프로젝트가 많았다.
4. **도구와 프로젝트 유형의 차이** — Claude Code·Codex가 읽는 규칙 파일이 다르고, 지식 볼트와 일반 코드 프로젝트가 요구하는 운영 방식도 달랐다.

여기에 LLM Wiki를 다른 프로젝트의 지식 소스로 쓰되, 소비 프로젝트가 Wiki 원본을 직접 수정하지 않게 하는 경계도 필요했다. 결국 문제는 “무슨 내용을 적을까”보다 **어떤 정보를 어디에 두고 언제 읽고 어떻게 갱신할까**에 가까웠다.

## [Concept] Project Context란 무엇인가

내부 LLM Wiki에서 context engineering·agent memory·handoff·spec-driven development를 조사한 뒤 다음과 같이 용어를 고정했다.

> **Project context** — 장기 프로젝트를 위해 유지하는 영속 Markdown 문서 집합 전체. 디스크에서는 memory로 존재하고, 필요한 순간 컨텍스트 창에 로드된다.

Project context는 세 가지 memory 레이어로 구성된다.

| 레이어 | 답하는 질문 | 성격 | 대표 파일 |
|---|---|---|---|
| **Procedural memory** | 어떻게 일하는가? | 안정적인 규칙·절차 | `AGENTS.md`, `CLAUDE.md`, `HANDOFF-RULES.md`, `constitution.md` |
| **Episodic memory** | 어디까지 했는가? | 세션마다 변하는 상태 | `context.md`, `progress.md`, `todo.md` |
| **Semantic memory** | 무엇을 만들고 있는가? | 비교적 안정적인 명세·지식 | `spec.md`, `docs/`, 하위 시스템 `README.md` |

이때 **context engineering**은 세션마다 무엇을 읽고 빼고 압축할지 결정하는 행위이고, **handoff**는 episodic memory를 다음 세션으로 넘기는 하위 동작이다.

### 조사에서 얻은 설계 근거

- [Anthropic의 Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — 컨텍스트를 유한 자원으로 보고 durable knowledge와 working state를 분리
- [ACE 논문](https://arxiv.org/abs/2510.04618v3) — 반복적인 문서 전체 재작성에서 발생하는 context collapse와 brevity bias, 증분 갱신과 grow-and-refine 처방
- [MemGPT 논문](https://arxiv.org/abs/2310.08560v2) — 컨텍스트 창을 RAM, 외부 저장을 disk처럼 다루는 계층형 memory 관점
- [GitHub Spec Kit](https://github.com/github/spec-kit) — constitution → spec → plan → tasks로 명세와 구현을 제약하는 방식
- [Harper Reed의 LLM codegen workflow](https://harper.blog/2025/02/16/my-llm-codegen-workflow-atm/)와 [HANDOFF.md 패턴](https://fazm.ai/blog/claude-code-architecture-handoff-pattern) — 체크리스트로 상태를 이어가고 과거 에이전트가 미래 에이전트에게 핵심만 넘기는 실전 사례

중요한 발견은 **Spec-driven development만으로는 세션 핸드오프가 해결되지 않는다**는 점이었다. Spec Kit은 무엇을 만들지에는 강하지만, 어제 어디까지 했고 다음에 무엇을 해야 하는지는 별도의 episodic layer가 맡아야 했다.

## [Process] 어떤 과정으로 설계했나

### 1. 문서보다 용어를 먼저 고정했다

첫 질문은 “파일을 몇 개 만들까”가 아니었다.

> “용어를 먼저 정확히 정의했으면 해. 장기 프로젝트에서 세션 간 공유를 위해 Markdown 문서로 관리하는 이 행위 혹은 문서들을 뭐라고 정의하는 게 적절할까?”

엄밀히는 디스크의 문서는 memory지만, 표준의 범위는 세션에 로드되고 관리되는 전체였다. 그래서 `Project Memory` 대신 **Project Context Standard**를 이름으로 선택하고, memory는 내부 레이어를 설명하는 용어로 남겼다.

### 2. 파일을 크기가 아니라 트리거로 나눴다

초기에는 배포법·사이트 구조·자동화 사용법을 `HANDOFF-RULES.md` 한곳에 넣고 필요한 섹션만 검색하는 방안도 검토했다. 결론은 **언제 읽는지가 다르면 파일도 달라야 한다**였다.

- 매 세션 항상 필요한 행동 규칙 → `AGENTS.md`
- 세션 종료 때만 필요한 갱신 절차 → `HANDOFF-RULES.md`
- 배포할 때만 필요한 운영법 → `docs/deployment.md`
- 특정 자동화를 다룰 때만 필요한 지식 → 코드 옆 `README.md`

파일 크기는 분리의 원인이 아니라, 같은 트리거 안에서 섹션을 나눌 시점을 알려주는 보조 신호로만 사용했다.

### 3. 중복을 없애고 정보의 수명을 설계했다

같은 사실이 세 번 반복해서 나타나는 것을 보고 **한 사실은 정본 한곳에만 두고 나머지는 포인터로 연결한다**는 원칙을 만들었다. 중복 여부는 세 축으로 판단했다.

- **레이어** — 규칙·상태·명세 중 어디에 속하는가
- **스코프** — 프로젝트 전체인가, 특정 기능에만 해당하는가
- **수명** — 계속 유지할 지식인가, 구현 기간에만 쓰는 기록인가

Code repository에서는 기능별 `spec.md`·`plan.md`·`tasks.md`를 작업 기록으로 유지하고, 완료 뒤 장기 보존 가치가 있는 부분만 `docs/`로 **졸업**시키는 흐름을 추가했다. 임시 계획과 영속 문서를 섞지 않기 위한 장치다.

### 4. 실행 위치와 결과물 위치를 두 평면으로 분리했다

실제 사용 환경에서는 에이전트를 실행하는 WSL 경로와 Git 저장소·Obsidian 볼트가 있는 Windows 경로가 달랐다. 이를 예외로 취급하지 않고 구조에 포함했다.

```text
[Control plane — 세션 운전석]
context.md · progress.md · todo.md · HANDOFF-RULES.md
                         │
                         │ context.md의 단방향 포인터
                         ▼
[Artifact plane — 결과물과 운영 규칙]
AGENTS.md · CLAUDE.md · constitution.md · specs/ · docs/ · source
                         │
                         │ ref로 읽고 outbox로 되먹임
                         ▼
[Knowledge vault — 외부 지식 정본]
index.md → wiki/ 합성 문서 · consumer에서는 read-only
```

단일 저장소는 두 평면을 같은 루트에 둘 수 있고(Layout A), 공개 결과물만 하위 Git 저장소로 분리할 수도 있으며(Layout A′), 실행 위치와 결과물이 다르면 두 평면을 완전히 나눌 수 있다(Layout B). 핵심은 물리적 위치가 아니라 **control과 artifact의 책임을 섞지 않는 것**이었다.

동시에 프로젝트 유형을 두 가지 overlay로 나눴다.

- **Knowledge vault** — 지식을 수집·합성하고 다른 프로젝트에 제공. `ref`·`outbox`·`drain` 채널과 문서 schema가 필요
- **Code repository** — Wiki를 읽기 전용으로 소비하며 constitution·spec·plan·tasks·docs 흐름을 사용

### 5. 전 파일을 다시 읽고 모호함을 결정 항목으로 바꿨다

초안을 바로 표준으로 선언하지 않고 라이브 배포 전에 전체 파일을 검토했다. 이 과정에서 14개의 수정·추가·모호 항목을 찾았다.

- drain은 매 세션 자동 실행하지 않고 요청이 있을 때만 수행
- 템플릿 안에서만 통하는 Wiki 링크는 복사된 프로젝트에서 깨지므로 평문 규칙으로 교체
- 파일이 어느 평면으로 복사되는지 배치표 추가
- 신규 프로젝트와 기존 프로젝트 소급 적용을 구분하고, 기존 `AGENTS.md`는 덮어쓰지 않도록 제한
- 프로젝트 유형·배치·Wiki 소비 여부·Git·문서 언어를 먼저 확인하는 Step 0 인터뷰 추가

결정되지 않은 항목을 억지로 일반화하지 않은 것도 중요했다. Automation 전용 overlay와 자동 spec gate는 실증이 부족하다고 판단해 표준에 넣지 않고 **보류 상태로 확정**했다.

### 6. 세션 시작과 종료를 명령으로 만들었다

매번 “`context.md`를 읽어줘”, “핸드오프 문서를 업데이트해줘”라고 입력하던 동작을 `/pickup`과 `/hand-off`로 정리했다.

- `/pickup` — 현재 실행 위치의 `context.md`를 진입점으로 읽고, artifact 규칙과 `todo.md`를 따라 현재 상태 파악
- `/hand-off` — 세션에서 바뀐 사실을 정본에 먼저 저장한 뒤 `progress.md`·`todo.md`·`context.md`를 각 역할에 맞게 갱신

도구별 자동 로드 차이도 해결했다. 규칙의 정본은 `AGENTS.md` 한곳에 두고, Claude Code에는 `CLAUDE.md`가 `@AGENTS.md`를 가져오는 bridge 역할만 하게 했다. 복사본 두 개를 사람이 동기화하던 구조를 제거한 것이다.

### 7. 성공 사례뿐 아니라 실패로 규칙을 보강했다

초기 Antigravity PDF 변환 프로젝트에서는 결과물이 PDF 페이지 이미지를 Markdown에 붙여넣은 수준에 그쳤다. 이 실패를 “도구가 부족했다”로 끝내지 않고, project context에 다음 규칙이 부족했다는 증거로 사용했다.

- 텍스트를 실제로 추출해야 한다는 성공 기준
- OCR fallback과 실패 모드
- 최종 산출물과 중간 raw 파일의 분리
- 검증 없이 완료로 처리하지 않는 gate

이후 신규 knowledge vault에 템플릿을 수정 없이 적용해보고, 이미 운영 중인 프로젝트에는 기존 규칙을 보존한 채 빠진 구조만 소급 적용했다. 마지막으로 표준을 만든 LLM Wiki 자체에도 같은 구조를 적용해 **자기 자신으로 자기 설계를 검증하는 도그푸딩**을 수행했다.

## [Architecture] 최종 구성

공통 base는 여섯 파일이다.

| 파일 | 역할 | 읽는 시점 |
|---|---|---|
| `AGENTS.md` | 도구 공통 운영 규칙의 정본 | 매 세션 자동 |
| `CLAUDE.md` | Claude Code용 `AGENTS.md` bridge | 매 세션 자동 |
| `context.md` | 현재 상태와 작업 위치를 가리키는 슬림 진입점 | 세션 시작 |
| `progress.md` | 완료 작업과 결정의 append-only 이력 | 필요할 때 검색 |
| `todo.md` | 미완 작업의 단일 정본 | 세션 시작·계획 |
| `HANDOFF-RULES.md` | 문서를 어디에 어떤 형식으로 갱신할지 정한 런북 | 세션 종료 |

여기에 project type별 overlay와 tool별 add-on을 더한다.

```text
project-context/
├─ base 6 files
├─ overlays/
│  ├─ knowledge-vault/
│  └─ code-repo/
├─ addons/
│  ├─ claude-commands/
│  └─ antigravity-skills/
└─ _TEMPLATE-USAGE.md
```

## [Result] 무엇이 남았나

- **실파일 템플릿 26개** — base 6파일, knowledge-vault·code-repo overlay, 도구별 세션 명령 add-on, 배치·복사 가이드
- **설계 기록 21세션** — 설계 본류 19세션 + 초기 배포 실증 2세션의 읽기용 대화와 원본 JSONL 보존
- **검증된 적용** — 6개 프로젝트 실증 뒤 표준을 `reviewed`로 승격했고, 2026년 7월 아카이브 기준 12개 프로젝트까지 적용
- **Greenfield와 retrofit 모두 검증** — 새 knowledge vault는 템플릿 수정 없이 통과했고, 기존 프로젝트는 규칙을 덮어쓰지 않고 빠진 부분만 추가
- **현재 포트폴리오도 실사용 사례** — 세션 운전 상태와 실제 사이트 저장소가 분리된 Layout B로 이 표준을 사용 중

## [Retrospective] 배운 점

1. **문서 시스템의 본질은 작성이 아니라 라우팅이다** — 같은 정보를 더 잘 요약하는 것보다, 정보가 생겼을 때 들어갈 위치와 갱신 형식을 결정론적으로 정하는 것이 drift를 줄였다.
2. **짧음과 보존은 서로 다른 채널로 해결한다** — `context.md`는 짧게 유지하고, 세부 이력은 `progress.md`에 누적해야 시작 비용과 정보 보존을 동시에 잡을 수 있었다.
3. **실패 결과물은 규칙의 테스트 케이스다** — 품질이 낮은 산출물은 프롬프트만 고칠 문제가 아니라 성공 기준·검증 gate·파일 역할이 부족하다는 신호였다.
4. **도구 이식은 복사가 아니다** — 같은 의도를 유지하되 각 도구의 자동 로드 규칙과 파일 형식에 맞게 bridge와 add-on을 달리해야 했다.
5. **모든 패턴을 표준에 넣지 않는다** — 두세 사례에서만 보인 특수한 구조는 관찰로 남기고, 반복 실증된 것만 공통 규칙으로 승격했다.

## [Next] 이후 확장

- 문서 이름·포인터·용량 예산을 자동 검사하는 lint 도입 여부 검토
- 기능 개발과 탐색성 spike를 구분하는 spec gate를 더 사용해본 뒤 표준 승격 판단
- Claude Code·Codex·worktree 기반 도구에서 생기는 운영 차이를 실사용 관찰로 계속 보강
