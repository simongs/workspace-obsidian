# LLM Wiki Obsidian Vault

LLM이 위키를 자동으로 작성하고 유지보수하는 개인 지식 관리 시스템.

[Andrej Karpathy의 LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 패턴을 기반으로, [Zettelkasten](https://en.wikipedia.org/wiki/Zettelkasten)의 상향식 지식 축적 철학과 [PARA](https://fortelabs.com/blog/para/)의 실행 가능성 기반 분류를 결합했다. [Steph Ango](https://stephango.com/vault)(Obsidian CEO)의 vault 구조와 [obsidian-skills](https://github.com/kepano/obsidian-skills)를 참고하여 설계했다.

## 핵심 아이디어

> "Obsidian이 IDE이고, LLM이 프로그래머이고, Wiki가 코드베이스다." — Andrej Karpathy

기존 RAG는 질문할 때마다 원본에서 검색하여 매번 지식을 재조립한다. LLM Wiki는 다르다 — LLM이 원본 소스를 읽고 **위키로 컴파일**해놓는다. 소스를 추가할수록 기존 페이지가 풍부해지고, 교차참조가 자동으로 구축되며, 모순이 발견되면 표시된다. 지식이 복리로 성장한다.

사람의 역할: 소스 큐레이션, 질문, 사고
LLM의 역할: 요약, 교차참조, 분류, 정리 — 그 외 모든 것

## Vault 구조

```
workspace-obsidian/
├── CLAUDE.md              # LLM Wiki 운영 스키마 (Ingest/Query/Lint 워크플로우)
├── Clippings/             # 원본 소스 (불변, LLM은 읽기만)
├── Notes/                 # 내가 쓰는 노트, 대화 아카이브
├── Wiki/                  # LLM이 관리하는 위키 페이지
│   ├── index.md           # 위키 전체 목차 (도메인별)
│   └── log.md             # 활동 로그 (시간순)
├── Daily/                 # 데일리 노트
├── Categories/            # Bases 뷰 (가상 분류)
├── Templates/             # 노트 및 Bases 템플릿
│   └── Bases/             # .base 뷰 정의 파일
└── Attachments/           # 이미지, 파일
```

### 각 폴더의 역할

| 폴더 | 누가 쓰나 | 역할 | 출처 |
|------|-----------|------|------|
| `Clippings/` | 사람 | 원본 소스 보관 (불변) | Karpathy "raw" + Ango "Clippings" |
| `Wiki/` | LLM | 컴파일된 지식 (개념, 개체, 종합) | Karpathy "wiki" |
| `Notes/` | 사람 | 직접 쓰는 노트, 대화 아카이브 | Ango "Notes" + Zettelkasten "fleeting" |
| `Daily/` | 사람 | 데일리 노트 | Ango "Daily" |
| `Categories/` | LLM | Bases 뷰로 가상 분류 | Ango "Categories" |
| `CLAUDE.md` | 사람+LLM | 위키 운영 규칙 | Karpathy "schema" |

### Zettelkasten 흐름

```
Fleeting (Daily/, Notes/)  →  Literature (Clippings/)  →  Permanent (Wiki/)
    내가 쓴다                    내가 수집한다               LLM이 정제한다
```

### PARA는 폴더가 아닌 Properties로

```yaml
status: active       # Project (진행 중)
status: ongoing      # Area (지속적)
status: archived     # Archive (완료)
```

폴더를 이동하면 wikilink가 깨진다. Properties로 status만 바꾸면 Bases 뷰에서 자동 필터링된다.

## 워크플로우

### Ingest (소스 수집)

```
사용자: URL 또는 텍스트 제공
  → defuddle로 클린 마크다운 추출
  → Clippings/에 원본 저장
  → Wiki/에 개념/개체 페이지 생성 또는 업데이트
  → Wiki/index.md, Wiki/log.md 업데이트
```

한 소스가 10~15개 위키 페이지에 영향을 줄 수 있다.

### Query (질의)

```
사용자: 위키에 대한 질문
  → Wiki/index.md에서 관련 페이지 탐색
  → 관련 페이지 읽고 종합 답변
  → 좋은 답변은 Wiki/에 새 페이지로 저장
```

### Lint (점검)

```
사용자: "위키 건강 체크 해줘"
  → 페이지 간 모순 발견
  → 고아 페이지 (인바운드 링크 없는 페이지)
  → 빠진 교차 참조 보완
  → 새로운 탐구 질문 제안
```

## 도구

### 필수

| 도구 | 용도 |
|------|------|
| [Claude Code](https://claude.ai/claude-code) | LLM Wiki 엔진 — 위키 생성/유지보수 |
| [Obsidian](https://obsidian.md) | vault 뷰어 — 그래프 뷰, Bases, 편집 |
| [Obsidian CLI](https://help.obsidian.md/cli) | CLI로 vault 조작 |

### Claude Code 플러그인

| 플러그인 | 용도 |
|----------|------|
| [obsidian-skills](https://github.com/kepano/obsidian-skills) (by Steph Ango) | Obsidian 네이티브 문법 지원 (wikilinks, Bases, Canvas, defuddle) |
| obsidian-ai-archive (커스텀 스킬) | LLM 대화를 상세 지식 아카이브로 저장 |

### Obsidian 플러그인

| 플러그인 | 용도 |
|----------|------|
| Dataview | frontmatter 기반 동적 쿼리 |
| Templater | 고급 템플릿 (변수, 조건문) |

## 시작하기

### 새 맥북에서 환경 재현

```bash
# 1. CLI 도구 설치
npm install -g defuddle-cli
brew install gh
gh auth login

# 2. Vault clone
gh repo clone simongs/workspace-obsidian
cd workspace-obsidian

# 3. Obsidian에서 vault 열기 후 커뮤니티 플러그인 설치:
#    - Dataview
#    - Templater

# 4. Claude Code에서 obsidian-skills 설치
#    /plugin marketplace add kepano/obsidian-skills
#    /plugin install obsidian@obsidian-skills
#    /reload-plugins
```

### 첫 번째 Ingest 해보기

Claude Code에서:

```
"https://kotlinlang.org/docs/coroutines-overview.html 이 문서를 수집해줘"
```

LLM이 소스를 읽고, Clippings에 저장하고, Wiki에 관련 페이지들을 생성/업데이트한다.

## 설계 철학

이 vault는 세 가지 철학의 종합이다:

| 철학 | 기여 |
|------|------|
| **LLM Wiki** (Karpathy) | 3층 구조, Ingest/Query/Lint 워크플로우, LLM이 유지보수 담당 |
| **Zettelkasten** (Luhmann) | 상향식 지식 축적, 노트 성숙도 흐름 (Fleeting → Literature → Permanent) |
| **PARA** (Forte) | 실행 가능성 기반 분류, Properties로 구현 |
| **Steph Ango의 Vault** | 폴더 최소화, Categories/Bases로 가상 분류, 복수형 네이밍 |

## 참고 자료

- [LLM Wiki — Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [How I Use Obsidian — Steph Ango](https://stephango.com/vault)
- [kepano/kepano-obsidian](https://github.com/kepano/kepano-obsidian) — Ango의 vault 템플릿
- [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) — Obsidian CEO의 Claude Code 스킬
- [The PARA Method — Tiago Forte](https://fortelabs.com/blog/para/)
- [Zettelkasten Method](https://zettelkasten.de/overview/)

## 라이선스

MIT
