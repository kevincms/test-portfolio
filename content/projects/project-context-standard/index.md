---
title: "새 AI 세션에 작업 맥락을 넘기는 방법 (Project Context)"
date: 2026-07-06
lastmod: 2026-09-10
summary: "AI로 메모리 논문과 GitHub 사례를 조사해, 이전 결정과 남은 작업을 다음 세션에 넘기는 문서 구조와 인계 절차를 설계했다."
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
status: "Maintained"
role: "설계·적용·운영 검증"
duration: "2026.06 – 현재 (초기 설계 3주)"
team_size: 1
highlights:
  - "메모리 연구와 공개 개발 사례를 바탕으로 문서의 역할·읽는 시점·갱신 방식 설계"
  - "공통 문서 6개와 프로젝트별 확장 규칙, 도구별 세션 시작·종료 절차 작성"
  - "실제 운영에서 발견한 규칙 불일치와 과도한 문서 읽기를 단계적으로 보완"
---

<style>
main:has(#pcs-summary) h1{word-break:keep-all;overflow-wrap:break-word}
.pcs-viz{--pcs-ink:#1e293b;--pcs-muted:#64748b;--pcs-line:#dbe4e8;--pcs-bg:#f8fafb;--pcs-card:#fff;--pcs-accent:#0f766e;--pcs-tint:#eaf7f4;color:var(--pcs-ink);margin:2rem 0;font-size:.9rem;line-height:1.6;word-break:keep-all;overflow-wrap:break-word}
.dark .pcs-viz{--pcs-ink:#e2e8f0;--pcs-muted:#a6b5c6;--pcs-line:#334155;--pcs-bg:#17202d;--pcs-card:#111827;--pcs-accent:#5eead4;--pcs-tint:#12352f}
.pcs-viz *{box-sizing:border-box;min-width:0}
.pcs-viz :where(p,h3,h4,ul,ol,li,figcaption){margin:0}
.pcs-viz :where(ul,ol){padding:0;list-style:none}
.pcs-viz code{padding:0!important;background:transparent!important;color:inherit;font-size:.84em;font-weight:500;overflow-wrap:anywhere}
.pcs-viz code::before,.pcs-viz code::after{content:none}
.pcs-viz a{color:inherit;text-decoration:none!important}
.pcs-viz a:focus-visible{outline:3px solid var(--pcs-accent);outline-offset:4px}
.pcs-viz strong{color:var(--pcs-ink)}
.pcs-eyebrow{display:block;color:var(--pcs-accent);font-size:.7rem;font-weight:700;letter-spacing:.09em;margin-bottom:.35rem}
.pcs-figure{padding:1.3rem;border:1px solid var(--pcs-line);border-radius:16px;background:var(--pcs-bg)}
.pcs-figure-heading{margin-bottom:1rem}
.pcs-figure-heading>strong{display:block;font-size:1.02rem;line-height:1.5}
.pcs-viz figcaption{margin-top:1rem;color:var(--pcs-muted);font-size:.75rem;line-height:1.65;text-align:left}
.pcs-roadmap-grid{display:grid;grid-template-columns:repeat(5,minmax(0,1fr));gap:.5rem}
.pcs-roadmap-grid>a{display:flex;flex-direction:column;gap:.2rem;padding:.85rem .7rem;background:var(--pcs-card);border:1px solid var(--pcs-line);border-radius:10px;transition:border-color .15s,background-color .15s}
.pcs-roadmap-grid>a:hover{border-color:var(--pcs-accent);background:var(--pcs-tint)}
.pcs-roadmap-number{font-family:ui-monospace,monospace;font-size:1.35rem;font-weight:700;color:var(--pcs-accent);line-height:1.2}
.pcs-roadmap-grid strong{font-size:.95rem}
.pcs-roadmap-grid small{color:var(--pcs-muted);font-size:.7rem;line-height:1.45}
.pcs-memory table{width:100%;table-layout:fixed;font-size:.85rem;line-height:1.65}
.pcs-memory :where(th,td){vertical-align:top;word-break:keep-all;overflow-wrap:anywhere}
.pcs-memory code{padding:0!important;background:transparent!important;white-space:normal;overflow-wrap:anywhere;font-size:.75rem;font-weight:500}
.pcs-memory code::before,.pcs-memory code::after{content:none}
.pcs-research-head,.pcs-research-row{display:grid;grid-template-columns:.8fr 1.1fr 1.2fr;gap:1rem}
.pcs-research-head{padding:0 .9rem .5rem;color:var(--pcs-muted);font-size:.7rem;font-weight:700}
.pcs-research-row{align-items:center;padding:1rem .9rem;background:var(--pcs-card);border:1px solid var(--pcs-line);border-radius:10px;margin-top:.5rem}
.pcs-research-row strong{font-size:.87rem}
.pcs-research-row span{display:block;font-size:.79rem;line-height:1.6}
.pcs-research-row .pcs-evidence{color:var(--pcs-muted)}
.pcs-research-row .pcs-decision{color:var(--pcs-accent);font-weight:600;border-left:2px solid var(--pcs-accent);padding-left:.8rem}
.pcs-panels{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:1rem}
.pcs-panel{padding:1rem;background:var(--pcs-card);border:1px solid var(--pcs-line);border-radius:10px}
.pcs-panel-title{display:flex;align-items:baseline;justify-content:space-between;gap:.5rem;margin-bottom:.8rem}
.pcs-panel-title strong{font-size:.9rem}
.pcs-panel-title small{color:var(--pcs-muted);font-family:ui-monospace,monospace;font-size:.63rem;letter-spacing:.03em}
.pcs-tree li{padding:.45rem 0 .45rem .65rem;border-left:2px solid var(--pcs-line)}
.pcs-tree li+li{margin-top:.3rem}
.pcs-tree code{display:block;color:var(--pcs-accent);font-size:.79rem;line-height:1.5}
.pcs-tree span{display:block;color:var(--pcs-muted);font-size:.72rem;line-height:1.5;margin-top:.08rem}
.pcs-pointer{margin-top:1rem;padding:.65rem .8rem;border-radius:8px;background:var(--pcs-tint);color:var(--pcs-accent);font-size:.76rem;text-align:center}
.pcs-sequence{counter-reset:pcs-step}
.pcs-sequence li{counter-increment:pcs-step;display:grid;grid-template-columns:1.35rem minmax(0,1fr);gap:.55rem;align-items:start;padding:.5rem 0;font-size:.78rem}
.pcs-sequence li::before{content:counter(pcs-step);display:grid;place-items:center;width:1.3rem;height:1.3rem;border-radius:50%;background:var(--pcs-tint);color:var(--pcs-accent);font-size:.7rem;font-weight:700}
.pcs-sequence code{font-size:.75rem}
.pcs-sequence span{display:block}
.pcs-timeline{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:.8rem}
.pcs-timeline a{display:block;position:relative;border-top:2px solid var(--pcs-line);padding-top:.85rem}
.pcs-timeline a::before{content:"";position:absolute;top:-5px;left:0;width:8px;height:8px;border-radius:50%;background:var(--pcs-accent)}
.pcs-timeline time{display:block;color:var(--pcs-accent);font-family:ui-monospace,monospace;font-size:.76rem;margin-bottom:.25rem}
.pcs-timeline strong{display:block;font-size:.83rem;line-height:1.5}
.pcs-timeline span{display:block;color:var(--pcs-muted);font-size:.73rem;line-height:1.6;margin-top:.25rem}
[id^="pcs-"]{scroll-margin-top:6rem}
@media(max-width:640px){.pcs-viz{margin:1.6rem 0}.pcs-figure{padding:1rem}.pcs-roadmap-grid{gap:.3rem}.pcs-roadmap-grid>a{align-items:center;padding:.65rem .2rem}.pcs-roadmap-number{font-size:1.1rem}.pcs-roadmap-grid strong{font-size:.79rem}.pcs-roadmap-grid small{display:none}.pcs-research-head{display:none}.pcs-research-row{grid-template-columns:1fr;gap:.4rem}.pcs-panels{grid-template-columns:1fr}.pcs-panel-title small{font-size:.62rem}.pcs-timeline{grid-template-columns:repeat(2,minmax(0,1fr));row-gap:1.25rem}}
@media print{.pcs-viz{break-inside:avoid;--pcs-ink:#1e293b!important;--pcs-muted:#475569!important;--pcs-line:#cbd5e1!important;--pcs-bg:#fff!important;--pcs-card:#fff!important;--pcs-accent:#0f766e!important;--pcs-tint:#f1f5f9!important}}
</style>

## 00. 요약 {#pcs-summary}

새 AI 세션이 이전 작업을 이어가도록, **기록할 문서와 읽고 갱신할 순서**를 설계했다.

AI에 메모리 논문·GitHub 사례 조사를 맡기고, 내 작업에 적용할 구조를 선택한 뒤 실사용에서 보완했다.

<nav class="pcs-viz pcs-roadmap" aria-label="글의 다섯 단계">
<span class="pcs-eyebrow">프로젝트 흐름 · 단계를 누르면 해당 내용으로 이동</span>
<div class="pcs-roadmap-grid">
<a href="#pcs-problem"><span class="pcs-roadmap-number">01</span><strong>문제</strong><small>맥락 재설명</small></a>
<a href="#pcs-research"><span class="pcs-roadmap-number">02</span><strong>조사</strong><small>논문·공개 사례</small></a>
<a href="#pcs-design"><span class="pcs-roadmap-number">03</span><strong>설계</strong><small>문서·인계 절차</small></a>
<a href="#pcs-operation"><span class="pcs-roadmap-number">04</span><strong>운영</strong><small>실사용 문제 수정</small></a>
<a href="#pcs-results"><span class="pcs-roadmap-number">05</span><strong>결과</strong><small>적용과 남은 과제</small></a>
</div>
</nav>

## 01. 문제 정의 {#pcs-problem}

처음 필요했던 것은 간단했다. 다음 세션이 지금까지 한 일을 알고, 이어서 할 작업부터 시작하면 됐다. 그래서 직전 세션을 요약해 `context.md`에 남겼다.

프로젝트가 길어지면서 이 파일에 들어가는 내용도 늘었다. 늘 지켜야 할 규칙, 지난 세션에 바꾼 내용, 다음 할 일, 기능을 그렇게 설계한 이유가 함께 쌓였다. 문서를 짧게 다시 정리하는 과정에서는 구체적인 예외와 결정 근거가 조금씩 빠졌다.

포트폴리오에서는 배포 절차나 사이트 구조처럼 오래 참고할 정보도 필요했다. 이런 내용까지 새 세션의 진입 문서에 모으면 매번 읽는 양이 커지고, 별도로 옮기면 어디를 찾아야 하는지 다시 알려줘야 했다. 정보를 보존하는 방법과 다음 세션이 그 정보를 찾는 방법을 함께 정할 필요가 생겼다.

## 02. 연구·사례 조사 {#pcs-research}

비슷한 문제를 다룬 연구와 실제 개발 사례를 찾아보도록 AI에 요청하고, 관련 자료를 LLM Wiki에 정리했다. 조사에서 확인한 생각을 내 프로젝트의 문서 관리 방식으로 연결했다.

<figure class="pcs-viz pcs-figure" aria-labelledby="pcs-research-caption">
<div class="pcs-figure-heading"><span class="pcs-eyebrow">조사 → 설계</span><strong>자료에서 확인한 생각을 작업 규칙으로 옮겼다</strong></div>
<div class="pcs-research-head" aria-hidden="true"><span>풀고 싶었던 문제</span><span>참고한 자료</span><span>내 설계에 반영한 내용</span></div>
<div class="pcs-research-row"><strong>무엇을 남기고 읽을까?</strong><span class="pcs-evidence">MemGPT · Anthropic<br>외부 저장과 필요한 정보의 회수</span><span class="pcs-decision">진입 문서는 짧게 유지<br>상세 문서는 필요할 때 읽기</span></div>
<div class="pcs-research-row"><strong>요약하며 빠지는 정보는?</strong><span class="pcs-evidence">ACE<br>항목 단위 갱신과 누적·정제</span><span class="pcs-decision">이력은 추가해서 보존<br>현재 상태는 따로 갱신</span></div>
<div class="pcs-research-row"><strong>파일은 어떻게 나눌까?</strong><span class="pcs-evidence">Spec Kit · Harper · HANDOFF.md<br>명세·작업 목록·세션 인계 사례</span><span class="pcs-decision">규칙·진행 상태·명세 구분<br>시작·종료 절차 지정</span></div>
<figcaption id="pcs-research-caption">참고 자료와 내 설계 선택의 연결 관계. 세부 내용과 원문 링크는 아래에 정리했다.</figcaption>
</figure>

### 2.1 메모리의 저장과 회수

[MemGPT 논문](https://arxiv.org/abs/2310.08560v2)은 제한된 컨텍스트 창과 외부 저장소 사이에서 정보를 옮기는 계층형 메모리 관점을 제시한다. 여기서 참고한 것은 **저장할 정보의 양과 지금 읽어야 할 정보의 양을 구분하는 방식**이었다. 프로젝트 기록은 파일에 보존하고, 현재 작업에 필요한 부분을 선택해 읽는 구조를 생각할 수 있었다.

[Anthropic의 Context Engineering 글](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)에서는 에이전트가 외부 노트에 상태를 기록하고 나중에 다시 읽는 방법, 파일 경로 같은 참조를 유지하며 필요한 시점에 자료를 불러오는 방법을 확인했다. 이를 내 프로젝트에 적용하면서 `context.md`에는 현재 상태와 관련 문서의 위치를 남기고, 세부 지식은 별도 문서에서 찾도록 설계했다.

### 2.2 요약·갱신과 정보 유실

[ACE 논문](https://arxiv.org/abs/2510.04618v3)은 간결한 요약을 만드는 과정에서 세부 지식이 빠지는 경향과, 문서를 반복 재작성하면서 정보가 침식되는 현상을 다룬다. 구조화된 항목을 추가·수정하며 지식을 누적하고 정제하는 접근을 제시한다.

이 내용은 내가 겪은 문제를 검토하는 근거가 됐다. 짧은 진입 문서와 상세 이력의 갱신 방식을 구분하기로 했다. 현재 상태는 짧게 고치되, 지난 결정과 작업 이력은 별도 파일에 추가하고 필요한 부분만 수정하도록 정했다.

### 2.3 공개 개발 도구와 인계 사례

GitHub에서는 [Spec Kit](https://github.com/github/spec-kit)의 프로젝트 원칙, 기능 명세, 구현 계획, 작업 목록을 나누는 구성을 살펴봤다. 특히 템플릿에 질문과 확인 항목을 넣어, 에이전트가 빠뜨리지 말아야 할 내용을 명시하는 방식을 참고했다.

세션별 진행 상태를 관리하는 사례도 함께 조사했다. [Harper Reed의 작업 방식](https://harper.blog/2025/02/16/my-llm-codegen-workflow-atm/)에서는 명세와 계획을 파일로 남기고 `todo.md`를 체크하면서 상태를 이어간다. [Fazm의 HANDOFF.md 사례](https://fazm.ai/blog/claude-code-architecture-handoff-pattern)는 세션 종료 시 변경 내용과 미완 작업을 기록하고, 다음 세션이 먼저 읽도록 구성한다.

당시 자료를 비교하면서 내게 필요한 역할을 구분했다. 기능 명세와 계획은 무엇을 만들지 설명하지만, 다음 세션에는 어디까지 했고 무엇이 남았는지도 전달해야 했다. 여기에 도구가 지켜야 할 작업 규칙까지 포함해 **규칙, 진행 상태, 명세·지식**을 각각 관리하는 구조로 정리했다.

## 03. 문서와 인계 절차 설계 {#pcs-design}

### 3.1 문서 역할과 읽는 시점

메모리 분야의 procedural·episodic·semantic 분류를 참고해 프로젝트 정보를 나눴다. 각각 절차, 경험, 지식에 관한 기억이다. 내 문서에는 다음과 같이 적용했다.

<div class="pcs-memory" role="region" aria-label="메모리 분류와 프로젝트 문서의 대응">

| 메모리 분류 | 뜻과 질문 | 내 문서 |
|---|---|---|
| **절차 기억**<br>procedural | 행동 방법과 절차<br>“어떻게 일하나?” | **규칙·절차**<br>`AGENTS.md`<br>`HANDOFF-RULES.md` |
| **일화 기억**<br>episodic | 개별 경험과 사건<br>“무슨 일을 했나?” | **상태·이력**<br>`context.md`<br>`todo.md`<br>`progress.md` |
| **의미 기억**<br>semantic | 사실과 개념에 관한 지식<br>“무엇을 알고 있나?” | **명세·설계 지식**<br>`specs/` · `docs/` |

</div>

분류는 정보의 역할을 나누는 기준으로 사용했다. 파일을 나눌 때는 **언제 읽는가**도 함께 봤다. 같은 규칙이라도 매 작업에 필요한 내용과 세션을 끝낼 때만 필요한 내용은 달랐다.

배포법·사이트 구조·자동화 사용법을 `HANDOFF-RULES.md` 한곳에 모으고 필요한 섹션만 검색하는 방안도 검토했다. 하지만 배포, 사이트 수정, 세션 종료는 각각 다른 시점에 일어난다. 해당 작업을 할 때 바로 찾고 수정할 수 있도록 파일을 나눴다. 예를 들어 배포 절차는 `docs/deployment.md`에, 특정 자동화의 사용법은 그 코드 옆 `README.md`에 두었다.

공통 문서는 `AGENTS.md`, `CLAUDE.md`, `context.md`, `todo.md`, `progress.md`, `HANDOFF-RULES.md` 여섯 개로 정리했다. 기능 명세와 운영 참고 자료는 프로젝트에 맞춰 추가한다.

### 3.2 폴더를 나눈 이유

문서의 역할을 정한 뒤에는 어디에 둘지도 결정해야 했다. 프로젝트마다 다음 요구가 겹쳐 있었다.

- **공개 Git 저장소에 넣을 범위:** 제품 코드와 AI 작업 규칙·개인 세션 기록을 따로 관리하고 싶었다. 그래서 제품 하위 폴더만 Git 저장소로 두고, AI 작업 문서는 그 밖에 두는 배치를 마련했다.
- **Windows·WSL 도구의 작업 위치:** AI는 WSL에서 실행했지만, Godot 에디터나 에셋 추출 도구는 Windows에서 사용하는 프로젝트도 있었다. 이런 경우에는 산출물을 Windows 쪽에 두고 WSL의 세션 폴더에서 경로로 연결했다.
- **공유 자료와 개인 작업 기록의 구분:** 위키는 다른 프로젝트에서도 참고하는 자료였다. 공통 규칙과 지식은 위키에, 내 할 일과 세션 기록은 별도 폴더에 두어 개인 진행 상황이 공유 자료와 섞이지 않게 했다.

문서 역할, Git에 올릴 범위, 실제 파일 위치는 각각 결정했다. 단일 코드 저장소에서는 두 역할의 문서를 같은 폴더에 둘 수도 있다. 포트폴리오는 세션 폴더와 산출물 폴더를 나누고, 산출물 안의 `test-page/`만 사이트 저장소로 관리한다.

<figure class="pcs-viz pcs-figure" aria-labelledby="pcs-files-caption">
<div class="pcs-figure-heading"><span class="pcs-eyebrow">파일 구조</span><strong>현재 포트폴리오의 문서 배치</strong></div>
<div class="pcs-panels">
<div class="pcs-panel"><div class="pcs-panel-title"><strong>▾ 세션 작업 폴더</strong><small>CONTROL</small></div><ul class="pcs-tree">
<li><code>├─ context.md</code><span>시작 시 확인할 현재 상태와 문서 위치</span></li>
<li><code>├─ todo.md</code><span>시작·작업 선택 시 읽는 남은 일</span></li>
<li><code>├─ progress.md</code><span>필요한 부분을 찾아 읽는 작업 이력</span></li>
<li><code>└─ HANDOFF-RULES.md</code><span>종료 시 읽는 문서 갱신 절차</span></li>
</ul></div>
<div class="pcs-panel"><div class="pcs-panel-title"><strong>▾ 산출물 폴더</strong><small>ARTIFACT</small></div><ul class="pcs-tree">
<li><code>├─ AGENTS.md</code><span>작업을 재개할 때 읽는 공통 규칙</span></li>
<li><code>├─ CLAUDE.md</code><span>Claude Code에서 공통 규칙을 참조</span></li>
<li><code>├─ constitution.md</code><span>프로젝트의 기본 원칙</span></li>
<li><code>├─ specs/ · docs/</code><span>기능 명세와 운영 참고 자료</span></li>
<li><code>└─ test-page/</code><span>공개 사이트의 Git 관리 범위</span></li>
</ul></div>
</div>
<div class="pcs-pointer"><code>context.md</code> → 산출물 위치 확인 → <code>AGENTS.md</code> 명시적으로 읽기</div>
<figcaption id="pcs-files-caption">현재 배치의 핵심 파일만 표시했다. <code>AGENTS.md</code> 등 AI 작업 문서는 공개 사이트 저장소(<code>test-page/</code>) 밖에 있다.</figcaption>
</figure>

이후 LLM Wiki의 산출물을 Windows에서 WSL로 옮겼을 때도, 세션 기록과 공유 지식을 구분한 구조는 유지했다. 파일 위치를 바꾸어도 문서의 역할과 연결 방식은 그대로 사용할 수 있었다.

### 3.3 기준 문서와 갱신 순서

파일을 나누면서 같은 규칙이 여러 문서에 반복되는 문제도 확인했다. 한쪽만 수정하면 다른 쪽에는 오래된 내용이 남기 때문에, **한 사실의 기준 문서는 한곳으로 정하고 다른 문서에서는 그 위치를 가리키도록** 했다.

예를 들어 기능 구현 중 작성한 계획에는 당시의 선택과 시행착오가 남는다. 구현이 끝난 뒤에도 필요한 운영 방법은 `docs/`에 정리하고, 계획 문서에서는 그 위치를 참조하게 했다. 진행 상황을 기록할 때도 운영 규칙 전체를 다시 적지 않도록 했다.

시작할 때 읽는 순서와 종료할 때 쓰는 순서를 함께 정했다. 종료 시에는 바뀐 규칙과 설계를 먼저 반영하고, 작업 이력을 추가한 뒤 다음 세션의 진입 문서를 갱신한다.

<figure class="pcs-viz pcs-figure" aria-labelledby="pcs-cycle-caption">
<div class="pcs-figure-heading"><span class="pcs-eyebrow">세션 인계 흐름</span><strong>이전 세션이 남긴 기록으로 다음 작업을 시작한다</strong></div>
<div class="pcs-panels">
<div class="pcs-panel"><div class="pcs-panel-title"><strong>세션 시작 · 읽기</strong><small>pickup</small></div><ol class="pcs-sequence">
<li><span><code>context.md</code><br>현재 상태·작업 위치 확인</span></li>
<li><span><code>AGENTS.md</code> · <code>todo.md</code><br>작업 규칙·우선순위 확인</span></li>
<li><span>선택한 작업에 필요한<br>명세·참고 문서·이력 읽기</span></li>
</ol></div>
<div class="pcs-panel"><div class="pcs-panel-title"><strong>세션 종료 · 쓰기</strong><small>hand-off</small></div><ol class="pcs-sequence">
<li><span>바뀐 규칙·설계 지식을<br>해당 내용의 기준 문서에 반영</span></li>
<li><span><code>progress.md</code> · <code>todo.md</code><br>완료 이력 추가·남은 작업 정리</span></li>
<li><span><code>context.md</code><br>다음 세션을 위한 상태·위치 갱신</span></li>
</ol></div>
</div>
<figcaption id="pcs-cycle-caption">종료 절차는 <code>HANDOFF-RULES.md</code>를 따라 실행한다. 상세 기록을 보존하면서 시작 시 읽는 범위를 구분한 구조다.</figcaption>
</figure>

이렇게 갱신 방법을 정해 두면 새 기록이 생길 때마다 어디에 넣을지 처음부터 판단할 필요가 줄어든다. 실제 적용에서는 문서 사이에 같은 내용이 다시 쌓이지 않는지도 계속 확인해야 했다.

### 3.4 시작·종료 명령

문서 구조에 맞춰 세션 시작과 종료 절차를 명령으로 묶었다. Claude Code의 `/pickup`과 `/hand-off`, Codex의 `$pickup`과 `$hand-off`가 그 역할을 한다.

시작할 때는 `context.md`를 직접 읽고, 그 안에 적힌 작업 위치와 재개 절차를 따라 규칙과 우선 작업을 확인한다. 종료할 때는 `HANDOFF-RULES.md`에 따라 바뀐 사실을 각 문서에 반영한다. 에이전트가 다음 세션에 넘길 내용을 임의로 한 문서에 요약하지 않도록 읽기와 쓰기의 순서를 지정했다.

폴더를 분리한 프로젝트에서는 산출물 폴더의 `AGENTS.md`를 명시적으로 읽게 했다. 자동으로 규칙이 로드되는지는 도구와 시작 위치에 따라 달라지기 때문이다.

## 04. 실사용 문제와 개선 {#pcs-operation}

기본 문서 구조를 정한 뒤에도 도구를 바꿔 같은 프로젝트를 이어가는 과정에는 문제가 남았다. 공통 규칙과 도구별 명령을 단계적으로 수정했다.

<figure class="pcs-viz pcs-figure" aria-labelledby="pcs-timeline-caption">
<div class="pcs-figure-heading"><span class="pcs-eyebrow">2026년 운영·개선 기록</span><strong>규칙 통합 이후에도 사용하며 수정했다</strong></div>
<div class="pcs-timeline">
<a href="#pcs-rule-drift"><time datetime="2026-07-06">07.06</time><strong>공통 규칙 통합</strong><span>규칙 사본을 참조 방식으로 변경</span></a>
<a href="#pcs-rule-drift"><time datetime="2026-07-30">07.30</time><strong>명령 간 차이 점검</strong><span>도구별 누락을 양방향으로 보완</span></a>
<a href="#pcs-reading"><time datetime="2026-08-15">08.15</time><strong>재개 시 읽기 제한</strong><span>긴 출력·상세 변경의 과도한 읽기 조정</span></a>
<a href="#pcs-variants"><time datetime="2026-08-28">08.28</time><strong>변형 명령 정리</strong><span>기본 스킬을 읽고 이어서 실행</span></a>
</div>
<figcaption id="pcs-timeline-caption">날짜를 누르면 해당 문제와 수정 과정을 볼 수 있다.</figcaption>
</figure>

### 4.1 도구별 명령의 차이 {#pcs-rule-drift}

초기에는 같은 규칙을 `AGENTS.md`와 `CLAUDE.md`에 각각 두고 수정할 때 둘 다 고쳐야 했다. 7월 6일 LLM Wiki에 표준을 적용하면서, Claude용 규칙 사본 219줄을 `@AGENTS.md`를 참조하는 브리지로 바꿨다. 프로젝트의 공통 규칙은 한 파일에서 관리하게 됐다.

하지만 세션 시작·종료 명령은 도구별 파일 형식이 달라 별도로 유지했다. 7월 30일 양쪽을 비교했을 때 실제 규칙 차이가 발견됐다. Codex로 옮기는 과정에서 추가한 세션 기록과 산출물의 위치 구분 등이 Claude 쪽 명령에는 돌아가지 않았다. 반대로 Codex 쪽에 빠진 세부 절차도 있었다.

이때는 바뀐 명령만 확인하던 방식을 고쳐, 한쪽을 수정하면 다른 도구의 절차도 함께 점검하도록 했다. 다만 도구마다 필요한 내용은 구분했다. Codex용 작업 폴더는 문서별 심볼릭 링크를 사용하는 환경이어서, 파일을 찾지 못하면 링크와 대상 경로를 확인하는 절차가 필요했다.

### 4.2 과도한 문서 읽기 {#pcs-reading}

`context.md`를 짧게 만들면 다음에 필요한 문서를 따라 읽는 절차가 중요해졌다. 그런데 Codex의 실제 재개 과정에서는 목차와 이력 파일의 긴 행을 읽고, 이전 변경의 상세 내용을 재구성하면서 준비 단계부터 많은 컨텍스트를 사용했다.

8월 15일에는 시작 절차의 읽기 범위를 더 구체적으로 제한했다. 할 일은 우선순위가 높은 항목부터, 이력은 관련 부분만 읽게 했다. 긴 행은 명령에서 출력 길이를 제한하고, 재개 중에는 코드 변경의 상세 내용까지 읽지 않도록 했다. 인계 문서와 작업 폴더가 다르면 우선 해당 파일과 차이를 보고하고, 그 작업을 선택한 뒤 자세히 확인하게 했다.

이 보완은 문제가 관찰된 Codex 쪽에 적용했다. 같은 문제가 재현되지 않은 Claude 쪽에는 일괄 복사하지 않았다.

### 4.3 변형 명령의 갱신 누락 {#pcs-variants}

별도 작업 폴더인 Git worktree에서 시작할 때는 세션 기록이 있는 원래 폴더를 먼저 찾아야 했다. 이를 위해 만든 `pickup-orca`·`hand-off-orca` 변형이 7월 설치본에 머물러, 8월에 추가한 기본 명령의 읽기 제한을 받지 못한 문제가 드러났다.

8월 28일에는 변형 명령이 작업 폴더를 찾는 부분을 맡고, 이후에는 기본 스킬을 읽어 이어서 실행하도록 바꿨다. 설치해서 사용하는 명령과 저장소에서 관리하는 원본도 대조해 맞췄다. 기본 절차를 수정했을 때 변형 명령에도 반영될 경로를 만든 것이다.

## 05. 결과와 남은 과제 {#pcs-results}

결과물은 공통 문서 여섯 개, 코드 프로젝트와 지식 저장소에 맞춘 확장 규칙, 도구별 세션 명령으로 정리했다. 2026년 9월 기준 패키지에는 템플릿·가이드·스킬을 포함해 33개 파일이 있다.

적용할 때는 다음을 확인했다.

- 이미 운영 중이던 포트폴리오와 위키에는 기존 규칙을 보존하고 빠진 구조를 추가했다.
- Claude Code·Codex·Antigravity용 시작·종료 절차를 마련하고, 실제 사용에서 발견한 도구별 차이를 반영했다.

배포 기록은 15건이며 같은 저장소에 다시 적용한 기록도 포함한다. 이 숫자는 고유 사용자 수나 운영 효과를 뜻하지 않는다. 설계와 배포·운영 과정을 추적할 수 있도록 관련 대화 39세션도 별도로 보존했다.

지금까지 확인한 것은 여러 작업 환경에 적용할 수 있었고, 인계 과정에서 발생한 문제를 문서와 명령에 반영해 왔다는 점이다. 프로젝트 전체의 생산성 향상이나 정보 유실 방지 효과는 아직 확정할 수 없다.

운영하면서 경로와 규칙이 오래된 채 남거나, 명령을 옮기는 과정에서 수정 사항이 빠질 수 있다는 것도 확인했다. 다음에는 문서 이름·참조 경로·출력량 제한처럼 기계적으로 확인할 수 있는 항목을 자동 검사하는 방법을 검토하려 한다.
