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

**지식 출처 정책:**

| 지식 유형 | 예시 | 처리 방법 |
|-----------|------|-----------|
| 프로젝트 특정 지식 | pg_settlement VOID 로직, 내부 API 구조 | Wiki 필수. 없으면 "ingest 필요" 신호 |
| 도메인 배경 지식 | 부가가치세 원리, JWT 구조, 카드 매입 개념 | 자유롭게 답변 (wiki가 있으면 wiki 우선) |
| 기술 일반 지식 | Spring JPA, HTTP, SQL 기초 | 자유롭게 답변 |

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

### Callout 스타일 가이드 (obsidian-admonition)

Q&A 답변 및 Wiki 저장 시 ``` 코드블록 대신 **Obsidian Native Callout**을 사용한다.
실제 프로그래밍 코드(Java, Kotlin, SQL, bash)는 ``` 코드블록을 유지한다.

| 상황 | Callout 타입 |
|------|:----------:|
| 흐름도, 아키텍처 다이어그램 | `[!example]` |
| 핵심 원칙 / 반드시 기억해야 할 결론 | `[!important]` |
| 배경 설명 / 개념 보충 | `[!info]` |
| 판별 로직 / 멘탈 모델 | `[!tip]` |
| 주의사항 / 혼동 방지 / SAP 핵심 제약 | `[!warning]` |
| 정상 케이스 / 성공 시나리오 | `[!success]` |
| 미확인 / 열린 질문 | `[!question]` |
| 후속 작업 / 미결 사항 | `[!todo]` |
| 이슈 / 문제 원인 | `[!failure]` |
| 학습 인사이트 / 핵심 발견 | `[!insight]` |

**원칙**: 모든 callout은 기본 펼침 (`-` 접힘 금지, 단 `[!quote]-` 원본 전문 인용은 예외). 텍스트 아트 흐름도는 callout으로, 실제 코드는 코드블록 유지.

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

### 코드 기반 분석 요청 시

"코드로 보여줘", "코드 분석해줘" 등의 요청이 오면:

**참조 코드베이스**: `/Users/hmc7549232/workspace/workspace-git/hmg-billing/`

**정책**: 프로젝트를 통째로 스캔하지 않는다. 대신 사용자에게 역질문:

```
코드를 함께 보겠습니다.
어느 파일에서 시작하면 좋을까요?
예: 서비스 클래스명, 엔티티 파일명, 패키지 경로 등을 알려주시면
해당 파일부터 추적하겠습니다.
```

사용자가 파일/클래스명을 주면 해당 파일을 Read하고, 관련 파일을 on-demand로 추가 탐색한다.

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

대화 내용을 분석해 후보를 제시하고 사용자에게 확인한다:

**신규 페이지 생성** (기본):
- 기존 wiki에 없는 종합·비교·분석 결과
- 여러 wiki 페이지를 교차하는 새로운 시각

**기존 페이지 보강**:
- 기존 페이지에 빠진 섹션이나 관점을 추가하는 경우

```
Wiki 저장 방식:

**후보 A** — 신규 페이지 [[Wiki/도메인/새파일명]] 생성
이유: ...

**후보 B** — [[Wiki/도메인/기존파일명]] 보강
이유: ...

어느 방식이 좋으신가요?
```

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

### 3-6. Git commit

```bash
git add -A
git commit -m "$(cat <<'EOF'
query: [주제 요약]

- Sources: Sources/ai-queries/YYYY-MM-DD_주제.md
- Wiki: [생성/업데이트된 페이지]
EOF
)"
```

커밋 후 Obsidian 딥링크 제공:

```
✅ Query session 완료

[Wiki 페이지](obsidian://open?vault=workspace-obsidian&file=Wiki/...)
[Q&A 원문](obsidian://open?vault=workspace-obsidian&file=Sources/ai-queries/...)
```

---

## 완료 기준

- Q&A 원문이 `Sources/ai-queries/`에 저장됐다
- 생산된 지식이 Wiki 페이지에 반영됐다 (신규 or 보강)
- Sources ↔ Wiki 양방향 링크(`wiki:` / `sources:`)가 연결됐다
- `Wiki/logs/YYYY-MM.md`에 `#query` 항목이 추가됐다
- git commit이 완료됐다
