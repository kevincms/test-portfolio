---
title: "Project Context: PDF 벤치마크로 확인한 효과와 한계"
date: 2026-08-26
lastmod: 2026-09-16
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
main:has(#pcb-summary) :is(h1,h2,h3),body:has(#pcb-summary) .hb-toc a{word-break:keep-all;overflow-wrap:break-word;text-wrap:balance}
main:has(#pcb-summary) :is(h2,h3){scroll-margin-top:6rem}
main:has(#pcb-summary) .prose p{word-break:keep-all;overflow-wrap:break-word}
main:has(#pcb-summary) :not(pre)>code:before,main:has(#pcb-summary) :not(pre)>code:after{content:none}
.pbv{--ink:#233343;--muted:#586b7d;--line:#d5e0e7;--paper:#fff;--wash:#f3f7fa;--teal:#087e80;--teal-bg:#e3f4ef;--blue:#356aba;--blue-bg:#edf2fd;--amber:#936018;--amber-bg:#fff3dc;--c2:#74849b;--c3:#087e80;color:var(--ink);font-size:15px;line-height:1.65;word-break:keep-all;overflow-wrap:break-word;margin:1.8rem 0}
.dark .pbv{--ink:#e3edf5;--muted:#acbacc;--line:#354554;--paper:#15202d;--wash:#101a25;--teal:#78dbcc;--teal-bg:#173b39;--blue:#a3bdff;--blue-bg:#202f4b;--amber:#f2c67b;--amber-bg:#382e21;--c2:#a4b2c8;--c3:#78dbcc}
.pbv *{box-sizing:border-box;min-width:0}
.pbv :is(p,h3,h4,ul,ol,li,figure,figcaption){margin:0}
.pbv :is(ul,ol){list-style:none;padding:0}
.pbv strong,.pbv b{color:inherit}
.pbv code{background:transparent!important;padding:0!important;color:inherit;font:.84em ui-monospace,SFMono-Regular,Consolas,monospace;white-space:normal;overflow-wrap:anywhere}
.pbv code:before,.pbv code:after{content:none!important}
.pbv a{color:inherit;text-decoration:none!important}
.pbv :is(a,summary):focus-visible{outline:3px solid var(--teal);outline-offset:5px}
.pbv-icon{display:block;flex-shrink:0;width:44px;height:44px;fill:none;stroke:currentColor;stroke-width:1.6;stroke-linecap:round;stroke-linejoin:round}
.pbv-board{padding:1.5rem;background:var(--wash);border:1px solid var(--line);border-radius:18px}
.pbv-kicker{display:block;color:var(--teal);font-size:11px;font-weight:700;letter-spacing:.075em;margin-bottom:.5rem}
.pbv-title{display:block;font-size:19px;line-height:1.5;letter-spacing:-.03em;margin-bottom:1.3rem}
.pbv figcaption{font-size:12px;line-height:1.7;color:var(--muted);margin-top:1rem}
.pbv-arrow{font-size:24px;line-height:1;color:var(--muted);text-align:center}
.pbv-tag{display:inline-flex;align-items:center;gap:.35rem;padding:.2rem .55rem;border:1px solid var(--line);border-radius:5px;font:11px ui-monospace,monospace;color:var(--muted);background:var(--paper)}
.pbv-muted{color:var(--muted)}
.pbv-roadmap{display:grid;grid-template-columns:repeat(5,1fr);gap:.35rem;border-block:1px solid var(--line);padding:.65rem 0}
.pbv-roadmap a{padding:.35rem;text-align:center;border-radius:5px;font-size:13px;font-weight:600;transition:background .15s}
.pbv-roadmap a:hover{background:var(--teal-bg)}
.pbv-roadmap span{font:11px ui-monospace,monospace;color:var(--teal);margin-right:.4rem}
.pbv-roadmap small{display:block;font-size:11px;font-weight:400;color:var(--muted);margin-top:.15rem}
.pbv-summary{display:grid;grid-template-columns:repeat(3,1fr);border-block:1px solid var(--line);padding:1.2rem 0;gap:1rem;text-align:center}
.pbv-summary>div+div{border-left:1px solid var(--line)}
.pbv-summary b{display:block;font-size:30px;line-height:1.4;letter-spacing:-.035em;font-variant-numeric:tabular-nums;color:var(--teal)}
.pbv-summary span,.pbv-summary small{display:block;font-size:12px;color:var(--muted)}
.pbv-summary small{font-size:11px;margin-top:.35rem}
.pbv-summary>div:last-child b{color:var(--amber)}
.pbv-overview{display:grid;grid-template-columns:1.15fr 25px .9fr 25px 1fr;align-items:center;gap:.5rem;text-align:center;padding:.5rem 0}
.pbv-overview strong{display:block;font-size:14px;margin-top:.75rem}
.pbv-overview small{display:block;font-size:11px;color:var(--muted);margin-top:.25rem}
.pbv-papers{position:relative;display:flex;justify-content:center;height:118px;padding-top:5px}
.pbv-paper{width:70px;height:97px;border:1px solid var(--line);border-radius:4px 10px 4px 4px;background:var(--paper);color:var(--teal);padding:10px 8px;box-shadow:3px 4px 0 var(--line)}
.pbv-paper:nth-child(1){transform:rotate(-10deg) translate(12px,5px)}
.pbv-paper:nth-child(2){z-index:1;transform:translateY(-5px)}
.pbv-paper:nth-child(3){transform:rotate(10deg) translate(-12px,5px)}
.pbv-paper .pbv-icon{width:100%;height:56px}.pbv-paper span{display:block;font-size:9px;text-align:center}
.pbv-runner{display:flex;align-items:center;justify-content:center;flex-direction:column;height:118px;color:var(--teal)}
.pbv-runner .pbv-icon{width:66px;height:66px}
.pbv-runner code{display:block;padding:.3rem .5rem!important;background:var(--teal-bg)!important;border-radius:5px;margin-top:.3rem;font-size:10px}
.pbv-output{position:relative;width:124px;max-width:100%;height:118px;margin:auto;padding:13px 14px;border:1px solid var(--line);border-radius:7px;background:var(--paper);text-align:left}
.pbv-output code{display:block;color:var(--blue);font-size:11px;margin-bottom:12px}
.pbv-output-line{height:3px;background:var(--line);margin:8px 0;border-radius:2px}
.pbv-output-line:nth-child(3){width:72%}.pbv-output-line:nth-child(5){width:83%}
.pbv-output-mark{position:absolute;right:-6px;bottom:-6px;background:var(--teal-bg);color:var(--teal);border:1px solid var(--teal);border-radius:6px;font-size:11px;padding:4px 6px}
.pbv-pair{display:grid;grid-template-columns:repeat(2,1fr);gap:1rem}
.pbv-condition{padding:1.1rem;background:var(--paper);border:1px solid var(--line);border-radius:12px}
.pbv-condition-head{display:flex;align-items:baseline;gap:.6rem;margin-bottom:1.2rem}.pbv-condition-head b{font:600 15px ui-monospace,monospace;color:var(--c2)}
.pbv-condition[data-arm=C3] .pbv-condition-head b{color:var(--c3)}
.pbv-condition-head strong{font-size:14px}.pbv-file{position:relative;padding:1rem;border:1px solid var(--line);border-radius:5px;background:var(--wash)}
.pbv-file:before{content:"";position:absolute;right:0;top:0;width:13px;height:13px;border-bottom:1px solid var(--line);border-left:1px solid var(--line);background:var(--paper)}
.pbv-file code{display:block;font-size:12px;margin-bottom:.65rem}.pbv-file ul{display:grid;gap:.3rem;font-size:12px;color:var(--muted)}
.pbv-file li{border-left:2px solid var(--c2);padding:.25rem .5rem;background:var(--paper)}
.pbv-file-entry{border-color:var(--teal);background:var(--teal-bg)}.pbv-file-entry code{color:var(--teal);margin-bottom:.25rem}.pbv-file-entry span{font-size:12px}
.pbv-branches{position:relative;display:grid;grid-template-columns:1fr 1fr;gap:.65rem;padding-top:1.5rem}
.pbv-branches:before{content:"";position:absolute;top:0;left:50%;height:.75rem;border-left:1px solid var(--teal)}
.pbv-branches:after{content:"";position:absolute;top:.75rem;left:25%;right:25%;height:.75rem;border:1px solid var(--teal);border-bottom:0}
.pbv-branch{border:1px solid var(--line);background:var(--wash);border-radius:5px;text-align:center;padding:.7rem .2rem}
.pbv-branch .pbv-icon{width:29px;height:29px;margin:0 auto .35rem;color:var(--teal)}.pbv-branch strong{display:block;font-size:12px}.pbv-branch small{display:block;font-size:10px;color:var(--muted);margin-top:.2rem}
.pbv-material{display:grid;grid-template-columns:auto 1fr;gap:1.1rem;align-items:center;padding:1rem;background:var(--paper);border:1px solid var(--line);border-radius:10px}
.pbv-material>b{font-size:39px;line-height:1.2;color:var(--teal);font-variant-numeric:tabular-nums}
.pbv-material strong{display:block;font-size:14px}.pbv-material small{display:block;color:var(--muted);font-size:12px;margin-top:.2rem}
.pbv-document-row{display:grid;grid-template-columns:repeat(13,1fr);gap:5px;margin:1rem 0}
.pbv-document{display:flex;flex-direction:column;align-items:center;gap:3px;color:var(--muted);font:9px ui-monospace,monospace}
.pbv-document .pbv-icon{width:100%;height:36px;color:var(--teal)}
.pbv-repeats{display:grid;grid-template-columns:repeat(3,1fr);gap:.6rem;border-top:1px solid var(--line);padding-top:1rem}
.pbv-repeat{padding:.75rem;background:var(--paper);border:1px solid var(--line);border-radius:8px}
.pbv-repeat strong{display:block;font-size:12px;margin-bottom:.65rem}.pbv-repeat span{display:block;font:11px ui-monospace,monospace;padding:.3rem .4rem;background:var(--wash);border-left:3px solid var(--c2)}
.pbv-repeat span+span{margin-top:.4rem;border-left-color:var(--c3)}
.pbv-scope-total{display:flex;flex-wrap:wrap;align-items:baseline;justify-content:space-between;gap:.4rem;margin-top:1rem;font-size:12px;color:var(--muted)}
.pbv-scope-total strong{font-size:26px;color:var(--teal);font-variant-numeric:tabular-nums}
.pbv-specs{display:flex;gap:.45rem;flex-wrap:wrap;padding-top:1rem;margin-top:1rem;border-top:1px solid var(--line)}
.pbv-measures{display:grid;grid-template-columns:repeat(2,1fr);gap:1.2rem 1.5rem}
.pbv-measure{display:grid;grid-template-columns:42px 1fr;gap:.8rem;align-items:start}
.pbv-measure .pbv-icon{width:38px;height:38px;color:var(--teal)}
.pbv-measure:nth-child(2) .pbv-icon{color:var(--blue)}.pbv-measure:nth-child(3) .pbv-icon{color:var(--amber)}
.pbv-measure strong{display:block;font-size:15px;margin-bottom:.35rem}.pbv-measure p{font-size:12px;color:var(--muted)}.pbv-measure small{display:block;font-size:11px;margin-top:.6rem}
.pbv-tools{display:grid;grid-template-columns:1fr 24px 1fr 24px 1fr;gap:.4rem;align-items:center}
.pbv-tool{align-self:stretch;padding:1rem .75rem;border:1px solid var(--line);border-radius:10px;background:var(--paper)}
.pbv-tool .pbv-icon{width:43px;height:43px;color:var(--teal);margin-bottom:1rem}.pbv-tool strong{display:block;font-size:14px}.pbv-tool p{font-size:12px;color:var(--muted);margin-top:.4rem;line-height:1.7}
.pbv-tool-index{float:right;font:10px ui-monospace,monospace;color:var(--muted)}
.pbv-oracle{display:flex;align-items:center;justify-content:space-between;gap:.7rem;margin-top:1rem;border:1px solid var(--teal);border-radius:8px;background:var(--teal-bg);padding:.85rem 1rem}
.pbv-oracle p{font-size:12px}.pbv-oracle strong{color:var(--teal)}.pbv-oracle b{flex-shrink:0;white-space:nowrap;font:600 25px ui-monospace,monospace;color:var(--teal)}
.pbv-chart-legend{display:flex;flex-wrap:wrap;gap:.9rem;font-size:11px;color:var(--muted);margin-bottom:1rem}
.pbv-chart-legend span{display:flex;align-items:center;gap:5px}.pbv-key{width:10px;height:10px;border-radius:2px;background:var(--c2)}.pbv-key-c3{background:var(--c3)}
.pbv-chart-group+.pbv-chart-group{margin-top:1.2rem}
.pbv-chart-group-head{display:flex;align-items:baseline;justify-content:space-between;gap:.7rem;margin-bottom:.5rem;font-size:12px}
.pbv-chart-group-head b{font-size:15px;color:var(--teal);font-variant-numeric:tabular-nums}
.pbv-chart-group-head .pbv-gap{font-size:11px;color:var(--muted);font-weight:400}
.pbv-bar-row{display:grid;grid-template-columns:24px minmax(0,1fr) 57px;gap:.65rem;align-items:center;font:12px ui-monospace,monospace;line-height:1.4}
.pbv-bar-row+.pbv-bar-row{margin-top:7px}
.pbv-bar-label{color:var(--muted);font-size:11px}.pbv-bar-row[data-arm=C3] .pbv-bar-label{color:var(--teal)}
.pbv-bar-row b{text-align:right;font-weight:500;font-variant-numeric:tabular-nums}
.pbv-bar-track{height:16px;border-left:1px solid var(--line);border-right:1px solid var(--line);background:linear-gradient(to right,transparent calc(50% - .5px),var(--line) calc(50% - .5px),var(--line) calc(50% + .5px),transparent calc(50% + .5px)),var(--paper)}
.pbv-bar{display:block;height:100%;width:var(--share);background:var(--c2);border-radius:0 3px 3px 0}
.pbv-bar-row[data-arm=C3] .pbv-bar{background:var(--c3)}
.pbv-axis{display:flex;justify-content:space-between;gap:.2rem;margin:6px 67px 0 35px;color:var(--muted);font:10px ui-monospace,monospace}
.pbv-axis-label{font-size:11px;text-align:right;color:var(--muted);margin-top:.5rem!important}
.pbv-pooled{padding-bottom:1.2rem;border-bottom:1px solid var(--line);margin-bottom:1.2rem}
.pbv-pooled .pbv-chart-group-head{font-size:14px}.pbv-pooled .pbv-chart-group-head b{font-size:26px}.pbv-pooled .pbv-bar-track{height:23px}
.pbv-tradeoff{display:flex;flex-wrap:wrap;gap:.4rem 1.5rem;border-top:1px solid var(--line);padding-top:1rem;margin-top:1rem;font-size:12px;color:var(--muted)}
.pbv-tradeoff strong{color:var(--teal)}
.pbv-first{display:grid;gap:.8rem}.pbv-first-repeat{display:grid;grid-template-columns:48px 1fr;gap:.8rem;align-items:center}.pbv-first-repeat>strong{font-size:12px}
.pbv-first-lanes{display:grid;gap:6px}.pbv-lane{display:grid;grid-template-columns:22px 1fr;gap:.35rem;align-items:center}.pbv-lane>span{font:10px ui-monospace,monospace;color:var(--muted)}
.pbv-dots{display:grid;grid-template-columns:repeat(13,1fr);gap:4px;position:relative;align-items:center}.pbv-dots:before{content:"";position:absolute;left:3px;right:3px;border-top:1px solid var(--line)}
.pbv-dot{position:relative;display:grid;place-items:center;width:100%;height:21px;border-radius:4px;font:10px ui-monospace,monospace;color:var(--muted)}
.pbv-dot:before{content:"";width:4px;height:4px;border-radius:50%;background:var(--line)}
.pbv-dot-active{background:var(--c2);color:var(--paper);font-weight:700}.pbv-dot-active:before{content:none}
.pbv-lane[data-arm=C3] .pbv-dot-active{background:var(--c3);color:var(--paper)}
.pbv-lane-axis{display:flex;justify-content:space-between;margin:.6rem 0 0 85px;font-size:10px;color:var(--muted)}
.pbv-procedure-bottom{border-top:1px solid var(--line);margin-top:1.3rem;padding-top:1.2rem}
.pbv-mini-stats{display:grid;grid-template-columns:1fr 1fr;gap:1rem;margin-top:1.1rem;padding-top:1rem;border-top:1px solid var(--line)}
.pbv-mini-stats small{display:block;font-size:11px;color:var(--muted)}.pbv-mini-stats strong{font-size:19px;font-variant-numeric:tabular-nums;letter-spacing:-.02em}.pbv-mini-stats em{display:inline-block;white-space:nowrap;font-style:normal;color:var(--teal)}
.pbv-details{border-block:1px solid var(--line);font-size:14px}
.pbv-details summary{display:flex;align-items:center;justify-content:space-between;gap:1rem;padding:1rem .15rem;font-weight:600;cursor:pointer;list-style:none}
.pbv-details summary::-webkit-details-marker{display:none}.pbv-details summary:after{content:"+";font-size:22px;font-weight:400;color:var(--teal)}.pbv-details[open] summary:after{content:"−"}
.pbv-details table{width:100%;table-layout:fixed;font-size:12px;line-height:1.65;margin:0 0 1rem}
.pbv-details :is(th,td){padding:.65rem .45rem;vertical-align:top;overflow-wrap:anywhere;border-bottom:1px solid var(--line);font-variant-numeric:tabular-nums;text-align:left}
.pbv-details th:first-child{width:38%}
.pbv-quality .pbv-chart-group-head b{font-size:12px;color:var(--amber)}
.pbv-quality-note{padding:.8rem 1rem;border-left:3px solid var(--amber);background:var(--amber-bg);margin-top:1.2rem!important;font-size:12px}
.pbv-evidence{display:grid;grid-template-columns:1fr 28px 1fr 28px 1fr;gap:.4rem;align-items:center}
.pbv-evidence-item{text-align:center;padding:1rem .5rem;align-self:stretch;border:1px solid var(--line);border-radius:10px;background:var(--paper)}
.pbv-evidence-item .pbv-icon{width:45px;height:45px;margin:0 auto .7rem;color:var(--teal)}
.pbv-evidence-item strong{display:block;font-size:14px}.pbv-evidence-item p{font-size:12px;color:var(--muted);margin:.5rem 0 .75rem}.pbv-evidence-item small{display:block;font-size:11px;color:var(--amber)}
.pbv-evidence-item:first-child small{color:var(--teal)}.pbv-evidence-item:nth-child(n+3) .pbv-icon{color:var(--amber)}
.pbv-evidence-arrow{color:var(--muted);font-size:20px;text-align:center}
.pbv-limits{display:grid;grid-template-columns:auto 1fr;gap:1.2rem;align-items:center;padding:1.1rem 1.3rem;border-left:3px solid var(--amber);background:var(--amber-bg);border-radius:0 8px 8px 0}
.pbv-limits>b{font-size:37px;line-height:1.1;color:var(--amber)}.pbv-limits strong{display:block;font-size:14px}.pbv-limits p{font-size:12px;color:var(--muted);margin-top:.35rem}
.pbv-followup{display:grid;grid-template-columns:1fr 34px 1fr;gap:.7rem;align-items:center}
.pbv-followup-panel{padding:1.2rem;background:var(--paper);border:1px solid var(--line);border-radius:10px;align-self:stretch}
.pbv-followup-panel .pbv-icon{width:45px;height:45px;margin:.8rem 0;color:var(--teal)}
.pbv-followup-panel strong{display:block;font-size:16px}.pbv-followup-panel p{font-size:12px;color:var(--muted);margin-top:.5rem}
.pbv-complete{color:var(--teal);background:var(--teal-bg);border-color:var(--teal)}
@media(max-width:600px){
  .pbv{font-size:14px;margin:1.5rem 0}.pbv-board{padding:1rem;border-radius:12px}.pbv-title{font-size:17px;margin-bottom:1.1rem}.pbv figcaption{font-size:11px}
  .pbv-roadmap a{font-size:12px;padding:.35rem .1rem}.pbv-roadmap span{display:block;margin:0;font-size:10px}.pbv-roadmap small{display:none}
  .pbv-summary{gap:.4rem;padding:1rem 0}.pbv-summary b{font-size:23px}.pbv-summary span{font-size:10px}.pbv-summary small{font-size:10px}
  .pbv-overview{grid-template-columns:1fr;gap:.65rem}.pbv-overview>div{display:grid;grid-template-columns:135px 1fr;column-gap:.7rem;text-align:left;align-items:center;width:100%}
  .pbv-overview>div>strong{align-self:end;margin:0}.pbv-overview>div>small{grid-column:2;align-self:start;margin-top:.3rem}
  .pbv-overview>div>:first-child{grid-row:1/3}.pbv-overview>.pbv-arrow{transform:rotate(90deg);font-size:20px}
  .pbv-papers{height:92px;justify-content:center;align-items:center}.pbv-paper{width:49px;height:72px;padding:5px;flex-shrink:0}.pbv-paper:nth-child(1){transform:rotate(-10deg) translate(9px,5px)}.pbv-paper:nth-child(3){transform:rotate(10deg) translate(-9px,5px)}.pbv-paper .pbv-icon{height:43px}.pbv-paper span{font-size:8px}
  .pbv-runner{height:90px}.pbv-runner .pbv-icon{width:46px;height:46px}.pbv-runner code{font-size:9px}.pbv-output{width:106px;height:88px;margin:auto;padding:9px 10px}.pbv-output code{font-size:10px;margin-bottom:6px}.pbv-output-line{margin:6px 0}.pbv-output-line:last-of-type{display:none}.pbv-output-mark{font-size:9px}
  .pbv-pair{grid-template-columns:1fr}.pbv-condition{padding:1rem}.pbv-condition-head{margin-bottom:.8rem}
  .pbv-material{padding:.8rem;gap:.8rem}.pbv-material>b{font-size:31px}.pbv-material strong{font-size:13px}.pbv-material small{font-size:11px}
  .pbv-document-row{gap:2px}.pbv-document .pbv-icon{height:24px}.pbv-document{font-size:8px}.pbv-repeats{gap:.35rem}.pbv-repeat{padding:.65rem .45rem}.pbv-repeat span{font-size:10px;padding:.2rem .2rem}.pbv-repeat strong{font-size:11px}.pbv-specs{gap:.35rem}.pbv-tag{font-size:10px}.pbv-scope-total{font-size:11px}.pbv-scope-total strong{font-size:24px}
  .pbv-measures{grid-template-columns:1fr;gap:1.1rem}.pbv-measure{grid-template-columns:38px 1fr;gap:.7rem}.pbv-measure small{margin-top:.3rem}
  .pbv-tools{grid-template-columns:1fr;gap:.5rem}.pbv-tools>.pbv-arrow{transform:rotate(90deg);font-size:18px}.pbv-tool{display:grid;grid-template-columns:37px 1fr;column-gap:.7rem;padding:.9rem}.pbv-tool .pbv-icon{width:34px;height:34px;grid-column:1;grid-row:1/3;margin:0;align-self:center}.pbv-tool strong,.pbv-tool p{grid-column:2}.pbv-tool-index{display:none}.pbv-tool p{margin-top:.25rem}.pbv-oracle{padding:.75rem;gap:.5rem}.pbv-oracle p{font-size:11px}.pbv-oracle b{font-size:22px}
  .pbv-bar-row{grid-template-columns:20px minmax(0,1fr) 49px;gap:.45rem;font-size:11px}.pbv-bar-label{font-size:10px}.pbv-axis{margin-right:56px;margin-left:27px;font-size:9px}.pbv-chart-group-head{font-size:11px}.pbv-chart-group-head b{font-size:14px}.pbv-pooled .pbv-chart-group-head b{font-size:24px}.pbv-bar-track{height:14px}.pbv-pooled .pbv-bar-track{height:20px}.pbv-chart-legend{gap:.6rem;font-size:10px}.pbv-tradeoff{font-size:11px;gap:.4rem .8rem}
  .pbv-first-repeat{grid-template-columns:34px 1fr;gap:.5rem}.pbv-first-repeat>strong{font-size:10px}.pbv-lane{grid-template-columns:18px 1fr;gap:.3rem}.pbv-lane>span{font-size:9px}.pbv-dots{gap:2px}.pbv-dot{height:18px;font-size:9px}.pbv-lane-axis{margin-left:65px;font-size:9px}.pbv-mini-stats{gap:.6rem}.pbv-mini-stats strong{font-size:17px}.pbv-mini-stats small{font-size:10px}
  .pbv-details{font-size:13px}.pbv-details table{font-size:11px}.pbv-details :is(th,td){padding:.6rem .3rem}.pbv-details th:first-child{width:36%}.pbv-quality .pbv-chart-group-head b{font-size:11px}
  .pbv-evidence{grid-template-columns:1fr;gap:.45rem}.pbv-evidence-item{display:grid;grid-template-columns:40px 1fr;column-gap:.75rem;text-align:left;padding:.9rem}.pbv-evidence-item .pbv-icon{grid-row:1/4;margin:0;align-self:center;width:35px;height:35px}.pbv-evidence-item p,.pbv-evidence-item small{grid-column:2}.pbv-evidence-item p{margin:.2rem 0}.pbv-evidence-arrow{transform:rotate(90deg);font-size:19px}
  .pbv-limits{padding:1rem;gap:.8rem}.pbv-limits>b{font-size:31px}.pbv-limits strong{font-size:13px}.pbv-limits p{font-size:11px}
  .pbv-followup{grid-template-columns:1fr;gap:.6rem}.pbv-followup>.pbv-arrow{transform:rotate(90deg);font-size:20px}.pbv-followup-panel{padding:1rem}.pbv-followup-panel .pbv-icon{float:right;width:35px;height:35px;margin:0}.pbv-followup-panel strong{font-size:15px}
}
@media(prefers-reduced-motion:reduce){.pbv *{transition:none!important}}
@media print{.pbv{break-inside:avoid;--ink:#233343!important;--muted:#586b7d!important;--line:#d5e0e7!important;--paper:#fff!important;--wash:#f3f7fa!important;--teal:#087e80!important;--teal-bg:#e3f4ef!important;--amber:#936018!important;--amber-bg:#fff3dc!important;--c2:#74849b!important;--c3:#087e80!important}}
</style>

<svg width="0" height="0" aria-hidden="true" focusable="false" style="position:absolute;overflow:hidden"><defs>
<symbol id="pbv-file" viewBox="0 0 48 48"><path d="M11 4h19l9 9v31H11zM30 4v10h9M18 23h14m-14 7h14m-14 7h9"/></symbol>
<symbol id="pbv-table" viewBox="0 0 48 48"><rect x="5" y="9" width="38" height="30" rx="2"/><path d="M5 19h38M5 29h38M18 9v30m12-30v30"/></symbol>
<symbol id="pbv-math" viewBox="0 0 48 48"><path d="m6 27 5 9 8-24h24M25 23h14M30 17l4 12m-5 4h8"/></symbol>
<symbol id="pbv-diagram" viewBox="0 0 48 48"><rect x="16" y="4" width="16" height="10" rx="2"/><rect x="2" y="33" width="16" height="11" rx="2"/><rect x="30" y="33" width="16" height="11" rx="2"/><path d="M24 14v10M10 33v-9h28v9"/></symbol>
<symbol id="pbv-terminal" viewBox="0 0 48 48"><rect x="4" y="7" width="40" height="32" rx="4"/><path d="M4 15h40M10 11h1m4 0h1m4 0h1M12 23l6 5-6 5m13 0h10"/></symbol>
<symbol id="pbv-note" viewBox="0 0 48 48"><rect x="9" y="6" width="30" height="37" rx="3"/><path d="M16 3v7m8-7v7m8-7v7M16 20l3 3 5-6m4 4h5M16 32l3 3 5-6m4 4h5"/></symbol>
<symbol id="pbv-book" viewBox="0 0 48 48"><path d="M24 12C18 7 10 6 4 9v29c7-3 14-1 20 3 6-4 13-6 20-3V9c-6-3-14-2-20 3zm0 0v29M10 17l7 1m-7 6 7 1m14-7 7-1m-7 8 7-1"/></symbol>
<symbol id="pbv-input" viewBox="0 0 48 48"><path d="M18 9h22v32H18M3 24h25m-7-7 7 7-7 7M24 15h9m-9 20h9"/></symbol>
<symbol id="pbv-reuse" viewBox="0 0 48 48"><path d="M39 16A17 17 0 0 0 9 12L5 19m0-10v10h10M9 32a17 17 0 0 0 30 4l4-7m0 10V29H33M18 25l4 4 9-10"/></symbol>
<symbol id="pbv-check" viewBox="0 0 48 48"><path d="m24 4 16 6v13c0 10-8 17-16 21C16 40 8 33 8 23V10zM16 23l6 6 11-12"/></symbol>
<symbol id="pbv-flask" viewBox="0 0 48 48"><path d="M17 4h14m-11 0v15L7 38a4 4 0 0 0 3 6h28a4 4 0 0 0 3-6L28 19V4M14 29h20M19 35h1m8 3h1"/></symbol>
</defs></svg>

## 00. 요약 {#pcb-summary}

Project Context의 기록 방식이 실제 작업에서 어떤 차이를 만드는지 보려고 **PDF 처리 작업 78세션을 실행했다**. 한 파일에 기록을 모으는 방식과 역할별로 나누는 방식을 나란히 돌렸다.

<figure class="pbv pbv-board" aria-labelledby="pbv-overview-caption" data-figure="overview">
<span class="pbv-kicker">PDF 처리 작업</span>
<strong class="pbv-title">정답이 있는 PDF를 처리하고 결과를 정답과 비교했다</strong>

<div class="pbv-overview">
<div><div class="pbv-papers"><div class="pbv-paper"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-table"/></svg><span>표</span></div><div class="pbv-paper"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-math"/></svg><span>수식</span></div><div class="pbv-paper"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-diagram"/></svg><span>도표</span></div></div><strong>정답이 있는 PDF</strong><small>합성 문서 13개 · 각 1페이지</small></div>
<span class="pbv-arrow" aria-hidden="true">→</span>
<div><div class="pbv-runner"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-terminal"/></svg><code>GPT-5.4 + 도구 4종</code></div><strong>두 기록 조건으로 실행</strong><small>C2 · 한 파일 / C3 · 역할별 분리</small></div>
<span class="pbv-arrow" aria-hidden="true">→</span>
<div><div class="pbv-output"><code># 처리 결과</code><div class="pbv-output-line"></div><div class="pbv-output-line"></div><div class="pbv-output-line"></div><div class="pbv-output-line"></div><span class="pbv-output-mark">정답과 비교</span></div><strong>결과와 실행 기록</strong><small>입력량 · 절차 · 정보 보존</small></div>
</div>
<figcaption id="pbv-overview-caption">PDF와 결과물은 작업 흐름을 설명하는 개념도다. 실제 문서나 실행 화면을 캡처한 그림은 아니다.</figcaption>
</figure>

역할 분리 조건은 세 번의 반복 모두 세션당 입력 토큰이 한 파일 조건보다 적었고, 평균으로는 **19.5% 낮았다**. 절차를 남기기 시작한 시점도 더 빨랐다. 다만 정보 보존 점수는 세 번 모두 역할 분리 쪽이 같거나 낮았고, 절차 재사용의 차이는 이번 지표로 가려내지 못했다. 이 글에는 실험을 위해 만든 실행·채점 도구와 거기서 나온 결과, 그리고 그 결과를 어디까지 읽을 수 있는지를 적었다.

<div class="pbv pbv-summary" role="group" aria-label="실험의 규모와 핵심 결과"><div><span>실행 규모</span><b>78세션</b><small>같은 자료 묶음 반복</small></div><div><span>평균 입력 토큰</span><b>−19.5%</b><small>C3 · 세 번 모두 감소</small></div><div><span>정보 보존 점수</span><b>C3 ≤ C2</b><small>최대 차이 0.034 · 동등성 판정 못함</small></div></div>

<nav class="pbv pbv-roadmap" aria-label="글의 다섯 단계"><a href="#pcb-motivation"><span>01</span>동기<small>운영에서 측정으로</small></a><a href="#pcb-design"><span>02</span>설계<small>조건·도구</small></a><a href="#pcb-results"><span>03</span>결과<small>입력량·품질</small></a><a href="#pcb-limits"><span>04</span>한계<small>해석 범위</small></a><a href="#pcb-next"><span>05</span>후속<small>다음 평가</small></a></nav>

## 01. 운영 경험을 정량적 지표로 확인하기 {#pcb-motivation}

앞선 프로젝트 [「Project Context: 새 세션을 위한 인수인계 설계」]({{< relref "/projects/project-context-standard" >}})에서는 기록할 문서의 역할을 나누고, 세션을 시작하고 끝내는 절차를 정했다. 실제 프로젝트에 적용하는 동안 규칙이 어긋나거나 필요 이상으로 문서를 읽는 문제가 나왔고, 그때마다 고쳤다.

그다음에 궁금한 건 이 방식이 실제로 어떤 효과를 내느냐였다. 문서를 나누면 매번 읽는 입력량이 줄어드는지, 앞서 남긴 절차를 다음 작업에서 다시 쓰는지, 결과물에 원문 정보가 충분히 남는지. 이 세 가지를 숫자로 확인하고 싶었다.

첫 대상은 PDF 처리 작업으로 정했다. 정답 정보가 딸린 합성 PDF를 쓰면 표·수식·도표가 결과물에 얼마나 남았는지 볼 수 있어서다. 같은 자료를 연달아 처리하게 하면 기록 방식에 따라 입력량과 절차 관리 행동이 어떻게 갈리는지도 함께 비교할 수 있었다.

## 02. PDF 탐색 실험 설계 {#pcb-design}

### 2.1 비교한 두 기록 방식

조건 이름은 벤치마크 설계에서 쓰던 번호를 그대로 가져왔다. **C2**는 `AGENTS.md` 한 파일에 기록을 모으고, **C3**는 `AGENTS.md`에 진입 규칙과 포인터만 두고 상태·지식은 역할별 문서로 나눠 둔다.

보려던 건 **작업 중 남긴 기록을 어떤 구조로 관리하는가** 하나였다. 설계에는 기록 파일이 아예 없는 C0, 예전 방식대로 `context.md` 한 장만 쓰는 C1, 규칙만 고정해 주는 C-static 같은 기준 조건도 있다. 이번 탐색 실험에서 실제로 돌린 건 C2와 C3뿐이다.

<figure class="pbv pbv-board" aria-labelledby="pbv-structure-caption" data-figure="structure">
<span class="pbv-kicker">2.1 · 비교 조건</span>
<strong class="pbv-title">C2는 한 파일에 쌓고, C3는 역할별로 나눴다</strong>

<div class="pbv-pair">
<div class="pbv-condition" data-arm="C2"><div class="pbv-condition-head"><b>C2</b><strong>한 파일에 누적</strong></div><div class="pbv-file"><code>AGENTS.md</code><ul><li>작업에 필요한 규칙</li><li>현재 상태와 진행 기록</li><li>작업하면서 얻은 지식</li></ul></div></div>
<div class="pbv-condition" data-arm="C3"><div class="pbv-condition-head"><b>C3</b><strong>역할별로 분리</strong></div><div class="pbv-file pbv-file-entry"><code>AGENTS.md</code><span>진입 규칙 + 문서 포인터</span></div><div class="pbv-branches"><div class="pbv-branch"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-note"/></svg><strong>상태 문서</strong><small>진행 상황</small></div><div class="pbv-branch"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-book"/></svg><strong>지식 문서</strong><small>절차·배운 내용</small></div></div></div>
</div>
<figcaption id="pbv-structure-caption">기록 구조를 역할 수준으로 그렸다. C3의 최종 파일은 6개였으며, 그림의 문서 상자 수가 실제 파일 수를 뜻하지는 않는다.</figcaption>
</figure>

### 2.2 실행 범위

<figure class="pbv pbv-board" aria-labelledby="pbv-scope-caption" data-figure="scope">
<span class="pbv-kicker">2.2 · PDF Arc Probe v0</span>
<strong class="pbv-title">13개 문서를 두 조건에서 각각 세 번 처리했다</strong>

<div class="pbv-material"><b>13</b><div><strong>독립적인 자료 묶음은 하나</strong><small>합성 PDF 13개, 모두 1페이지. 같은 묶음을 매번 사용.</small></div></div>
<div class="pbv-document-row" aria-label="같은 PDF 13개"><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>01</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>02</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>03</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>04</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>05</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>06</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>07</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>08</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>09</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>10</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>11</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>12</span></span><span class="pbv-document"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><span>13</span></span></div>
<div class="pbv-repeats"><div class="pbv-repeat"><strong>1회차</strong><span>C2 · 13세션</span><span>C3 · 13세션</span></div><div class="pbv-repeat"><strong>2회차</strong><span>C2 · 13세션</span><span>C3 · 13세션</span></div><div class="pbv-repeat"><strong>3회차</strong><span>C2 · 13세션</span><span>C3 · 13세션</span></div></div>
<div class="pbv-scope-total"><span>조건별 39세션 · 13문서 × 2조건 × 3반복</span><strong>총 78세션</strong></div>
<div class="pbv-specs"><span class="pbv-tag">GPT-5.4</span><span class="pbv-tag">reasoning_effort=none</span><span class="pbv-tag">temperature=0</span></div>

<figcaption id="pbv-scope-caption">각 조건은 반복마다 기록이 없는 상태에서 시작했다. 78세션 모두 정상 종료했고, 실패·실행 상한 도달·누출 없음으로 기록됐다. 제공한 도구는 read_file·list_dir·write_file·bash 4종이다.</figcaption>
</figure>

반복해서 돌린 건 같은 조건에서도 결과가 얼마나 흔들리는지 보기 위해서다. 다만 자료는 매번 같은 묶음이었으니, 이 반복이 다른 문서 묶음에 대한 검증을 대신하지는 않는다.

### 2.3 무엇을 측정했나

<figure class="pbv pbv-board" aria-labelledby="pbv-measures-caption" data-figure="measures">
<span class="pbv-kicker">2.3 · 평가 항목</span>
<strong class="pbv-title">입력량·절차·재사용·정보 보존을 측정했다</strong>

<div class="pbv-measures">
<div class="pbv-measure"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-input"/></svg><div><strong>얼마나 입력했나</strong><p>세션당 입력 토큰 · 단계 수 · 단계당 입력량</p><small>기록 방식에 따른 모델 입력량 차이</small></div></div>
<div class="pbv-measure"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-note"/></svg><div><strong>절차를 언제 기록했나</strong><p>첫 기록 시점 · 절차 갱신 횟수</p><small>작업 방법을 기록·관리하는 행동</small></div></div>
<div class="pbv-measure"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-reuse"/></svg><div><strong>다음 작업에서 다시 썼나</strong><p>엄격 · 의미 · 행동 기반 재사용 판정</p><small>기록한 절차가 이후 행동으로 이어지는지</small></div></div>
<div class="pbv-measure"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-check"/></svg><div><strong>원문 정보가 얼마나 남았나</strong><p>표 · 수식 · 도표의 fidelity recall</p><small>결과물에서 회수한 정답 정보의 비율</small></div></div>
</div>
<figcaption id="pbv-measures-caption">정보 보존 점수는 결과물 전체의 품질 점수가 아니다. 입력량이 줄면서 원문 정보까지 함께 줄어드는지 살펴보는 기준이다.</figcaption>
</figure>

정보 보존 점수가 결과물의 품질 전체를 대표하지는 않는다. 입력량이 줄 때 원문 정보까지 같이 줄어드는지 보려고 둔 기준이다. 채점 방식은 다음 절에 적었다.

### 2.4 실험을 위해 만든 도구

세션을 같은 조건으로 반복해 돌리고 결과를 같은 기준으로 채점하려면 도구가 필요했다. 그래서 Python으로 실행기와 채점기를 만들었다.

<figure class="pbv pbv-board" aria-labelledby="pbv-tooling-caption" data-figure="tooling">
<span class="pbv-kicker">2.4 · 직접 만든 실행·채점 도구</span>
<strong class="pbv-title">자료 생성·세션 실행·채점을 Python 도구로 처리했다</strong>

<div class="pbv-tools">
<div class="pbv-tool"><span class="pbv-tool-index">01</span><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><strong>자료 생성</strong><p>합성 PDF와 정답 자료를 함께 생성.<br>정답은 채점기에 제공.</p></div><span class="pbv-arrow" aria-hidden="true">→</span>
<div class="pbv-tool"><span class="pbv-tool-index">02</span><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-terminal"/></svg><strong>세션 실행</strong><p>모델 + 도구 4종으로 처리.<br>결과물·토큰·단계 수 기록.</p></div><span class="pbv-arrow" aria-hidden="true">→</span>
<div class="pbv-tool"><span class="pbv-tool-index">03</span><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-check"/></svg><strong>요소별 채점</strong><p>표의 셀·관계, 수식 구조,<br>도표의 연결·사실 비교.</p></div>
</div>
<div class="pbv-oracle"><p><strong>채점기 자체 점검</strong><br>정답을 그대로 Markdown으로 옮긴 뒤 채점</p><span class="pbv-arrow" aria-hidden="true">→</span><b>1.0</b></div>

<figcaption id="pbv-tooling-caption">세션 사이에는 조건별 기록 파일만 넘겼다. 정답 자료나 다른 세션의 파일에 접근했는지는 실행 기록으로 따로 검사했다.</figcaption>
</figure>

실행기는 OpenAI API로 모델을 부르고, 파일 읽기·목록·쓰기와 셸 도구만 쥐여 준다. 세션이 끝나면 조건별 기록 파일만 다음 세션으로 넘어간다. 세션마다 입력 토큰·단계 수·도구 결과 크기를 남겼고, 정답 자료나 다른 세션의 파일을 건드리지 않았는지도 함께 검사했다.

입력 자료는 공개 벤치마크 [pdf-parse-bench](https://github.com/phorn1/pdf-parse-bench)(MIT)의 생성기를 확장해 만들었다. PDF가 만들어질 때 표·수식·도표의 정답 자료도 같이 나온다.

채점할 때 표는 셀 값과 행·열 제목–값 관계를 맞춰 보고, 수식은 LaTeX 구문 트리(pylatexenc)의 구조와 기호를 비교한다. 도표는 유형에 따라 코드 줄이나 노드와 연결, 기재된 사실을 견줘 회수율을 계산한다. 채점 전에는 HTML 태그·코드 블록 표기·표 구분선을 걷어 냈다. 출력 형식을 어떻게 골랐느냐가 정보 보존 점수를 흔들면 안 되기 때문이다.

채점기 자체도 점검해야 했다. 정답을 그대로 Markdown으로 옮겨 채점하면 모든 점수가 1.0으로 나와야 한다. 이 성질을 기준으로 병합 셀 파싱 오류 같은 **결함 7개를 찾아 고쳤다**. 렌더링되지 않는 LaTeX 주석은 분모에서 뺐는데, 그대로 두면 표 자료의 한 문서에서 recall의 18%가 처음부터 얻을 수 없는 점수가 됐기 때문이다.

## 03. 관찰한 결과 {#pcb-results}

### 3.1 평균 입력량은 C3에서 낮았다

조건별 39세션을 합쳐 계산한 평균 입력량은 C2가 **81,841 tokens/session**, C3가 **65,900 tokens/session**이다. C2 대비 C3가 약 **19.5% 낮게 관찰됐다**.

<figure class="pbv pbv-board" aria-labelledby="pbv-input-caption" data-figure="input">
<span class="pbv-kicker">3.1 · 모델 입력량</span>
<strong class="pbv-title">세 번 모두 C3에서 입력 토큰이 적었다</strong>
<div class="pbv-chart-legend"><span><i class="pbv-key" aria-hidden="true"></i>C2 · 한 파일에 누적</span><span><i class="pbv-key pbv-key-c3" aria-hidden="true"></i>C3 · 역할별 분리</span></div><div class="pbv-pooled"><div class="pbv-chart-group-head"><strong>전체 평균</strong><b>−19.5%</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="input" data-replicate="pooled" data-value="81841" data-max="90000"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:90.934444%"></span></div><b>81,841</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="input" data-replicate="pooled" data-value="65900" data-max="90000"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:73.222222%"></span></div><b>65,900</b></div><div class="pbv-axis" aria-hidden="true"><span>0</span><span>45,000</span><span>90,000</span></div></div><div class="pbv-chart-group"><div class="pbv-chart-group-head"><strong>1회차</strong><b>−13.9%</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="input" data-replicate="1" data-value="80488" data-max="90000"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:89.431111%"></span></div><b>80,488</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="input" data-replicate="1" data-value="69286" data-max="90000"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:76.984444%"></span></div><b>69,286</b></div></div><div class="pbv-chart-group"><div class="pbv-chart-group-head"><strong>2회차</strong><b>−25.8%</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="input" data-replicate="2" data-value="80792" data-max="90000"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:89.768889%"></span></div><b>80,792</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="input" data-replicate="2" data-value="59955" data-max="90000"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:66.616667%"></span></div><b>59,955</b></div></div><div class="pbv-chart-group"><div class="pbv-chart-group-head"><strong>3회차</strong><b>−18.7%</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="input" data-replicate="3" data-value="84243" data-max="90000"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:93.603333%"></span></div><b>84,243</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="input" data-replicate="3" data-value="68460" data-max="90000"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:76.066667%"></span></div><b>68,460</b></div></div><div class="pbv-axis" aria-hidden="true"><span>0</span><span>45,000</span><span>90,000</span></div><p class="pbv-axis-label">입력 토큰 / 세션 · 모든 막대 0–90,000 동일 척도</p><div class="pbv-tradeoff"><span>단계 수 <strong>−6.0%</strong></span><span>단계당 입력량 <strong>−14.4%</strong></span></div>
<figcaption id="pbv-input-caption">전체 평균은 조건별 39세션을 합쳐 계산했다. 감소 폭은 13.9–25.8%. 모델 입력 토큰의 차이이며, 전체 요금이나 프로젝트 생산성의 개선율을 뜻하지 않는다.</figcaption>
</figure>

세 번 모두 방향은 같았다. 그래도 같은 문서 묶음을 반복한 결과라, 독립된 세 표본으로 보지는 않았다.

좀 더 들어가면 C3는 단계 수가 6.0% 적었고(세 반복 모두 감소), 단계당 입력량은 14.4% 낮았다. 입력량 차이가 단계 수 감소만으로 설명되지는 않는다는 뜻이다. 다만 단계당 입력에는 문서만이 아니라 도구 실행 결과와 누적 대화도 들어가므로, 이 숫자를 곧 문서를 읽는 양이 줄어든 비율로 읽을 수는 없다.

### 3.2 C3는 절차를 일찍 기록하고 더 자주 갱신했다

<figure class="pbv pbv-board" aria-labelledby="pbv-procedure-caption" data-figure="procedure">
<span class="pbv-kicker">3.2 · 절차의 첫 기록과 갱신</span>
<strong class="pbv-title">C3는 세 반복 모두 첫 세션부터 기록했다</strong>
<div class="pbv-first"><div class="pbv-first-repeat"><strong>1회차</strong><div class="pbv-first-lanes"><div class="pbv-lane" data-arm="C2" aria-label="1회차 C2: 7번째 세션에 절차 첫 기록"><span>C2</span><div class="pbv-dots" aria-hidden="true"><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot pbv-dot-active">7</span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span></div></div><div class="pbv-lane" data-arm="C3" aria-label="1회차 C3: 1번째 세션에 절차 첫 기록"><span>C3</span><div class="pbv-dots" aria-hidden="true"><span class="pbv-dot pbv-dot-active">1</span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span></div></div></div></div><div class="pbv-first-repeat"><strong>2회차</strong><div class="pbv-first-lanes"><div class="pbv-lane" data-arm="C2" aria-label="2회차 C2: 1번째 세션에 절차 첫 기록"><span>C2</span><div class="pbv-dots" aria-hidden="true"><span class="pbv-dot pbv-dot-active">1</span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span></div></div><div class="pbv-lane" data-arm="C3" aria-label="2회차 C3: 1번째 세션에 절차 첫 기록"><span>C3</span><div class="pbv-dots" aria-hidden="true"><span class="pbv-dot pbv-dot-active">1</span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span></div></div></div></div><div class="pbv-first-repeat"><strong>3회차</strong><div class="pbv-first-lanes"><div class="pbv-lane" data-arm="C2" aria-label="3회차 C2: 1번째 세션에 절차 첫 기록"><span>C2</span><div class="pbv-dots" aria-hidden="true"><span class="pbv-dot pbv-dot-active">1</span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span></div></div><div class="pbv-lane" data-arm="C3" aria-label="3회차 C3: 1번째 세션에 절차 첫 기록"><span>C3</span><div class="pbv-dots" aria-hidden="true"><span class="pbv-dot pbv-dot-active">1</span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span><span class="pbv-dot"></span></div></div></div></div></div><div class="pbv-lane-axis" aria-hidden="true"><span>1번째 세션</span><span>13번째 세션</span></div><div class="pbv-procedure-bottom"><div class="pbv-chart-group-head"><strong>절차 갱신 횟수 · 세 반복 합계</strong><span class="pbv-gap">단위: 회</span></div><div class="pbv-bar-row" data-arm="C2" data-metric="updates" data-replicate="total" data-value="11" data-max="25"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:44.000000%"></span></div><b>11</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="updates" data-replicate="total" data-value="23" data-max="25"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:92.000000%"></span></div><b>23</b></div><div class="pbv-axis" aria-hidden="true"><span>0</span><span>25</span></div></div><div class="pbv-mini-stats"><div><small>최종 파일 수 · 매 반복</small><strong>C2 1개 · <em>C3 6개</em></strong></div><div><small>노트 쓰기 호출 · 세 반복 합계</small><strong>C2 30회 · <em>C3 123회</em></strong></div></div>
<figcaption id="pbv-procedure-caption">위 숫자는 절차를 처음 기록한 세션이다. C2도 세 번 모두 절차를 형성했지만, 1회차는 7번째 세션이었다. 더 많은 기록·갱신이 재사용의 개선을 뜻하지는 않는다.</figcaption>
</figure>

<details class="pbv pbv-details"><summary>기록량·쓰기 호출의 반복별 수치</summary>
<table><thead><tr><th scope="col">관찰 항목</th><th scope="col">C2</th><th scope="col">C3</th></tr></thead><tbody>
<tr><th scope="row">절차 첫 기록</th><td>7 · 1 · 1번째 세션</td><td>1 · 1 · 1번째 세션</td></tr>
<tr><th scope="row">절차 갱신 횟수</th><td>총 11회</td><td>총 23회</td></tr>
<tr><th scope="row">최종 파일 수</th><td>1개</td><td>6개</td></tr>
<tr><th scope="row">최종 기록 용량</th><td>4.3 · 4.9 · 5.0 KB</td><td>4.6 · 7.2 · 7.6 KB</td></tr>
<tr><th scope="row">노트 쓰기 호출</th><td>8 · 10 · 12회</td><td>46 · 43 · 34회</td></tr>
</tbody></table><p class="pbv-muted">나열한 값은 1·2·3회차 순서다. 노트 쓰기 호출 합계는 C2 30회, C3 123회다.</p>
</details>

C3는 반복마다 첫 세션부터 절차를 남겼다. C2도 세 번 모두 절차를 만들기는 했는데, 그중 한 번은 7번째 세션에 가서야 처음 기록했다. 차이가 난 곳은 절차를 만들었느냐가 아니라 **언제 만들고 얼마나 자주 손보느냐**였다.

눈에 띈 건 C3가 기록은 더 많이 남기면서 평균 입력량은 낮았다는 점이다. 저장해 둘 정보와 지금 세션이 읽을 정보를 나눌 수 있다는 쪽으로 읽히는 관찰이다. 대신 노트 쓰기 호출은 C3가 약 4배 많았으니, 기록을 유지하는 작업량도 같이 봐야 한다. 다만 실제로 쓴 바이트 수와 출력 토큰은 3회차에서 C2가 더 많았다. 쓰기 비용 전체가 늘 C3에서 크다고 말하기는 어렵다.

### 3.3 품질과 재사용은 판정하지 못했다

원문 정보 보존 점수인 **fidelity recall은 세 번 모두 C3가 C2 이하**였다. 차이는 0–0.034로 크지 않았고, 3회차는 반올림한 값이 아예 같다. 그런데 어느 정도 차이까지를 같은 품질로 볼지 미리 정해 두지 않았다(4.2). 그래서 입력 토큰이 낮았다는 것만으로 같은 품질을 더 적은 비용으로 얻었다고 결론 내릴 수는 없었다.

<figure class="pbv pbv-board pbv-quality" aria-labelledby="pbv-quality-caption" data-figure="quality">
<span class="pbv-kicker">3.3 · 원문 정보 보존</span>
<strong class="pbv-title">정보 보존 점수는 C3가 같거나 낮았다</strong>
<div class="pbv-chart-legend"><span><i class="pbv-key" aria-hidden="true"></i>C2 · 한 파일에 누적</span><span><i class="pbv-key pbv-key-c3" aria-hidden="true"></i>C3 · 역할별 분리</span></div><div class="pbv-chart-group"><div class="pbv-chart-group-head"><strong>1회차</strong><b>C2 − C3 = 0.034</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="fidelity" data-replicate="1" data-value="0.926" data-max="1"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:92.600000%"></span></div><b>0.926</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="fidelity" data-replicate="1" data-value="0.892" data-max="1"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:89.200000%"></span></div><b>0.892</b></div></div><div class="pbv-chart-group"><div class="pbv-chart-group-head"><strong>2회차</strong><b>C2 − C3 = 0.009</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="fidelity" data-replicate="2" data-value="0.937" data-max="1"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:93.700000%"></span></div><b>0.937</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="fidelity" data-replicate="2" data-value="0.928" data-max="1"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:92.800000%"></span></div><b>0.928</b></div></div><div class="pbv-chart-group"><div class="pbv-chart-group-head"><strong>3회차</strong><b>반올림한 표시값 같음</b></div><div class="pbv-bar-row" data-arm="C2" data-metric="fidelity" data-replicate="3" data-value="0.93" data-max="1"><span class="pbv-bar-label">C2</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:93.000000%"></span></div><b>0.930</b></div>
<div class="pbv-bar-row" data-arm="C3" data-metric="fidelity" data-replicate="3" data-value="0.93" data-max="1"><span class="pbv-bar-label">C3</span><div class="pbv-bar-track" aria-hidden="true"><span class="pbv-bar" style="--share:93.000000%"></span></div><b>0.930</b></div></div><div class="pbv-axis" aria-hidden="true"><span>0</span><span>0.5</span><span>1.0</span></div><p class="pbv-axis-label">fidelity recall · 모든 막대 0–1 동일 척도</p><p class="pbv-quality-note">어느 정도의 점수 차이까지 같은 품질로 볼지 미리 정하지 않았다. <strong>품질 동등성은 판정하지 못했다.</strong></p>
<figcaption id="pbv-quality-caption">회수한 정답 정보의 비율을 표시했다. 차이는 반올림된 값으로 계산한 0–0.034이며, 3회차의 표시값이 같다는 것이 원점수의 완전한 일치를 뜻하지는 않는다.</figcaption>
</figure>

정답에 없는 내용을 만든 양(false positives)과 형식 일관성은 반복마다 우위가 엇갈렸다.

절차를 다시 썼는지에 대한 판정도 흐릿했다. 엄격한 기준에서는 점수가 바닥에 깔리고, 의미 기반 기준에서는 천장에 붙었다. 행동 기반 판정처럼 다른 지표도 반복마다 방향이 달랐다.

결국 이번 실험에서 본 것은 절차를 빨리 남기고 자주 고치는 행동까지다. 거기서 **기록한 절차가 이후 작업에서 더 잘 재사용됐다는 결론**까지 이어지지는 않았다.

## 04. 평가하면서 확인한 한계 {#pcb-limits}

### 4.1 기록·재사용·품질은 따로 확인해야 했다

절차 문서를 만들었다는 것, 그 문서를 나중에 썼다는 것, 그렇게 해서 결과가 좋았다는 것은 서로 다른 관찰이다. 갱신 횟수나 파일 수가 늘었다고 재사용과 품질이 따라 좋아졌다고 볼 수는 없다.

<figure class="pbv pbv-board" aria-labelledby="pbv-evidence-caption" data-figure="evidence">
<span class="pbv-kicker">4.1 · 구분해서 확인할 것</span>
<strong class="pbv-title">기록·재사용·품질은 각각 따로 측정해야 했다</strong>

<div class="pbv-evidence">
<div class="pbv-evidence-item"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-note"/></svg><strong>절차를 기록함</strong><p>언제 남겼고<br>얼마나 갱신했나</p><small>기록 행동 관찰</small></div><span class="pbv-evidence-arrow" aria-hidden="true">⇢</span>
<div class="pbv-evidence-item"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-reuse"/></svg><strong>다음 작업에 활용함</strong><p>엄격 기준은 바닥<br>의미 기준은 천장</p><small>재사용 차이 판별 못함</small></div><span class="pbv-evidence-arrow" aria-hidden="true">⇢</span>
<div class="pbv-evidence-item"><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-check"/></svg><strong>좋은 결과를 얻음</strong><p>정보 보존 점수와<br>별도의 품질 기준 필요</p><small>동등한 품질 판정 못함</small></div>
</div>
<figcaption id="pbv-evidence-caption">한 단계의 관찰만으로 다음 단계까지 결론 낼 수 없었다. 행동 기반 재사용 지표도 반복마다 방향이 달랐다.</figcaption>
</figure>

특히 재사용 지표는 기준에 따라 점수가 바닥이나 천장에 몰려서, 두 조건의 차이를 가르지 못했다. 더 분명히 비교하려면 재사용을 어떻게 정의하고 채점할지부터 손봐야 한다.

### 4.2 입력량 차이의 원인과 품질 기준이 남았다

기록을 역할별로 나누는 방식은 낮은 입력량과 함께 관찰됐다. 그렇다고 역할 분리 자체의 효과라고 잘라 말하지는 못했다. 단계 수, 도구 사용 경로, 노트를 읽는 행동의 차이도 같이 영향을 줄 수 있어서다. 예를 들어 도구가 돌려준 결과의 평균 크기는 2·3회차에서는 C3가 작았지만 1회차에서는 컸다. 작은 도구 결과가 감소분의 일부는 설명해도, 세 반복 전체를 설명하지는 못한다.

“품질이 이 정도까지 차이 나면 동등하다고 볼 것인가”를 실험 전에 정해 두지 않았다. 조건을 가린 사람 평가도 없었다. 그래서 입력량이 줄었다는 것과 품질이 같다는 것을 함께 확정하기에는 근거가 모자랐다.

### 4.3 78세션의 적용 범위는 제한적이었다

전체 실행은 78세션이지만 **독립적인 자료 묶음은 하나**다. PDF는 전부 1페이지였고, 모델도 GPT-5.4 하나였다. 다른 문서와 다른 모델에서도 같은 결과가 나오는지는 따로 확인해야 한다.

<div class="pbv pbv-limits"><b>1</b><div><strong>78세션을 실행해도, 독립 자료 묶음은 하나였다.</strong><p>자료 1묶음 · 문서당 1페이지 · 모델 1종. 반복 횟수와 적용 범위는 다르다.</p></div></div>

세션이 쌓일수록 입력 비용이 완만해지는지도 반복마다 방향이 달랐다. 저장된 노트가 오래 지나도 정보를 얼마나 붙들고 있는지를 본 실험은 아니어서, 긴 프로젝트에서 맥락이 사라지는 것을 막아 준다는 데까지는 넓히지 않았다.

## 05. PDF 실험을 마치고 다음 평가로 {#pcb-next}

### 5.1 완료한 실험과 진행 중인 후속

PDF 실험에서 얻은 건 입력량, 절차 관리 행동, 결과물의 정보 보존을 한자리에서 비교할 수 있는 측정 기록이다. 동시에 절차 재사용을 가려내는 지표와 결과를 해석하는 범위가 아직 부족하다는 것도 알게 됐다.

그 부족한 부분을 메우려고 **밈 언어 기반 평가를 새로 시작했다**. 밈 표현으로 프로그램을 쓰는 작은 가상 언어를 만들고, 그 언어로 작업을 시키는 평가다. 아직 진행 중이라, 이 페이지의 수치는 먼저 끝난 PDF 실험에 한정한다.

<figure class="pbv pbv-board" aria-labelledby="pbv-followup-caption" data-figure="followup">
<span class="pbv-kicker">5.1 · 완료한 실험과 후속 작업</span>
<strong class="pbv-title">PDF 실험에서 남은 질문을 다음 평가로 옮겼다</strong>

<div class="pbv-followup">
<div class="pbv-followup-panel"><span class="pbv-tag pbv-complete">완료</span><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-file"/></svg><strong>PDF 탐색 실험</strong><p>78세션의 입력량·절차 관리·정보 보존 비교.<br>재사용 지표와 해석 범위의 한계 확인.</p></div><span class="pbv-arrow" aria-hidden="true">→</span>
<div class="pbv-followup-panel"><span class="pbv-tag">진행 중</span><svg class="pbv-icon" aria-hidden="true" focusable="false"><use href="#pbv-flask"/></svg><strong>밈 언어 기반 평가</strong><p>밈 표현으로 프로그램을 쓰는 작은 가상 언어.<br>그 언어로 작업하게 하는 후속 평가.</p></div>
</div>
<figcaption id="pbv-followup-caption">이 페이지의 수치는 먼저 완료한 PDF 실험에 한정한다. 후속 평가의 완료된 결과는 아직 없다.</figcaption>
</figure>

### 5.2 기록 기준

이 글은 내부 실험 보고서 「**PDF Arc Probe v0**」(2026-08-26)와 LLM Wiki에 정리해 둔 검토 내용을 바탕으로 썼다. 글에 쓴 집계 수치는 반복별 평균까지 [요약 데이터(YAML)](results-summary.yaml)로 함께 남겼다. 원시 세션 로그나 세션별 측정값은 그 파일에 넣지 않았다.

문서 구조를 왜 그렇게 설계했는지는 앞선 글 [「Project Context: 새 세션을 위한 인수인계 설계」]({{< relref "/projects/project-context-standard" >}})에 적어 뒀다.
