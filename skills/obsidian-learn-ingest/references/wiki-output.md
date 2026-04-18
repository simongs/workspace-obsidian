# Wiki Output Reference

메인 `SKILL.md`의 기술적 참조. Wiki/Sources 파일 **실제 작성 단계**에서 참조한다.
의사결정 로직(모드 선택, Phase 진행)은 SKILL.md 본문에 있음.

---

## 1. Callout 스타일 가이드 (obsidian-admonition)

Wiki 문서 작성 시 ``` 코드블록 대신 **Obsidian Native Callout**을 사용한다.
실제 프로그래밍 코드(Java, Kotlin, SQL, bash 명령어)는 ``` 코드블록을 유지한다.

### 타입 매핑

| 상황 | Callout 타입 | 사용 예 |
|------|:----------:|------|
| 흐름도, 아키텍처 다이어그램, 분개 패턴 | `[!example]` | 시스템 구성도, GL 분개 |
| 핵심 원칙 / 반드시 기억해야 할 결론 | `[!important]` | "CCS는 Zuora만 바라보면 된다" |
| 배경 설명 / 개념 보충 | `[!info]` | 개념 흐름, 모델 설명 |
| 판별 로직 / 멘탈 모델 / 빠른 참조 | `[!tip]` | 구독 유형 판별, 2쌍 구조 |
| 주의사항 / 혼동 방지 / SAP 핵심 제약 | `[!warning]` | Credit Memo 주의, Chargeback, 이중 처리 방지 |
| 정상 케이스 / 성공 시나리오 | `[!success]` | Scenario A — 정상 결제 |
| 미확인 / 열린 질문 | `[!question]` | +1 Document 미파악 |
| 후속 작업 / 미결 사항 | `[!todo]` | Phase 2 대체 작업 |
| 이슈 / 문제 원인 | `[!failure]` | 버그 원인, 에러 케이스 |
| 학습 인사이트 / 핵심 발견 | `[!insight]` | 세션 아카이브의 ★ Insight |
| 원본 인용 | `[!quote]` | 원본 노트 전문 callout |

### 원칙

1. **모든 callout은 기본 펼침** — `-` (접힘) 접미사 사용 금지
   - **예외**: 원본 전문 인용(`[!quote]-`)은 내용이 길어 기본 접힘을 허용한다
2. **실제 코드는 코드블록 유지** — Java, Kotlin, SQL, bash 등 언어 지정된 코드블록은 변환하지 않는다
3. **텍스트 아트 흐름도는 callout으로** — `→ ↓ ←` 화살표, `[시스템명]` 패턴, ASCII 박스 등
4. **단순 1줄 흐름은 callout 안 인라인 코드로** — `` `A → B → C` `` 형태
5. **과도한 callout 금지** — 모든 문단을 감싸지 않는다. 의미있는 강조에만 사용

### 변환 예시

*Before*:
````
```
[고객] → [Zuora] → [PaymentTech] → [카드사]
```
````

*After*:
```markdown
> [!example] HMG 북미 결제 흐름
> `[고객]` → `[Zuora]` → `[PaymentTech]` → `[카드사]`
```

---

## 2. Wiki 작성 체크리스트

Wiki 위치 합의 후 이 순서로 파일을 작성·갱신한다.

### Sources 저장 위치 기준

| 소스 유형 | Sources 경로 |
|-----------|---------------|
| GitHub 레포 / 기술 URL (Mode B) | `Sources/github/YYYY-MM-DD_레포명.md` |
| 공식 문서 / 레퍼런스 (Mode C) | `Sources/docs/[도구명]/YYYY-MM-DD_페이지제목.md` (학습 중 이미 완성) |
| 일반 외부 소스 | `Sources/외부-YYYY-MM-DD-제목.md` |
| Daily에서 ingest | `Sources/daily/YYYY-MM-DD_제목.md` |
| Notes에서 ingest | `Sources/notes-YYYY-MM-DD-제목.md` |

### 0-8단계 순서

```
0. (선행) Sources 파일이 이미 존재하면 읽어서 기존 `wiki:` 확인
1. Sources 저장 (Mode C는 이미 완료 — 건너뜀)
   - Daily / Notes/Inbox 소스: **제목 바로 아래, 정제 내용보다 먼저** 원본 전문을 callout으로 포함
     > [!quote]- 원본 노트 전문 보기
     > (원본 파일 전체 내용. 포맷 보존, 내용 변경 없음.)
   원본 callout 아래에 정제된 내용(리뷰 반영 완료 문구, 섹션들) 배치
2. Wiki/[도메인]/[개념명].md 생성 또는 업데이트
   - 기존 wiki 페이지와 wikilink 연결
3. 영향 받는 기존 Wiki 페이지에 교차참조 추가
4. Wiki/index.md 업데이트
5. 원본 소스 처리 (SKILL.md "원본 소스 처리 규칙" 참조)
6. Wiki/logs/YYYY-MM.md에 항목 추가: [날짜] #ingest | 제목 (출처: github/docs/daily/notes/외부)
7. Wiki/log.md 인덱스 갱신
8. Source 파일의 `wiki:` 프로퍼티 업데이트 (양방향 링크 완성)
   - 생성/업데이트된 Wiki 페이지 경로를 Source frontmatter에 추가
   - `wiki:` 프로퍼티가 없으면 새로 추가, 있으면 항목 추가
   - Wiki 파일의 `sources:` 프로퍼티도 이 Source을 가리키는지 확인
```

---

## 3. 마지막 단계: git commit + push

모든 파일 작업이 완료되면 commit + origin push를 수행한다.
이 vault는 GitHub private repo로 운영되므로 commit 후 `git push origin main`까지 기본.

```bash
git add -A
git commit -m "$(cat <<'EOF'
ingest: [소스 제목] — [핵심 내용 한 줄 요약]

- Sources: [생성된 파일명]
- Wiki: [생성/업데이트된 페이지 목록]
EOF
)" && git push origin main
```

커밋 메시지: `ingest: [소스 제목] — [핵심 요약]`

push 완료 후, 생성/업데이트된 파일의 Obsidian 링크를 제공한다:

```
✅ ingest 완료

[Source 파일명](obsidian://open?vault=workspace-obsidian&file=Sources/...)
[Wiki 페이지명](obsidian://open?vault=workspace-obsidian&file=Wiki/...)
```

vault 이름은 항상 `workspace-obsidian` 고정. 파일 경로는 vault 루트 기준 상대경로, `.md` 확장자 생략.
