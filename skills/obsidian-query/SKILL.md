---
name: obsidian-query
description: |
  Obsidian vault의 Wiki를 knowledge base 삼아 깊은 질의응답을 진행하고,
  대화에서 생산된 지식을 다시 Wiki로 돌려보내는 스킬.
  "질문 시작할게", "위키 기반으로 질문해볼게", "query 시작", "wiki 참조해서 답해줘",
  "이 주제 깊게 파고들고 싶어", "wiki로 질문할게" 같은 요청에 이 스킬을 사용한다.
  단순한 일회성 질문이 아닌, 하나의 주제로 긴 호흡의 대화를 이어가는 경우에 적합하다.
---

# obsidian-query

Wiki를 knowledge base로 삼아 질문하고, 그 결과가 다시 Wiki를 키우는 순환 워크플로우.

**핵심 목적**: Wiki가 중심이고 대화는 매개체다.
- 대화에서 생산된 지식은 다시 Wiki로 돌아간다
- 답변의 출처를 명확히 구분한다 (wiki 기반 / 일반 지식 / wiki 부재)

**결과 md 작성 규칙**: 생성되는 모든 `.md` 파일(Sources·Wiki)은 **`obsidian:obsidian-markdown`** 스킬 규칙 준수.

**사용자 사고 검증 (필수)**: 답변 생성 시 사용자 전제에 사실 오류·소수 관점이 있으면 답 전에 지적. 주류와 다른 주장엔 양쪽 관점 비교 제공. 규칙: **`../_shared/cognitive-safety.md`**.

**도구/스킬 표기**: 답변 중 도구/서브에이전트 사용 시 🥕 이모지 표기 (사용자 전역 CLAUDE.md 규칙 준수).

**지식 출처 정책:**

| 지식 유형 | 예시 | 처리 방법 |
|-----------|------|-----------|
| 이 vault 특정 지식 | 이 vault에만 존재하는 개체·정책·규칙 | Wiki 필수. 없으면 "ingest 필요" 신호 |
| 도메인 배경 지식 | 해당 도메인의 이론/개념/역사 | 자유롭게 답변 (wiki가 있으면 wiki 우선) |
| 기술 일반 지식 | 프레임워크·프로토콜·SQL 기초 등 | 자유롭게 답변 |

---

## Phase 1: Session Start

세션 시작 시 조용히 사전 준비한 뒤 사용자에게 알린다.

### 사전 준비 (사용자에게 알리기 전)

**2단계 인덱스 탐색:**

1. **Master Index scan**: `Wiki/index.md` 읽기
   - 어떤 도메인이 있는지 파악
   - 질문 주제와 관련된 도메인 식별

2. **Domain Index scan**: 관련 도메인의 `Wiki/[Domain]/index.md` 읽기
   - 해당 도메인 안의 페이지를 타입별로 파악 (Entity/Concept/Flow/Analysis)
   - 관련 페이지 후보 목록 확보

3. 내부 exchange counter = 0 으로 초기화

> 도메인을 특정할 수 없으면 Master Index만으로 시작하고, Q&A 중에 Domain Index를 추가 탐색한다.

### 사용자에게 알림

```
Query session 시작됩니다.
관련 Wiki 도메인: [파악된 도메인]
주요 관련 페이지: [관련 wiki 페이지 목록]

질문하세요.
```

---

## Phase 2: Q&A Loop

### 답변 형식 가이드

- **데이터/예시가 있을 때**: 마크다운 테이블로 표현한다 (사용자 선호)
- **흐름/순서**: `> [!example]` Callout으로 표현 (코드블록 ASCII 다이어그램 대신)
- **비교**: 테이블 우선, 내용이 짧으면 인라인 가능
- **핵심 원칙**: `> [!important]` Callout
- **주의사항**: `> [!warning]` Callout

---

### Callout 스타일

Q&A 답변 및 Wiki 저장 시 → **`../_shared/callout-guide.md`** 참조.

---

### 질문이 들어올 때마다

1. **Domain Index 확인**: 해당 도메인의 `Wiki/[Domain]/index.md`에서 관련 페이지 파악
2. **On-demand read**: 관련 wiki 페이지들을 Read로 읽는다
3. **Full scan**: index로 찾기 어려우면 Wiki/[Domain]/ 디렉토리 전체 탐색
4. wiki 지식 기반으로 답변한다
5. exchange counter += 1

> **출처 구분 원칙:**
> - Wiki 기반 답변: 자연스럽게 `[[wiki 페이지]]` 링크를 포함
> - 일반 지식 답변: 별도 표시 없이 답변 (웹 검색으로 확인 가능한 수준)
> - 프로젝트 특정 지식인데 wiki에 없을 때만:
>   "(이 내용은 wiki에 없어 정확히 답하기 어렵습니다. ingest 후 더 정확한 답변이 가능합니다.)"

### 5회마다 상기 알림

exchange counter가 5의 배수가 될 때마다 자연스럽게 삽입:

```
💬 질문이 N번 이어졌습니다.
계속 이어가시겠어요?
마무리하실 준비가 되면 "충분해" / "이해했어" / "마무리할게" 라고 말씀해주세요.
```

### 종료 신호 감지

"충분해", "이해했어", "이 정도면 됐어", "마무리할게", "슬 마무리", "여기까지" 등
→ Phase 3으로 진입

---

## Phase 3: Query-Ingest

### 3-1. 세션 요약 (사용자에게 제시)

```
이번 Query session 요약:
- 주제: [주제]
- 교환 횟수: N회
- 핵심 Q&A:
  Q: ...
  A: ...

Wiki에 저장하겠습니다.
```

### 3-2. Wiki output 결정

→ **`../_shared/wiki-placement.md`** 참조 (자동 결정 기준 + 후보 제시).

Query 결과물은 대개 **신규 종합·비교·분석 페이지**로 저장되므로 "후보 A(신규) vs 후보 B(기존 보강)" 2축 판단이 주로 적용된다.

### 3-3. Sources 저장

`Sources/ai-queries/YYYY-MM-DD_주제.md` 생성

포함 내용:
```markdown
---
created: YYYY-MM-DD
tags: [도메인, query]
source-type: ai-query
wiki:
  - "[[Wiki/도메인/결과페이지]]"
참조-wiki:
  - "[[Wiki/도메인/참조페이지1]]"
  - "[[Wiki/도메인/참조페이지2]]"
---

# [주제] — Query Session

## 세션 정보
- 날짜: YYYY-MM-DD
- 교환 횟수: N회
- 참조한 Wiki 페이지: [목록]

---

## Q&A 원문

### Q1. [질문 원문]
[답변 원문]

### Q2. [질문 원문]
[답변 원문]

...
```

### 3-4. Wiki 저장

사용자 선택에 따라 신규 페이지 생성 또는 기존 페이지 보강.

Wiki 페이지 frontmatter에 반드시 sources 추가:
```yaml
sources:
  - "[[Sources/ai-queries/YYYY-MM-DD_주제]]"
```

신규 페이지 생성 시 `Wiki/index.md`에 링크 추가.
Source 파일의 `wiki:` 프로퍼티 업데이트 (양방향 링크 완성).

### 3-5. 로그 업데이트

`Wiki/logs/YYYY-MM.md`에 추가:
```markdown
## [YYYY-MM-DD] #query | [주제 요약]
- Sources: [[Sources/ai-queries/YYYY-MM-DD_주제]]
- Wiki: [[Wiki/도메인/페이지명]] (신규/보강)
- 교환 횟수: N회
```

`Wiki/log.md` 인덱스 갱신 (해당 월 항목 수 업데이트).

### 3-6. Git commit + push

→ **`../_shared/git-workflow.md`** 참조. commit prefix는 **`query:`**.

---

## 완료 기준

- Q&A 원문이 `Sources/ai-queries/`에 저장됐다
- 생산된 지식이 Wiki 페이지에 반영됐다 (신규 or 보강)
- Sources ↔ Wiki 양방향 링크(`wiki:` / `sources:`)가 연결됐다
- `Wiki/logs/YYYY-MM.md`에 `#query` 항목이 추가됐다
- git commit이 완료됐다
