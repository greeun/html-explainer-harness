# html-explainer-harness

> 주제·개념·코드를 교육/리뷰/공유용 **단일 파일, 인포그래픽 우선 HTML 문서**로 변환 — Planner → Generator → Evaluator 에이전트 하네스가 만들고 품질 검증한다.

**언어:** 한국어 · [English](./README.md)

[Claude Code](https://claude.com/claude-code) 스킬. 글 단락을 쌓지 **않는다** — 각 개념을 가장 잘 담는 시각 표현(마인드맵, 순서도, 시퀀스 다이어그램, 차트, 비교 매트릭스, 웹툰, 도식, 또는 *그 주제만을 위해 발명한* 시각화)으로 바꾼 뒤, 결과를 실제로 렌더링하고, LLM이 판단할 수 없는 단 하나 — 시각적 느낌 — 을 위해 사람을 루프에 넣는다.

---

## 왜 필요한가

강한 모델도 압박이 없으면 **AI 슬롭**으로 흐른다: 보라 그라데이션, 이모지 불릿, 똑같은 카드 3개, 글의 벽. 이 스킬은 Anthropic의 *"Harness Design for Long-Running Application Development"* 하네스 패턴을 적용해 그 표류를 막는다. 분리된 세 에이전트가 **파일로만** 소통하므로, 작업을 *생성한* 에이전트가 *채점하지* 않는다 — 자기평가 편향을 구조로 차단한다.

## 좋은 출력의 기준

| 기준 | 가중 | 측정 대상 |
|------|:----:|-----------|
| **C1 인포그래픽 풍부도** | 2× | 개념이 다양하고 적합하며 주제 고유한 시각으로 변환됨 — 글 나열 아님. |
| **C2 시각 가독성** | 2× | 한눈에 들어옴: 위계, 스캔 경로, 여백; 연결선 완전 가시(가림/클리핑 없음). |
| **C3 Anti-Slop** | 2× | 주제 고유의 시각 언어; 어디에나 붙는 제네릭 슬롭 패턴 없음. *하드 게이트.* |
| **C4 학습 효과성** | 1× | 인터랙션·비유·예시가 이해를 쌓음. *쉬운 말 하드 서브게이트.* |
| **C5 내용 정확성** | 1× | 사실/개념/코드 정확하고 검증 가능. *하한: C5 < 3 → FAIL.* |

2× 세 축은 압박 없는 모델이 가장 약한 지점이라 가중을 둔다.

## 핵심 기능

- **독자 우선.** HTML 만들기 전에 **누구**(역할), **업무 도메인**(비유 소재), **수준**(입문/중급/전문가)을 묻는다. 독자를 절대 날조하지 않는다. 어휘·비유·깊이·시각화 복잡도가 전부 여기서 갈린다.
- **인포그래픽 우선.** Generator의 *첫* 사고 단계는 "이 개념을 어떤 시각 형태로 옮길까?". 글 단락은 fallback이지 기본값이 아니다. 밀도 기준: **시각화 ≥ 6개, 종류 ≥ 4종**, 모든 주요 섹션 ≥ 1개 시각.
- **쉬운 말 게이트.** 어려운 전문어가 즉시 일상어 풀이 없이 등장하면 하드 FAIL — 금지어 목록이 아니라 "이 독자가 풀이를 필요로 하는가?"로 판정.
- **대비로 잡는 Anti-slop.** 슬롭은 키워드 블록리스트가 아니라 슬롭 vs 비슬롭 few-shot 갤러리로 잡는다("주제 단어를 빼도 이 디자인이 다른 주제에 그대로 붙는가?").
- **필수 인간 시각 체크포인트.** HTML을 실제 브라우저에서 렌더, ≥2개 폭으로 스크린샷, 사람에게 레이아웃/색대비/여백/느낌 사인오프를 받는다. 이게 없으면 Evaluator는 **PASS 불가** — 모델 업그레이드로도 사라지지 않는 감각적 한계.
- **단일 자족 파일.** 빌드 없이 브라우저에서 바로 열림 (인라인 HTML/CSS/SVG/Canvas/JS; CDN 허용, 필수 아님).

## 사용법

Claude Code에서 원하는 바를 그냥 서술하면 된다. 다음 같은 문구에 스킬이 활성화된다:

- **KO:** "HTML 학습 문서", "교육용 HTML", "리뷰 문서 생성", "인터랙티브 설명 문서", "시각적 HTML 자료", "HTML 인포그래픽 문서", "개념 설명 HTML 만들어줘", "코드 리뷰 문서 HTML"
- **EN:** "interactive HTML explainer", "educational HTML doc", "visual learning document", "infographic HTML explainer", "make an HTML explainer for this concept", "single-file HTML teaching doc"

예시:

```
이 React useReducer 개념을 신입 프론트엔드 개발자(커머스 도메인, 입문)용
인터랙티브 HTML 설명 문서로 만들어줘.
```

스킬은 독자 삼각을 확인하고, 문서를 만들고, 렌더+스크린샷한 뒤, 너의 시각 사인오프를 받기 위해 멈추고, 그다음 적대적 평가를 돌린 후 결과를 전달한다.

## 활성화 흐름

```
1. 독자 인테이크        (게이트 — 누구/도메인/수준; 날조 금지)
2. 내용 수집
3. Planner 디스패치      →  spec.md (동결 스펙: 의도, 밀도 N/M, 정확성, Definition of Done)
4. Generator 디스패치    →  단일 HTML 파일 + generator_report.md
5. 렌더 + 스크린샷       (게이트 — 실제 브라우저, ≥2개 폭, 콘솔 로그)
6. 인간 시각 체크포인트  (게이트 — 시각 느낌에 사인오프)
7. Evaluator 디스패치    →  critique.md (6개 적대적 probe, 루브릭, PASS/FAIL)
8. 루프 (캡 3–5)         PASS → 전달 · FAIL → critique 들고 새 Generator 라운드
```

각 역할은 **별도 에이전트 디스패치**이며, 파일(`spec.md`, `generator_report.md`, `critique.md`) 외엔 아무것도 공유하지 않는다.

## 디렉토리 구조

```
html-explainer-harness/
├── SKILL.md                          # 오케스트레이터: 활성화 흐름, 게이트, 반복 지혜, 튜닝 루프
└── references/
    ├── planner-prompt.md             # Planner 에이전트 프롬프트 (spec.md 작성; 독자 인테이크 step 0)
    ├── generator-prompt.md           # Generator 에이전트 프롬프트 (개념 → 시각 → 인라인 구현)
    ├── evaluator-prompt.md           # Evaluator 에이전트 프롬프트 (6개 적대적 probe + 판정 로직)
    ├── rubric.md                     # 5개 기준, 가중, 판정 로직
    ├── evaluator-calibration.md      # 기준별 few-shot 1/3/5 점수 앵커 (관대함 방지)
    ├── infographic-patterns.md       # 시각화 카탈로그 + 슬롭/비슬롭 갤러리 + 시각 디테일 규칙
    └── screenshot-checkpoint.md      # 렌더 → 스크린샷 → 인간 사인오프 절차
```

## 티어 & 모델 가이드

기본 **Simplified** (단일 연속 Generator + 단일 종료시 Evaluator, 스프린트 분해 없음) — context anxiety가 제거된 Opus급 모델에 적합. 약한 모델(예: Sonnet 4.5)에선 **Full** 티어로 전환(더 작은 단위, 단단한 컨텍스트 리셋). 어떤 모델이든 **인간 시각 체크포인트는 필수 유지** — 컨텍스트 한계가 아니라 감각적 한계를 보완하기 때문.

## 출처

Anthropic의 *"Harness Design for Long-Running Application Development"* (2026) 하네스 원칙 구현: GAN식 역할 분리, 파일 기반 핸드오프, few-shot 앵커 기반 루브릭 평가, 압축(compaction) 대신 컨텍스트 리셋, "모든 하네스 구성요소는 가정을 인코딩한다 — 하중을 받는 것만 남겨라". `skill-wizard-harness` 메타-스킬로 제작.
