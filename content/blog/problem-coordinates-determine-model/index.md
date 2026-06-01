---
title: "문제의 좌표가 AI 모델을 결정한다"
date: 2026-05-29
summary: "CAPTCHA는 작은 CRNN으로 직접 만들고 커뮤니티 댓글은 대형 LLM에 맡겼다. 정반대의 선택은 운이 아니라 문제 구조가 시킨 일이었다 — No Free Lunch 관점에서 정리한 회고."
tags:
  - AI
  - Machine Learning
  - 회고
authors:
  - me
draft: true
---

> ⚠️ 초안(draft). 프로젝트 마무리 후 다듬을 예정.

## 1. 두 개의 정반대 결정

최근 두 프로젝트에서 **AI로 무언가를 인식/생성**하는 같은 부류의 문제를 만났는데, 결론은 정반대였다.

| | KCAPTCHA 인식 | 커뮤니티 댓글 생성 |
|---|---|---|
| 접근 | CRNN **직접 학습** | 대형 LLM **+ RAG** |
| 모델 크기 | ~10M params | 7B+ |
| 학습 비용 | 몇 시간, GPU 1장 | 수십~수백 GPU-day |
| GPT/Claude로? | **실패** | 오히려 정답 |

처음엔 의아했다. *왜 하나는 거대 모델도 못 푸는데 작게 직접 만들면 되고, 하나는 직접 만들면 비효율인데 거대 모델은 잘하지?*

## 2. 세 가지 익숙한 구분 — 그런데 다 같은 말이었다

찾아보니 업계엔 이미 세 가지 대비 개념이 있었다:

- **discriminative vs generative** (P(Y\|X)를 배우나, P(X,Y)를 배우나)
- **closed-ended vs open-ended** (정답이 닫혀있나, 열려있나)
- **specialist vs generalist** (좁고 깊나, 넓고 얕나)

그런데 정리하다 깨달았다. **이건 세 개의 다른 개념이 아니라, 두 개의 축이다.**

## 3. 축 1 — 출력의 결정성: H(Y|X)

입력이 주어졌을 때 정답이 얼마나 하나로 모이는가. 정보이론의 **조건부 엔트로피**로 잴 수 있다.

- CAPTCHA: 이미지 한 장 → 정답 문자열 딱 하나. `H(Y|X) ≈ 0`. **결정적(deterministic)**.
- 댓글: "오늘 점심 뭐 먹지" → 자연스러운 답 수백 개. `H(Y|X)`가 거대. **one-to-many**.

엔트로피가 0에 가까우면 **경계선만 그으면 되니까(discriminative)** 작은 모델로 충분하고 정확하다. 엔트로피가 크면 **분포 전체를 모델링(generative)** 해야 하니 큰 용량이 필요하다. → *discriminative/generative와 closed/open은 사실 이 한 축의 다른 이름이었다.*

## 4. 축 2 — 도메인의 너비

정답을 내기 위해 얼마나 넓은 사전지식이 필요한가.

- CAPTCHA: 픽셀 패턴만. 세상 지식 0. **좁다.**
- 댓글: 한국어 문법 + 어휘 + 세상 지식("황올"=BBQ 메뉴) + 문맥 추론 + 사이트 톤. **넓다.**

좁으면 specialist 하나로 끝, 넓으면 generalist가 이미 가진 지식을 빌려야 한다.

## 5. 그래서 — 진짜 큰 개념: No Free Lunch

두 축을 합치면 2D 지도가 나오고, 내 두 프로젝트는 **대각선 정반대 끝**에 있었다.

```
                 도메인 넓음 (generalist 필요)
                          ▲
   대형 분류기/임베딩       │       대형 LLM (+RAG)
   (broad·closed)         │       ← 커뮤니티 댓글
                          │         (broad·open)
   ─────────────────────--┼──────────────────────►
   닫힌 출력 H(Y|X)≈0      │       열린 출력 H(Y|X)↑
                          │
   작은 specialist        │       작은 fine-tuned LM
   from scratch           │       / 좁은 RAG
   ← KCAPTCHA (CRNN)      │
   (narrow·closed)        ▼
                 도메인 좁음 (specialist로 충분)
```

이 지도를 떠받치는 이론이 **No Free Lunch 정리**다: *모든 문제에 평균 내면 어떤 알고리즘도 우월하지 않다.* 따라서 **"무엇이 최고냐"는 질문 자체가 틀렸고, "이 문제의 구조에 어떤 모델의 귀납적 편향(inductive bias)이 맞느냐"가 옳은 질문**이다.

- CRNN이 CAPTCHA에서 GPT를 이긴 건 — CNN+CTC라는 편향이 "공간 → 시퀀스" 구조와 **정렬**됐기 때문.
- GPT가 CAPTCHA에서 진 건 — 자연 이미지로 학습돼 인위적 왜곡이 **분포 밖(OOD)** 이었기 때문. (게다가 의도적으로 풀이를 거부하도록 학습됨.)

내가 모델을 잘 고른 게 아니다. **문제의 좌표가 모델을 결정했다.**

## 6. 단, 시간이 흐르면 — The Bitter Lesson

반대 긴장도 있다. Rich Sutton의 *Bitter Lesson*: 장기적으론 사람이 손으로 넣은 구조보다 **연산·데이터로 미는 범용 방법**이 이긴다. 모순 같지만 경계가 갈린다. specialist가 이기는 조건은 ① 데이터·연산 제약 ② 좁고 고정된 분포 ③ 합성 데이터로 정답 무한 생성 가능 — **정확히 CAPTCHA**. 이 세 조건이 깨지는 순간 추는 generalist 쪽으로 넘어간다.

## 7. 결론 — 실전 체크리스트

새 AI 문제를 만나면 두 질문만:

1. **H(Y\|X)가 0에 가까운가?** (정답이 하나로 모이나) → 그렇다면 작게·직접·discriminative.
2. **넓은 사전지식이 필요한가?** → 그렇다면 큰 generalist를 빌려 RAG로 좁혀라.

> 계산기가 박사보다 곱셈을 빨리 풀지만, 박사가 계산기보다 시를 잘 쓴다. **문제가 곱셈인지 시인지 먼저 보라.**

---

### 참고 자료

- [The No Free Lunch Theorem, Kolmogorov Complexity, and the Role of Inductive Biases (arXiv 2304.05366)](https://arxiv.org/abs/2304.05366)
- [Inductive Bias: Matching ML Models to Real Problems](https://aliac.eu/blog/understanding-inductive-bias)
- [Generative vs Discriminative Models (DataCamp)](https://www.datacamp.com/blog/generative-vs-discriminative-models)
- [Open-Ended vs Close-Ended AI Tasks](https://www.tekst.com/blogs/gen-ai-exploring-the-differences-between-open-ended-and-close-ended-tasks)
- [Specialized Models Beat General LLMs (Sometimes)](https://dontpaniclabs.com/blog/post/2026/02/24/specialized-models-beat-general-llms-sometimes/)
- [Exploring OCR Capabilities of GPT-4V (arXiv 2310.16809)](https://arxiv.org/pdf/2310.16809)
