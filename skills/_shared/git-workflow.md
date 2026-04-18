# Git Workflow (commit + push + Obsidian 딥링크)

이 vault의 모든 스킬이 공유하는 git 완료 절차.
vault는 GitHub private repo로 운영되므로 **commit 후 origin push까지 기본**.

---

## 1. Commit + Push

모든 파일 작업이 완료되면:

```bash
git add -A
git commit -m "$(cat <<'EOF'
[prefix]: [주제] — [핵심 요약]

- Sources: [생성/업데이트된 Source 파일]
- Wiki: [생성/업데이트된 Wiki 페이지 목록]
EOF
)" && git push origin main
```

### Prefix 규칙 (스킬별)

| 스킬 | commit prefix |
|------|--------------|
| obsidian-learn-ingest | `ingest:` |
| obsidian-ai-session-ingest | `archive:` |
| obsidian-query | `query:` |

---

## 2. Obsidian 딥링크 제공 (push 후)

```
✅ [작업 유형] 완료

[Source 파일명](obsidian://open?vault=workspace-obsidian&file=Sources/...)
[Wiki 페이지명](obsidian://open?vault=workspace-obsidian&file=Wiki/...)
```

**포맷 규칙**:
- vault 이름은 항상 `workspace-obsidian` 고정
- 파일 경로는 vault 루트 기준 **상대경로**, `.md` 확장자 생략

---

## 3. 안전 규칙

- `--no-verify`, `--no-gpg-sign` 등 훅 우회 금지 (사용자가 명시적으로 요청한 경우에만)
- push 실패 시 force push 자동 금지. 사용자에게 보고 후 해결 방향 상의
- 비-빠른-포워드 에러 발생 시 pull/rebase 여부를 사용자와 상의
