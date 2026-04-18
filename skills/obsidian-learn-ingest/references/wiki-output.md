# Wiki Output Checklist

`obsidian-learn-ingest` 스킬의 Wiki 작성 단계 체크리스트.

공통 참조:
- Callout 스타일 → `skills/_shared/callout-guide.md`
- Wiki 위치 결정 → `skills/_shared/wiki-placement.md`
- Git commit + push + Obsidian 딥링크 → `skills/_shared/git-workflow.md`

---

## Sources 저장 위치 기준

| 소스 유형 | Sources 경로 |
|-----------|---------------|
| GitHub 레포 / 기술 URL (Mode B) | `Sources/github/YYYY-MM-DD_레포명.md` |
| 공식 문서 / 레퍼런스 (Mode C) | `Sources/docs/[도구명]/YYYY-MM-DD_페이지제목.md` (학습 중 이미 완성) |
| 일반 외부 소스 | `Sources/외부-YYYY-MM-DD-제목.md` |
| Daily에서 ingest | `Sources/daily/YYYY-MM-DD_제목.md` |
| Notes에서 ingest | `Sources/notes-YYYY-MM-DD-제목.md` |

---

## 0-8단계 순서

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

## 완료 후

→ `skills/_shared/git-workflow.md` 의 Commit + Push + Obsidian 딥링크 순서 수행
- commit prefix: **`ingest:`**
