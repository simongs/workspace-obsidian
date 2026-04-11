---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Knowledge Management
related:
  - "[[Obsidian]]"
  - "[[Steph Ango]]"
  - "[[PARA]]"
sources: []
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

Obsidian Bases는 [[Obsidian]]의 코어 기능으로, `.base` 파일을 통해 **frontmatter 기반 동적 뷰**를 생성한다. Notion의 데이터베이스 뷰와 유사하지만, 실체는 YAML 텍스트 파일이다.

## 작동 원리

`.base` 파일은 "스마트 폴더"다:

1. **필터** — vault 전체에서 조건에 맞는 노트를 자동 수집
2. **뷰** — 수집된 노트를 테이블, 카드, 리스트, 지도로 표시
3. **정렬** — 날짜, 이름, 평점 등으로 정렬

## 예시: Clippings.base

```yaml
filters:
  and:
    - note.categories.contains(link("Clippings"))
    - '!file.name.contains("Template")'
views:
  - type: table
    name: Clippings
    order: [file.name, author, created, published]
    sort:
      - property: created
        direction: DESC
```

이 파일이 하는 일: vault **어디에 있든**, frontmatter에 `categories: ["[[Clippings]]"]`가 있는 노트를 찾아서 테이블로 보여줌.

## [[PARA]] 구현에서의 역할

PARA를 폴더가 아닌 properties로 구현할 때, Bases가 **뷰 레이어**를 담당:
- `status: active`인 노트만 보여주는 "Projects" 뷰
- `status: archived`인 노트만 보여주는 "Archive" 뷰
- 노트를 이동하지 않고 `status` 값만 바꾸면 자동으로 뷰가 갱신됨

## 관련 개념

- [[Obsidian]] — Bases의 모체 앱
- [[Steph Ango]] — Bases를 핵심적으로 활용하는 vault 철학
- [[PARA]] — Bases로 폴더 없이 구현 가능
