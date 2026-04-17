# LLM Wiki Obsidian Vault

LLM이 위키를 자동으로 작성하고 유지보수하는 개인 지식 관리 시스템.

[Andrej Karpathy의 LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 패턴을 기반으로, [Zettelkasten](https://en.wikipedia.org/wiki/Zettelkasten)의 상향식 지식 축적과 [PARA](https://fortelabs.com/blog/para/)의 분류 철학을 결합했다.

## 핵심 아이디어

> "Obsidian이 IDE이고, LLM이 프로그래머이고, Wiki가 코드베이스다." — Andrej Karpathy

기존 RAG는 질문마다 원본에서 검색해 지식을 재조립한다. LLM Wiki는 다르다 — LLM이 원본 소스를 읽고 **Wiki로 컴파일**해놓는다. 소스가 쌓일수록 기존 페이지가 풍부해지고, 교차참조가 자동으로 구축되며, 모순이 발견되면 표시된다. **지식이 복리로 성장한다.**

```
사람의 역할: 소스 큐레이션, 질문, 사고
LLM의 역할: 요약, 교차참조, 분류, 정리 — 그 외 모든 것
```

## Vault 구조

```
workspace-obsidian/
├── CLAUDE.md              # LLM Wiki 운영 스키마 (LLM이 읽는 규칙서)
├── README.md              # 이 파일 — 사람이 읽는 소개서
├── Sources/               # 원본 소스 (불변 — ingest 후 수정 금지)
│   ├── HMG-Settlement/    # HMG 정산 시스템 관련 소스
│   ├── 북미/              # 북미 도메인 관련 소스
│   ├── ai-sessions/       # 보존할 가치 있는 세션 대화 아카이브
│   ├── ai-queries/        # Q&A 질의응답 원문
│   ├── daily/             # Daily 노트에서 ingest된 소스
│   └── docs/              # 공식 문서/레퍼런스
├── Wiki/                  # LLM이 관리하는 위키 페이지
│   ├── index.md           # 위키 전체 목차 (도메인별)
│   ├── log.md             # 활동 로그 월별 인덱스
│   ├── logs/              # 월별 상세 로그 (YYYY-MM.md)
│   ├── HMG-Settlement/    # 정산 시스템 도메인 (60개+ 엔티티)
│   ├── 북미/              # 북미 도메인 (HMA/KUS/Zuora)
│   ├── 인프라/            # k8s, ArgoCD, Harbor, hCloud, CI/CD
│   ├── Claude Code/       # Claude Code 사용법, 스킬, 공식 문서
│   ├── HMG-Series/        # HMG 공통 서비스 (hmg-notification 등)
│   └── 배경지식/          # SAP/회계, DB, 언어, 인물, 도구, 비즈니스
├── Daily/                 # Ingest 대기 스테이징 — ingest 후 삭제
├── Notes/                 # 직접 쓰는 노트, 대화 아카이브
│   └── Inbox/             # 미분류 노트 (분류 대기)
├── skills/                # Claude Code 커스텀 스킬
│   ├── obsidian-learn-ingest/   # 문서/소스 ingest 스킬
│   ├── obsidian-ai-session-ingest/  # 세션 대화 아카이브 스킬
│   └── obsidian-query/          # Wiki 기반 Q&A 스킬
├── Templates/             # 노트 템플릿
└── Attachments/           # 이미지, PDF, 첨부 파일
```

### 폴더 역할 요약

| 폴더 | 누가 쓰나 | 역할 |
|------|-----------|------|
| `Sources/` | 사람+LLM | ingest 완료된 원본 (불변) |
| `Wiki/` | LLM | 컴파일된 지식 (엔티티, 개념, 흐름) |
| `Daily/` | 사람 | ingest 대기 스테이징 영역 (처리 후 삭제) |
| `Notes/` | 사람 | 직접 쓰는 노트 (보존) |
| `skills/` | LLM | Ingest/Query/Lint 워크플로우 정의 |

## 워크플로우

### Ingest (소스 수집) — `obsidian-learn-ingest` 스킬

외부 문서, Figma 기획안, 코드 분석, Daily 노트를 Wiki로 변환한다.

```
소스 제공 (Figma URL / 코드 분석 요청 / Daily 노트)
    ↓
소스 분석 + Q&A 교차검증
    ↓
Sources/ 원본 저장 (불변)
    ↓
Wiki/ 페이지 생성/업데이트
    ↓
Wiki/logs/YYYY-MM.md 로그 + git commit
```

**실제 운영 패턴**:
- Figma 기획안 링크 → 스크린샷 + 코드 분석 병행 → entity wiki 전면 재작성
- 기획안 없는 경우 → 코드 분석만으로 ingest (Source에 `코드분석-` prefix)
- Daily 노트 → Q&A 검증 → Source 저장 후 Daily 파일 삭제

### Query (질의) — `obsidian-query` 스킬

Wiki를 knowledge base 삼아 긴 호흡의 질의응답을 진행한다.

```
"질문 시작할게" 트리거
    ↓
Wiki/index.md 스캔 → 관련 페이지 on-demand 읽기
    ↓
Q&A 진행 (5회마다 "계속?" 상기)
    ↓
Sources/ai-queries/ 저장 + Wiki 보강
```

### Lint (건강 체크)

```
"위키 정리해줘" 트리거
    ↓
- 고아 페이지 탐색 (인바운드 링크 없는 페이지)
- 깨진 wikilink 수정
- Sources ↔ Wiki 양방향 링크 불일치 수정
- Callout 스타일 위반 수정 ([!example] 적용)
- Wiki/logs/YYYY-MM.md에 #lint 항목 추가
```

## 도메인 현황 (2026-04-18 기준)

### HMG-Settlement (정산 시스템) — 주력 도메인

현대자동차그룹 빌링/정산 시스템의 완전한 도메인 지식 베이스.

| 영역 | 내용 |
|------|------|
| **엔티티** | 60개+ 테이블 (전표, 반제, 입금, 세금계산서, 거래처 등) |
| **흐름 문서** | 전표 생성·SAP 전송, 결산, 반제, 거래내역 수신 흐름 |
| **Enum** | SlipType, ProgramType, SapSlipState 등 |
| **운영** | 캐시 관리, EAI 장애 대응 |

### 북미 도메인

| 주제 | 내용 |
|------|------|
| HMA/KUS | 현대/기아 미국 법인, 입금/반제 전략 |
| Zuora | 구독 관리 SaaS, 4 Document 구조 |
| 회의 아카이브 | KUS 미국 방문 (2026-04) 3일차 결정사항 |

### 배경지식

SAP/회계(복식부기·전기키·분개), Database(동시성·트랜잭션), Kotlin, Obsidian, 카드 결제 생태계

### 인프라

GitLab CI, ArgoCD, Harbor, hCloud 모니터링, Helm, k8s Secret, AIP Gateway, hmg-cdn

## 통계 (2026-04-18 기준)

| 지표 | 값 |
|------|---|
| 총 ingest 횟수 | 64회 |
| 총 archive 횟수 | 4회 |
| 총 lint 횟수 | 5회 |
| HMG-Settlement 엔티티 | 56개 |
| Wiki 전체 도메인 | 8개 |

## 도구

| 도구 | 용도 |
|------|------|
| [Claude Code](https://claude.ai/claude-code) | LLM Wiki 엔진 — ingest/query/lint 실행 |
| [Obsidian](https://obsidian.md) | vault 뷰어 — 그래프 뷰, 편집, 검색 |
| Git / GitHub | 변경 이력 추적 (`wiki commit` 패턴) |
| Figma MCP | 기획안 스크린샷·디자인 컨텍스트 추출 |

### Claude Code 커스텀 스킬 (`skills/`)

| 스킬 | 트리거 | 역할 |
|------|--------|------|
| `obsidian-learn-ingest` | 소스 ingest 요청 | 문서·Figma·코드 분석 → Wiki 변환 |
| `obsidian-ai-session-ingest` | 세션 아카이브 요청 | 대화 → Sources/ai-sessions/ 보존 |
| `obsidian-query` | "질문 시작할게" | Wiki 기반 Q&A + 결과 ingest |

## 핵심 원칙

1. **Wiki의 모든 지식은 Sources를 통과한다** — 자동 지식화 없음
2. **Daily는 스테이징 영역** — ingest 완료 후 파일 삭제
3. **Sources는 불변** — wiki: 프로퍼티 추가만 예외
4. **Callout 스타일 통일** — 흐름 도식은 `[!example]`, 실제 코드만 코드 블록
5. **git commit 필수** — 모든 ingest/lint 후 커밋

## 시작하기

### 새 환경에서 클론

```bash
gh repo clone simongs/workspace-obsidian
cd workspace-obsidian
```

### Claude Code에서 사용

```
# 소스 ingest
"이 Figma 링크에 대해서 코드 분석과 함께 ingest 진행해줘"
"[daily 노트 내용] 이걸 ingest 해줘"

# 질의
"wiki 기반으로 반제 흐름에 대해 질문해볼게"

# 정리
"위키 정리해줘"
```

## 설계 철학

| 철학 | 기여 |
|------|------|
| **LLM Wiki** (Karpathy) | 3층 구조, Ingest/Query/Lint 워크플로우 |
| **Zettelkasten** (Luhmann) | 상향식 지식 축적, Fleeting → Permanent 흐름 |
| **PARA** (Forte) | 실행 가능성 기반 분류 |

## 참고 자료

- [LLM Wiki — Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [How I Use Obsidian — Steph Ango](https://stephango.com/vault)
- [The PARA Method — Tiago Forte](https://fortelabs.com/blog/para/)
- [Zettelkasten Method](https://zettelkasten.de/overview/)

## 라이선스

MIT
