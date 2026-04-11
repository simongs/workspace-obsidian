# LLM Wiki Schema

이 vault는 Karpathy의 LLM Wiki 패턴을 따릅니다.
LLM(Claude Code)이 위키를 생성하고 유지보수하며, 사람은 소스를 큐레이션하고 질문합니다.

## Vault 구조

```
Clippings/    — 원본 소스 (불변, 읽기 전용). 웹 기사, 책 발췌, 대화 기록
Notes/        — 사용자가 직접 쓰는 노트 (일지, 생각, 메모)
Wiki/         — LLM이 관리하는 위키 페이지 (개념, 개체, 요약, 종합)
  index.md    — 위키 전체 목차 (도메인별, LLM이 업데이트)
  log.md      — 활동 로그 (시간순, append-only)
Daily/        — 데일리 노트
Categories/   — Bases 뷰 파일 (.base 임베드)
Templates/    — 노트 템플릿
Attachments/  — 이미지, 파일
```

## 마크다운 규칙

- 내부 링크는 반드시 `[[wikilink]]` 사용
- 외부 URL은 `[텍스트](url)` 사용
- 모든 노트에 YAML frontmatter 필수
- 태그는 항상 복수형 (예: `concepts` X → tags 프로퍼티에 넣기)
- 날짜 형식: `YYYY-MM-DD`

## Frontmatter 규칙

모든 위키 페이지는 아래 프로퍼티를 포함해야 합니다:
- `categories`: `["[[Evergreen]]"]` 또는 `["[[Clippings]]"]` 등 — Bases 연동
- `type`: `concept` | `entity` | `source-summary` | `synthesis`
- `domain`: `["Kotlin"]` 등 — 지식 도메인
- `related`: 관련 위키 페이지 wikilinks
- `sources`: 참조한 Clippings 페이지 wikilinks
- `created`: 생성일
- `updated`: 마지막 수정일 (위키 페이지만)
- `tags`: 관련 태그 배열

## 작업 워크플로우

### Ingest (소스 수집)
1. 사용자가 URL 또는 텍스트를 제공하면:
2. `defuddle parse <url> --md`로 깨끗한 마크다운 추출
3. `Clippings/` 에 원본 저장 (Clipping Template 사용)
4. `Wiki/` 에 소스 요약 페이지 생성 (Wiki Source Summary Template)
5. 관련 개념/개체 페이지가 있으면 업데이트, 없으면 신규 생성
6. `Wiki/index.md` 업데이트
7. `Wiki/log.md` 에 항목 추가
8. 한 소스당 영향받는 위키 페이지를 모두 업데이트

### Query (질의)
1. 사용자 질문에 대해 `Wiki/index.md`에서 관련 페이지 탐색
2. 관련 위키 페이지 읽고 종합 답변
3. 가치 있는 답변은 `Wiki/`에 Synthesis 페이지로 저장
4. `Wiki/index.md`, `Wiki/log.md` 업데이트

### Lint (점검)
사용자가 요청하면 위키 건강 체크:
- 페이지 간 모순 발견
- 고아 페이지 (인바운드 링크 없는 페이지)
- 빠진 교차 참조
- 새 소스로 인해 낡아진 주장
- 탐구할 만한 새로운 질문 제안

## 위키 페이지 명명 규칙

- 개념: 개념 이름 그대로 (예: `Null Safety.md`, `코루틴.md`)
- 개체: 개체 이름 그대로 (예: `JetBrains.md`, `Kotlin 재단.md`)
- 소스 요약: `[소스제목] 요약.md` (예: `Kotlin 공식 문서 요약.md`)
- 종합: 질문이나 주제 (예: `Kotlin vs Java 비교.md`)

## 중요 원칙

- `Clippings/`의 파일은 절대 수정하지 않는다 (불변)
- `Wiki/`의 파일은 새 소스가 들어올 때마다 적극적으로 업데이트한다
- 모든 위키 페이지는 최소 하나의 다른 페이지와 `[[wikilink]]`로 연결되어야 한다
- 모순을 발견하면 숨기지 말고 명시적으로 표기한다 (callout 사용: `> [!warning] 모순`)
- 위키는 완벽할 필요 없다 — 점진적으로 개선된다
