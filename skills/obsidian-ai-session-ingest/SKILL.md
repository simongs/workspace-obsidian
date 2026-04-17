---
name: obsidian-ai-session-ingest
description: LLM과의 대화를 Obsidian vault에 상세한 지식 아카이브로 저장한다. 사용자 질문 원문 보존, 구조적 결과물 상세 기록, Insight 보존, 참고 개념 wikilink 연결, 설치/설정 재현 기록. "대화를 옵시디언에 저장해줘", "대화 아카이브 해줘", "obsidian에 대화 저장", "이 대화를 기록해줘", "세션 아카이브 해줘", "대화 기록 남겨줘", "save conversation to obsidian", "archive this conversation", "ai archive"에 반응.
---

# obsidian-ai-session-ingest

LLM과의 대화를 Obsidian vault의 `Sources/ai-sessions/`에 원본으로 저장하고, 관련 Wiki 페이지를 생성/업데이트한다.

## 핵심 원칙

- 사용자의 질문은 **원문 그대로(full-text)** 보존한다
- 답변은 상세히 기록하되, 사용된 도구/스킬은 🥕 이모지와 함께 답변 본문에 자연스럽게 녹인다
- 대화에서 나온 **★ Insight는 있는 경우에만** 포함한다
- 선행 지식이 필요한 개념은 **참고 개념** 섹션에 `[[wikilink]]` + 한 줄 설명으로 기록한다
- 설치/설정, 다음 단계 등은 **해당 내용이 있을 때만** 기록한다
- 대화 중 언급된 개념/개체가 Wiki에 없으면 **Wiki 페이지도 함께 생성**한다

---

## Workflow

### Step 1: Vault 경로 확인

```bash
VAULT_ROOT=$(git rev-parse --show-toplevel 2>/dev/null || pwd)
```

`Wiki/`와 `Sources/` 디렉토리가 있는지 확인하여 LLM Wiki vault인지 검증:

```bash
ls "${VAULT_ROOT}/Wiki" "${VAULT_ROOT}/Sources" 2>/dev/null
```

vault가 아니면 사용자에게 vault 경로를 물어본다.

---

### Step 2: 이전 저장 확인

```bash
find "${VAULT_ROOT}/Sources/ai-sessions" -name '*.md' -type f | sort -r | head -1
```

이전 저장 파일이 있으면 읽어서 **마지막 저장 이후의 대화만** 새로 저장한다.
이전 저장이 없으면 세션 전체를 저장한다.

---

### Step 3: 파일명 생성

저장 위치: `Sources/ai-sessions/` (세션 원본은 모두 여기에)

```bash
DATE=$(date +%Y-%m-%d)
SESSION_NAME="세션이름"   # 세션 제목 또는 주제 요약 (공백 → 하이픈)
FILENAME="${VAULT_ROOT}/Sources/ai-sessions/${DATE}_${SESSION_NAME}.md"
```

파일명 규칙: `YYYY-MM-DD_세션이름.md`
- 예: `2026-04-14_claude-code-사용법-세션.md`

---

### Step 4: Wiki 위치 결정

대화 중 Wiki에 기록할 지식이 있다면, **문서 작성 전에** 위치를 사용자와 합의한다.

1. `Wiki/index.md`를 읽어 관련 도메인의 기존 페이지를 파악한다
2. 후보 위치 2~3개를 제시하고 선택을 요청한다

```
Wiki 페이지 위치를 결정하겠습니다.

**후보 A** — 기존 [[Wiki/도메인/기존파일명]]에 합치기
이유: (합치려는 근거)

**후보 B** — 새 파일 [[Wiki/도메인/새파일명]] 생성
이유: (분리하려는 근거)

**후보 C** — 기존 파일에 섹션 추가 + 교차참조
이유: (분량이 적거나 맥락이 강할 때)

어느 위치가 좋으신가요?
```

Wiki에 기록할 내용이 없는 대화라면 이 단계를 건너뛴다.

---

### Callout 스타일 가이드 (obsidian-admonition)

Sources 및 Wiki 문서 작성 시 ``` 코드블록 대신 **Obsidian Native Callout**을 사용한다.
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

### Step 5: 대화 분석 및 문서 작성

대화를 분석하여 아래 구조로 작성한다. **대화 유형에 따라 조건부 섹션은 자동으로 생략한다.**

#### 문서 구조

```markdown
---
type: conversation-archive
participants:
  - "사용자"
  - "Claude (모델명)"
domain: []
related: []
created: YYYY-MM-DD
tags:
  - conversation
  - archive
---

# YYYY-MM-DD_세션이름

## 개요
%%이 대화에서 무엇을 했는지 3-5문장 요약%%

---

## 1. {주제 제목}

### 질문 (원문)
> {사용자의 질문을 원문 그대로 인용. 한 글자도 바꾸지 않는다}

### 답변
%%답변의 핵심 내용을 상세히 기록%%
%%사용된 도구/스킬은 🥕 이모지와 함께 본문에 자연스럽게 포함%%
%%아키텍처 결과물, 디렉토리 구조, 비교 테이블, 코드 블록 등은 원본 그대로 포함%%

### 참고 개념                     ← 선행 지식이 필요한 경우만
- [[개념명]] — 한 줄 설명

> [!insight] ★ Insight            ← Insight가 있는 경우만
> {인사이트 내용을 callout으로 기록}

---

## 2. {다음 주제}
(같은 구조 반복)

---

## 설치 및 설정 기록               ← 설치/설정이 있었던 경우만

| 순서 | 작업 | 명령어/방법 | 비고 |
|------|------|------------|------|
| 1 | {작업} | `{명령어}` | {버전 등} |

### 재현 스크립트
```bash
# 재현 스크립트
{명령어들}
```

---

## 다음 단계                      ← 명확한 후속 작업이 있는 경우만
- [ ] {다음에 할 것} #next
- [ ] {나중에 할 것} #someday
```

---

### Step 6: 작성 지침

#### 사용자 질문 기록
- **원문 보존**: blockquote(`>`)로 한 글자도 수정 없이 기록
- 오타, 비문법적 표현도 그대로 유지 — 이것은 아카이브이다
- 연속된 메시지는 하나의 blockquote로 합친다

#### 답변 기록
- **구조적 결과물은 상세히**: 디렉토리 구조, 비교 테이블, 코드 블록 등 원본 그대로
- **단순 확인/인사는 생략**: "네", "알겠습니다" 등 실질적 내용 없는 교환 제외
- **의사결정 근거 포함**: "왜 이 방식을 선택했는가"를 본문에 자연스럽게 포함
- **도구/스킬 표기**: 🥕 이모지와 함께 자연스럽게 언급
  - 예: "🥕 WebFetch로 kepano의 vault 레포를 분석한 결과..."
  - 예: "🥕 defuddle로 공식 문서를 추출하여 Sources에 저장했다."

#### 참고 개념
- 답변을 나중에 다시 읽었을 때 **모르면 이해가 안 되는 것들**을 기록
- Wiki에 있으면 링크 연결, 없으면 미래 Wiki 페이지 후보

#### Insight
```markdown
> [!insight] ★ Insight
> {인사이트 내용}
```
Insight가 없는 주제에서는 생략.

#### 설치/설정
- 터미널 명령어는 실행 순서대로 기록 (실제 토큰/비밀번호 제외)
- 설정 파일 변경은 파일 경로와 변경 내용 기록
- 설치/설정이 없었다면 섹션 전체 생략

---

### Step 7: Wiki 연동

대화 중 언급된 개념/개체 중 Wiki에 아직 없는 것이 있으면 Wiki 페이지를 함께 생성한다.

1. 기존 Wiki 페이지로의 `[[wikilink]]`를 적극 사용
2. Step 4에서 결정한 위치에 Wiki 페이지 생성 또는 업데이트
3. 새 Wiki 페이지를 생성했으면 `Wiki/index.md`도 업데이트
4. `Wiki/logs/YYYY-MM.md`에 항목 추가:

```markdown
## [YYYY-MM-DD] #archive | 세션 주제 요약
- 파일: [[Sources/ai-sessions/파일명]]
- 신규 Wiki 페이지: (있다면 목록)
```

5. `Wiki/log.md` 인덱스 갱신 (해당 월 항목 수 업데이트)
6. Source 파일의 `wiki:` 프로퍼티 업데이트 (양방향 링크 완성)
   - 생성/업데이트된 Wiki 페이지 경로를 Source frontmatter에 추가
   - Wiki 파일의 `sources:` 프로퍼티도 이 Source를 가리키는지 확인

---

### Step 8: 저장 및 git commit

모든 파일 작업이 완료되면 git commit을 수행한다.

```bash
git add -A
git commit -m "$(cat <<'EOF'
archive: [세션 제목] — [핵심 내용 한 줄 요약]

- Sources: Sources/ai-sessions/{날짜}_{세션이름}.md
- Wiki: [생성/업데이트된 페이지 목록]
EOF
)"
```

커밋 메시지: `archive: [세션 제목] — [핵심 요약]`

git commit 완료 후, 생성/업데이트된 파일의 Obsidian 링크를 제공한다:

```
✅ archive 완료

[세션 파일명](obsidian://open?vault=workspace-obsidian&file=Sources/ai-sessions/...)
[Wiki 페이지명](obsidian://open?vault=workspace-obsidian&file=Wiki/...)   ← Wiki 생성 시에만
```

vault 이름은 항상 `workspace-obsidian` 고정. 파일 경로는 vault 루트 기준 상대경로, `.md` 확장자 생략.

---

## 완료 기준

- Sources/ai-sessions/에 대화 원본 파일이 생성됐다
- 대화에서 나온 새 지식이 Wiki에 반영됐다 (없으면 해당 없음)
- Wiki/logs/YYYY-MM.md에 #archive 항목이 추가됐다
- 로컬 git commit이 완료됐다
