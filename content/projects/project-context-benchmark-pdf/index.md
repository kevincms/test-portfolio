---
title: "Project Context: PDF 벤치마크로 확인한 효과와 한계"
date: 2026-08-26
lastmod: 2026-09-11
summary: "Project Context의 효과를 정량적으로 살펴보기 위해 세션 실행기와 채점기를 만들고 PDF 처리 작업 78세션을 실행했다. 기록 방식에 따른 입력량·절차 관리·정보 보존을 비교하고, 평가의 한계와 후속 과제를 정리했다."
draft: false
tags:
  - Side Project
  - AI Engineering
  - Developer Tools
tech_stack:
  - Python
  - GPT-5.4
  - OpenAI API
  - Markdown
  - YAML
featured: true
status: "Completed"
role: "벤치마크 설계·실행·결과 분석"
duration: "2026.08 · PDF 탐색 실험 완료"
team_size: 1
highlights:
  - "동일한 PDF 13개를 두 기록 방식으로 각각 3회 처리해 총 78세션 비교"
  - "역할 분리 조건의 입력 토큰이 세 반복 모두 낮음(평균 19.5% 감소), 품질 동등성은 판정 불가"
  - "세션 실행기와 요소별 채점기를 만들고, 자체 점검으로 채점기 결함 7개 수정"
  - "절차 재사용 지표의 한계를 정리하고 밈 언어 기반 후속 평가 착수"
---

<style>
main:has(#pcb-summary) :is(h1,h2,h3),body:has(#pcb-summary) .hb-toc a{word-break:keep-all;overflow-wrap:break-word}
main:has(#pcb-summary) :not(pre)>code::before,main:has(#pcb-summary) :not(pre)>code::after{content:none}
.pcb-viz{--pcb-ink:#1e293b;--pcb-muted:#53667a;--pcb-line:#dbe4e8;--pcb-bg:#f8fafb;--pcb-card:#fff;--pcb-accent:#0f766e;--pcb-tint:#eaf7f4;--pcb-baseline:#64748b;color:var(--pcb-ink);margin:2rem 0;font-size:.9rem;line-height:1.65;word-break:keep-all;overflow-wrap:break-word}
.dark .pcb-viz{--pcb-ink:#e2e8f0;--pcb-muted:#a6b5c6;--pcb-line:#334155;--pcb-bg:#17202d;--pcb-card:#111827;--pcb-accent:#5eead4;--pcb-tint:#12352f;--pcb-baseline:#94a3b8}
.pcb-viz *{box-sizing:border-box;min-width:0}
.pcb-viz :where(p,h3,h4,ul,ol,li,figcaption){margin:0}
.pcb-viz :where(ul,ol){padding:0;list-style:none}
.pcb-viz code{padding:0!important;background:transparent!important;color:inherit;font-size:.84em;font-weight:500;overflow-wrap:anywhere}
.pcb-viz code::before,.pcb-viz code::after{content:none}
.pcb-viz a{color:inherit;text-decoration:none!important}
.pcb-viz a:focus-visible{outline:3px solid var(--pcb-accent);outline-offset:4px}
.pcb-viz strong{color:var(--pcb-ink)}
.pcb-eyebrow{display:block;color:var(--pcb-accent);font-size:.7rem;font-weight:700;letter-spacing:.07em;margin-bottom:.4rem}
.pcb-roadmap-grid{display:grid;grid-template-columns:repeat(5,minmax(0,1fr));gap:.5rem}
.pcb-roadmap-grid>a{display:flex;flex-direction:column;gap:.2rem;padding:.85rem .7rem;background:var(--pcb-card);border:1px solid var(--pcb-line);border-radius:10px;transition:border-color .15s,background-color .15s}
.pcb-roadmap-grid>a:hover{border-color:var(--pcb-accent);background:var(--pcb-tint)}
.pcb-roadmap-number{font-family:ui-monospace,monospace;font-size:1.35rem;font-weight:700;color:var(--pcb-accent);line-height:1.2}
.pcb-roadmap-grid strong{font-size:.95rem}
.pcb-roadmap-grid small{color:var(--pcb-muted);font-size:.7rem;line-height:1.45}
.pcb-metrics{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:.7rem}
.pcb-metric{padding:1rem;background:var(--pcb-card);border:1px solid var(--pcb-line);border-radius:12px}
.pcb-metric>span{display:block;color:var(--pcb-muted);font-size:.74rem}
.pcb-metric>strong{display:block;margin:.25rem 0;font-size:1.65rem;line-height:1.3;font-variant-numeric:tabular-nums}
.pcb-metric>small{display:block;color:var(--pcb-muted);font-size:.73rem;line-height:1.55}
.pcb-figure{padding:1.3rem;border:1px solid var(--pcb-line);border-radius:16px;background:var(--pcb-bg)}
.pcb-figure-heading{margin-bottom:1rem}
.pcb-figure-heading>strong{display:block;font-size:1.02rem;line-height:1.5}
.pcb-viz figcaption{margin-top:1rem;color:var(--pcb-muted);font-size:.75rem;line-height:1.65;text-align:left}
.pcb-panels{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:1rem}
.pcb-panel{padding:1rem;background:var(--pcb-card);border:1px solid var(--pcb-line);border-radius:10px}
.pcb-panel-title{display:flex;align-items:baseline;justify-content:space-between;gap:.5rem;margin-bottom:.8rem}
.pcb-panel-title strong{font-size:.92rem}
.pcb-panel-title small{color:var(--pcb-accent);font-family:ui-monospace,monospace;font-size:.7rem;font-weight:700}
.pcb-records li{padding:.5rem .7rem;border-left:2px solid var(--pcb-line);font-size:.8rem;line-height:1.6}
.pcb-records li+li{margin-top:.35rem}
.pcb-records strong,.pcb-records code{display:block}
.pcb-records span{display:block;color:var(--pcb-muted);font-size:.75rem;margin-top:.15rem}
.pcb-panel>p{color:var(--pcb-muted);font-size:.78rem;line-height:1.65}
.pcb-formula{display:flex;align-items:center;justify-content:space-between;gap:.5rem;margin-top:1rem;padding:.85rem;border-radius:10px;background:var(--pcb-tint)}
.pcb-formula>span{flex:1;text-align:center;font-size:.7rem;color:var(--pcb-muted)}
.pcb-formula strong{display:block;color:var(--pcb-accent);font-size:1.15rem;font-variant-numeric:tabular-nums}
.pcb-formula>b{color:var(--pcb-accent);font-size:1rem}
.pcb-chart-row+.pcb-chart-row{margin-top:1.1rem}
.pcb-chart-label{display:flex;align-items:baseline;justify-content:space-between;gap:1rem;margin-bottom:.4rem;font-size:.8rem}
.pcb-chart-label strong{font-variant-numeric:tabular-nums;font-size:1rem}
.pcb-chart-track{height:1.05rem;border-radius:4px;background:var(--pcb-line);overflow:hidden}
.pcb-chart-fill{display:block;width:var(--pcb-share);height:100%;background:var(--pcb-baseline)}
.pcb-chart-row[data-arm="C3"] .pcb-chart-fill{background:var(--pcb-accent)}
.pcb-chart-axis{display:flex;justify-content:space-between;gap:1rem;margin-top:.35rem;color:var(--pcb-muted);font-size:.67rem;font-variant-numeric:tabular-nums}
.pcb-chart-note{margin-top:1rem!important;padding:.8rem;border-left:3px solid var(--pcb-accent);background:var(--pcb-tint);font-size:.8rem}
.pcb-table table{width:100%;table-layout:fixed;font-size:.95rem;line-height:1.65}
.pcb-table :where(th,td){vertical-align:top;word-break:keep-all;overflow-wrap:anywhere}
.pcb-table th:first-child{width:29%}
.pcb-table code{white-space:normal;overflow-wrap:anywhere;font-size:.8rem}
.pcb-table td{font-variant-numeric:tabular-nums}
.pcb-table.pcb-table-num th:first-child{width:auto}
.pcb-status{display:inline-block;margin-bottom:.6rem;padding:.15rem .5rem;border:1px solid var(--pcb-line);border-radius:5px;color:var(--pcb-muted);font-size:.7rem;font-weight:700}
.pcb-status-complete{background:var(--pcb-tint);color:var(--pcb-accent);border-color:var(--pcb-accent)}
[id^="pcb-"]{scroll-margin-top:6rem}
@media(max-width:640px){.pcb-viz{margin:1.6rem 0}.pcb-figure{padding:1rem}.pcb-roadmap-grid{gap:.3rem}.pcb-roadmap-grid>a{align-items:center;padding:.65rem .2rem}.pcb-roadmap-number{font-size:1.1rem}.pcb-roadmap-grid strong{font-size:.79rem}.pcb-roadmap-grid small{display:none}.pcb-metrics{gap:.4rem}.pcb-metric{padding:.8rem .55rem}.pcb-metric>strong{font-size:1.3rem}.pcb-metric>span,.pcb-metric>small{font-size:.67rem}.pcb-panels{grid-template-columns:1fr}.pcb-table table{font-size:.85rem}.pcb-formula{padding:.7rem .4rem;gap:.25rem}.pcb-formula strong{font-size:1rem}.pcb-chart-label{font-size:.75rem}}
@media print{.pcb-viz{break-inside:avoid;--pcb-ink:#1e293b!important;--pcb-muted:#475569!important;--pcb-line:#cbd5e1!important;--pcb-bg:#fff!important;--pcb-card:#fff!important;--pcb-accent:#0f766e!important;--pcb-tint:#f1f5f9!important;--pcb-baseline:#64748b!important}}
</style>

## 00. 요약 {#pcb-summary}

Project Context의 기록 방식이 실제 작업에 어떤 차이를 만드는지 확인하기 위해 **PDF 처리 작업 78세션을 실행했다**. 한 파일에 기록을 모으는 방식과 역할별로 나누는 방식을 비교했다.

역할 분리 조건의 세션당 입력 토큰은 세 번의 반복 모두 한 파일 조건보다 적었고, 평균 **19.5% 낮았다**. 절차도 더 일찍 기록했다. 다만 정보 보존 점수는 세 번 모두 역할 분리 조건이 같거나 낮았고, 절차 재사용의 차이는 이번 지표로 판별하지 못했다. 이 글은 실험을 위해 만든 실행·채점 도구와 결과, 그 결과를 해석할 수 있는 범위를 정리한 기록이다.

<div class="pcb-viz pcb-metrics" role="group" aria-label="실험의 규모와 핵심 결과">
<div class="pcb-metric"><span>실행 규모</span><strong>78세션</strong><small>13문서 × 2조건 × 3반복</small></div>
<div class="pcb-metric"><span>평균 입력 토큰</span><strong>−19.5%</strong><small>C2 대비 C3 · 3회 모두 감소</small></div>
<div class="pcb-metric"><span>정보 보존 점수</span><strong>C3 ≤ C2</strong><small>3회 모두 · 최대 차이 0.034</small></div>
</div>

<nav class="pcb-viz" aria-label="글의 다섯 단계">
<span class="pcb-eyebrow">실험 흐름 · 단계를 누르면 해당 내용으로 이동</span>
<div class="pcb-roadmap-grid">
<a href="#pcb-motivation"><span class="pcb-roadmap-number">01</span><strong>동기</strong><small>효과를 숫자로</small></a>
<a href="#pcb-design"><span class="pcb-roadmap-number">02</span><strong>설계</strong><small>조건 · 측정 도구</small></a>
<a href="#pcb-results"><span class="pcb-roadmap-number">03</span><strong>결과</strong><small>입력량·절차·품질</small></a>
<a href="#pcb-limits"><span class="pcb-roadmap-number">04</span><strong>한계</strong><small>평가의 해석 범위</small></a>
<a href="#pcb-next"><span class="pcb-roadmap-number">05</span><strong>후속</strong><small>다음 평가로 연결</small></a>
</div>
</nav>

## 01. 운영 경험을 정량적 지표로 확인하기 {#pcb-motivation}

앞선 프로젝트 [「Project Context: 새 AI 세션에 작업 맥락을 넘기는 방법」]({{< relref "/projects/project-context-standard" >}})에서는 기록할 문서의 역할과 세션 시작·종료 절차를 설계했다. 실제 프로젝트에 적용하며 규칙이 어긋나거나 필요 이상의 문서를 읽는 문제도 수정했다.

그다음에는 이 방식이 어떤 효과를 내는지 숫자로 설명하고 싶었다. 문서를 나누면 매번 읽는 입력량이 줄어드는지, 앞서 정리한 절차를 다음 작업에서 활용하는지, 결과물의 정보는 충분히 보존되는지 확인할 필요가 있었다.

첫 평가 대상으로 PDF 처리 작업을 선택했다. 정답 정보가 있는 합성 PDF를 사용하면 표·수식·도표의 정보가 결과물에 얼마나 남았는지 살펴볼 수 있다. 같은 자료를 연속으로 처리하면서 기록 방식에 따라 입력량과 절차 관리 행동이 어떻게 달라지는지도 비교했다.

## 02. PDF 탐색 실험 설계 {#pcb-design}

### 2.1 비교한 두 기록 방식

조건 이름은 벤치마크 설계의 번호를 그대로 썼다. **C2**는 `AGENTS.md` 한 파일에 기록을 모으고, **C3**는 `AGENTS.md`에 진입 규칙과 포인터만 두고 상태·지식을 역할별 문서로 나누어 보존한다.

비교의 초점은 **작업 중 남긴 기록을 어떤 구조로 관리하는가**였다. 설계에는 기록 파일이 없는 C0, 예전 방식인 `context.md` 한 장을 쓰는 C1, 규칙만 고정해 주는 C-static 같은 기준 조건도 있지만, 이번 탐색 실험에서는 C2와 C3만 실행했다.

<figure class="pcb-viz pcb-figure" aria-labelledby="pcb-structure-caption">
<div class="pcb-figure-heading"><span class="pcb-eyebrow">C2 ↔ C3 · 기록 구조 비교</span><strong>같은 PDF 작업에서 기록의 배치를 달리했다</strong></div>
<div class="pcb-panels">
<div class="pcb-panel"><div class="pcb-panel-title"><strong>한 파일에 누적</strong><small>C2</small></div><ul class="pcb-records"><li><code>AGENTS.md</code><span>작업에 필요한 기록을 한 파일에 보존</span></li></ul></div>
<div class="pcb-panel"><div class="pcb-panel-title"><strong>역할별로 분리</strong><small>C3</small></div><ul class="pcb-records"><li><code>AGENTS.md</code><span>진입 규칙과 역할별 문서로 가는 포인터</span></li><li><strong>상태·지식 문서</strong><span>기록을 역할에 맞는 문서로 나누어 보존</span></li></ul></div>
</div>
<div class="pcb-formula" aria-label="13문서 곱하기 2조건 곱하기 3반복은 78세션">
<span><strong>13</strong>PDF 문서</span><b aria-hidden="true">×</b><span><strong>2</strong>기록 조건</span><b aria-hidden="true">×</b><span><strong>3</strong>반복 실행</span><b aria-hidden="true">=</b><span><strong>78</strong>전체 세션</span>
</div>
<figcaption id="pcb-structure-caption">기록 구조의 개념도. 각 조건을 빈 상태에서 시작해 동일한 13문서 묶음을 처리하고, 이를 3회 반복했다.</figcaption>
</figure>

### 2.2 실행 범위

<div class="pcb-table" role="region" aria-label="실험 실행 범위">

| 항목 | 설정 |
|---|---|
| 실험 이름 | PDF Arc Probe v0 |
| 입력 자료 | 합성 PDF 13개, 모두 1페이지 |
| 독립 자료 묶음 | 1개 — 같은 13문서 묶음을 반복 사용 |
| 모델 | GPT-5.4 (`reasoning_effort=none`, `temperature=0`) |
| 에이전트 도구 | `read_file`·`list_dir`·`write_file`·`bash` 4종 |
| 조건·반복 | C2와 C3, 조건별 3회 반복 |
| 실행 규모 | 조건별 39세션, 총 78세션 |
| 실행 상태 | 78세션 모두 정상 종료, 실패·실행 상한 도달·누출 없음으로 기록 |

</div>

반복 실행을 통해 같은 조건에서도 결과가 얼마나 달라지는지 살펴봤다. 다만 같은 자료를 재사용했으므로, 이 반복이 서로 다른 문서 묶음에 대한 검증을 대신하지는 않는다.

### 2.3 무엇을 측정했나

<div class="pcb-table" role="region" aria-label="평가 항목과 확인하려던 내용">

| 평가 항목 | 측정 내용 | 확인하려던 것 |
|---|---|---|
| 입력량 | 세션당 입력 토큰, 단계 수, 단계당 입력량 | 기록 방식에 따른 모델 입력량 차이 |
| 절차 관리 | 절차를 처음 기록한 시점, 갱신 횟수 | 작업 방법을 언제부터 기록·관리하는가 |
| 절차 재사용 | 엄격·의미·행동 기반 재사용 판정 | 기록한 절차가 이후 행동으로 이어지는가 |
| 정보 보존 | 표·수식·도표 정보의 회수율(fidelity recall) | 원문 정보가 결과물에 얼마나 남았는가 |

</div>

정보 보존 점수는 결과물의 모든 품질을 대표하지는 않는다. 입력량이 줄 때 원문 정보까지 함께 줄어드는지 확인하기 위한 기준이었고, 채점 방식은 다음 절에 정리했다.

### 2.4 실험을 위해 만든 도구

세션을 같은 조건으로 반복 실행하고 결과를 같은 기준으로 채점하기 위해 Python으로 실행기와 채점기를 만들었다.

- **세션 실행기** — OpenAI API로 모델을 호출하고 파일 읽기·목록·쓰기와 셸 도구만 제공했다. 세션이 끝나면 조건별 기록 파일만 다음 세션으로 넘겼고, 세션마다 입력 토큰·단계 수·도구 결과 크기를 기록했다. 세션이 정답 자료나 다른 세션의 파일에 접근하지 않았는지도 따로 검사했다.
- **정답이 있는 입력 자료** — 공개 벤치마크 [pdf-parse-bench](https://github.com/phorn1/pdf-parse-bench)(MIT)의 생성기를 확장해 합성 PDF를 만들었다. 문서를 만들 때 표·수식·도표의 정답 자료도 함께 생긴다.
- **요소별 채점** — 표는 셀 값과 행·열 제목–값 관계, 수식은 LaTeX 구문 트리(pylatexenc)의 구조와 기호, 도표는 유형에 따라 코드 줄·노드와 연결·기재된 사실을 비교해 회수율을 계산했다. 채점 전에는 HTML 태그·코드 블록 표기·표 구분선을 걷어 내, 출력 형식의 선택이 정보 보존 점수를 흔들지 않게 했다.
- **채점기 자체 점검** — 정답을 그대로 Markdown으로 옮겨 채점하면 정답과 비교하는 모든 점수가 1.0이어야 한다. 이 성질로 채점기를 점검해 병합 셀 파싱 오류 등 결함 7개를 찾아 고쳤다. 렌더링되지 않는 LaTeX 주석도 분모에서 뺐다. 그대로 두면 표 자료의 한 문서에서는 recall의 18%가 처음부터 얻을 수 없는 점수가 됐다.

## 03. 관찰한 결과 {#pcb-results}

### 3.1 평균 입력량은 C3에서 낮았다

조건별 39세션을 합쳐 계산한 평균 입력량은 C2 **81,841 tokens/session**, C3 **65,900 tokens/session**이었다. C2 대비 C3의 입력량이 약 **19.5% 낮게 관찰됐다**.

<figure class="pcb-viz pcb-figure" aria-labelledby="pcb-input-caption">
<div class="pcb-figure-heading"><span class="pcb-eyebrow">평균 입력 토큰 / 세션</span><strong>81,841 → 65,900</strong></div>
<div class="pcb-chart-row" data-arm="C2"><div class="pcb-chart-label"><span>C2 · 한 파일에 누적</span><strong>81,841</strong></div><div class="pcb-chart-track" aria-hidden="true"><span class="pcb-chart-fill" style="--pcb-share:100%"></span></div></div>
<div class="pcb-chart-row" data-arm="C3"><div class="pcb-chart-label"><span>C3 · 역할별 분리</span><strong>65,900</strong></div><div class="pcb-chart-track" aria-hidden="true"><span class="pcb-chart-fill" style="--pcb-share:80.5%"></span></div></div>
<div class="pcb-chart-axis" aria-hidden="true"><span>0</span><span>81,841 tokens/session</span></div>
<p class="pcb-chart-note"><strong>C3 평균 입력량 −19.5%.</strong> 반복별 감소 폭은 13.9–25.8%였다.</p>
<figcaption id="pcb-input-caption">조건별 39세션의 통합 평균을 0에서 시작하는 같은 척도로 표시했다. 측정 대상은 모델 입력 토큰이며, 전체 요금이나 프로젝트 생산성의 개선율을 뜻하지 않는다.</figcaption>
</figure>

반복별 수치는 아래와 같다. 세 번 모두 같은 방향이었지만, 같은 문서 묶음을 반복한 결과이므로 독립된 세 표본으로 보지는 않았다.

<div class="pcb-table pcb-table-num" role="region" aria-label="반복별 세션당 평균 입력 토큰">

| 반복 | C2 | C3 | C3 변화 |
|---|---:|---:|---:|
| 1회차 | 80,488 | 69,286 | −13.9% |
| 2회차 | 80,792 | 59,955 | −25.8% |
| 3회차 | 84,243 | 68,460 | −18.7% |
| 전체 평균 | 81,841 | 65,900 | −19.5% |

</div>

세부적으로 C3의 단계 수는 6.0%(세 반복 모두 감소), 단계당 입력량은 14.4% 낮았다. 입력량 차이가 단계 수의 감소만으로 설명되지는 않았다. 다만 단계당 입력에는 문서뿐 아니라 도구 실행 결과와 누적 대화도 포함되므로, 이 수치를 문서를 읽는 양의 감소율로 그대로 볼 수는 없다.

### 3.2 C3는 절차를 일찍 기록하고 더 자주 갱신했다

<div class="pcb-table" role="region" aria-label="절차 관리와 저장량 비교">

| 관찰 항목 | C2 | C3 |
|---|---|---|
| 절차를 처음 기록한 세션 | 반복별 7·1·1번째 | 반복별 1·1·1번째 |
| 절차 갱신 횟수 | 총 11회 | 총 23회 |
| 최종 파일 수 | 1개 | 6개 |
| 최종 기록 용량 | 반복별 4.3·4.9·5.0KB | 반복별 4.6·7.2·7.6KB |
| 노트 쓰기 호출 | 반복별 8·10·12회 | 반복별 46·43·34회 |

</div>

C3는 모든 반복에서 첫 세션부터 절차를 기록했다. C2도 세 번 모두 절차를 형성했으며, 그중 한 번은 7번째 세션에 처음 기록했다. 따라서 차이는 절차 형성의 성공 여부보다 **형성 시점과 관리 빈도**에 있었다.

또한 C3에서는 더 많은 기록을 보존하면서 평균 입력량은 낮아지는 패턴이 나타났다. 저장할 정보와 현재 세션에 읽힐 정보를 분리할 가능성을 보여주는 관찰이다. 한편 노트 쓰기 호출은 C3가 약 4배 많았으므로 기록을 유지하는 작업량도 함께 살펴야 한다. 다만 실제로 쓴 바이트 수와 출력 토큰은 3회차에서 C2가 더 많아, 쓰기 비용 전체가 항상 C3에서 크다고 보기는 어려웠다.

### 3.3 품질과 재사용은 판정하지 못했다

원문 정보 보존 점수인 **fidelity recall은 세 번 모두 C3가 C2 이하**였다. 차이는 0–0.034로 크지 않았고, 3회차는 반올림한 값이 같았다. 그러나 어느 정도의 차이까지 같은 품질로 볼지 미리 정하지 않았으므로(4.2), 입력 토큰이 낮았다는 결과만으로 같은 품질을 더 적은 비용으로 얻었다고 결론 내릴 수는 없었다.

<div class="pcb-table pcb-table-num" role="region" aria-label="반복별 fidelity recall">

| 반복 | C2 | C3 |
|---|---:|---:|
| 1회차 | 0.926 | 0.892 |
| 2회차 | 0.937 | 0.928 |
| 3회차 | 0.930 | 0.930 |

</div>

정답에 없는 내용을 만든 양(false positives)과 형식 일관성은 반복마다 우위가 바뀌었다.

절차를 재사용했는지에 대한 판정도 분명하지 않았다. 엄격한 재사용 기준에서는 점수가 바닥에, 의미 기반 기준에서는 천장에 몰렸다. 행동 기반 판정 등 다른 지표의 방향도 반복마다 달랐다.

이번 실험에서는 절차를 빨리 기록하고 자주 갱신하는 행동을 관찰했다. 그러나 **기록한 절차가 이후 작업에서 더 잘 재사용됐다는 결론**까지 이어지지는 않았다.

## 04. 평가하면서 확인한 한계 {#pcb-limits}

### 4.1 기록·재사용·품질은 따로 확인해야 했다

절차 문서를 만들었다는 사실, 그 문서를 이후에 활용했다는 사실, 활용한 결과가 좋았다는 사실은 각각 다른 관찰이다. 갱신 횟수나 파일 수가 늘어도 재사용과 품질이 함께 좋아졌다고 볼 수 없다.

특히 재사용 지표는 기준에 따라 점수가 바닥이나 천장에 몰려 두 조건의 차이를 가르지 못했다. 더 분명히 비교하려면 재사용의 정의와 채점 기준부터 보완해야 했다.

### 4.2 입력량 차이의 원인과 품질 기준이 남았다

기록을 역할별로 나누는 방식은 낮은 입력량과 함께 관찰됐다. 그러나 단계 수, 도구 사용 경로, 노트를 읽는 행동의 차이도 영향을 줄 수 있어 역할 분리 자체의 효과를 따로 입증하지는 못했다. 예를 들어 도구가 돌려준 결과의 평균 크기는 2·3회차에서 C3가 작았지만 1회차에서는 컸다. 작은 도구 결과가 감소분의 일부를 설명할 수는 있어도 세 반복 전체를 설명하지는 못했다.

또한 “품질이 이 정도까지 차이 나면 동등하다고 볼 것인가”에 대한 허용 범위를 실험 전에 정하지 않았고, 조건을 가린 사람 평가도 없었다. 따라서 입력량 감소와 품질의 동등성을 함께 확정할 근거는 부족했다.

### 4.3 78세션의 적용 범위는 제한적이었다

전체 실행은 78세션이지만 **독립적인 자료 묶음은 하나**였다. 모든 PDF가 1페이지였고, 모델도 GPT-5.4 하나였다. 다양한 문서와 모델에 같은 결과가 나타나는지는 별도 검증이 필요하다.

시간이 흐를수록 입력 비용이 완만해지는지도 반복별 방향이 일치하지 않았다. 저장된 노트 자체의 장기 정보 보존율을 평가한 실험도 아니므로, 긴 프로젝트에서 맥락 유실을 막는 효과까지 일반화하지 않았다.

## 05. PDF 실험을 마치고 다음 평가로 {#pcb-next}

### 5.1 완료한 실험과 진행 중인 후속

PDF 실험에서는 입력량, 절차 관리 행동, 결과물의 정보 보존을 함께 비교할 수 있는 측정 기록을 만들었다. 동시에 절차 재사용을 판별하는 지표와 결과의 해석 범위에 부족함이 있다는 것도 확인했다.

이 부족함을 바탕으로 **밈 언어 기반 평가를 새로 시작했다**. 밈 표현으로 프로그램을 작성하는 작은 가상 언어를 만들고, 그 언어로 작업을 수행하게 하는 평가다. 현재 진행 중이며, 이 페이지의 수치는 먼저 완료된 PDF 실험에 한정한다.

<figure class="pcb-viz pcb-figure" aria-labelledby="pcb-followup-caption">
<div class="pcb-figure-heading"><span class="pcb-eyebrow">설계 → 측정 → 후속 평가</span><strong>첫 측정에서 얻은 결과와 남은 질문을 이어 간다</strong></div>
<div class="pcb-panels">
<div class="pcb-panel"><span class="pcb-status pcb-status-complete">완료</span><div class="pcb-panel-title"><strong>PDF 탐색 실험</strong><small>01</small></div><p>78세션의 입력량·절차 관리·정보 보존 비교. 재사용 지표와 일반화 범위의 한계 정리.</p></div>
<div class="pcb-panel"><span class="pcb-status">진행 중</span><div class="pcb-panel-title"><strong>밈 언어 기반 평가</strong><small>02</small></div><p>PDF 평가에서 느낀 부족함을 바탕으로 시작한 후속 작업. 완료된 결과는 아직 없다.</p></div>
</div>
<figcaption id="pcb-followup-caption">Project Context의 문서 구조를 설계한 뒤, 효과를 측정하고 평가 방식을 다시 보완하는 과정이다.</figcaption>
</figure>

### 5.2 기록 기준

이 글은 내부 실험 보고서 「**PDF Arc Probe v0**」(2026-08-26)와 LLM Wiki에 정리한 검토 내용을 바탕으로 작성했다. 글에 쓴 집계 수치는 반복별 평균을 포함해 [요약 데이터(YAML)](results-summary.yaml)로 함께 남겼다. 요약 데이터에는 원시 세션 로그나 세션별 측정값은 포함하지 않았다.

문서 구조를 설계한 배경은 앞선 글 [「Project Context: 새 AI 세션에 작업 맥락을 넘기는 방법」]({{< relref "/projects/project-context-standard" >}})에서 확인할 수 있다.
