---
created: 2026-04-18
updated: 2026-04-18
tags:
  - claude-design
  - claude-code
  - anthropic-labs
  - design-tool
sources:
  - "[[Sources/docs/claude-design/2026-04-18_get-started-with-claude-design]]"
---

# Claude Design

Anthropic Labs가 제공하는 **대화형 디자인 생성 도구**(research preview). 채팅으로 요구사항을 말하면 Claude가 캔버스에 UI를 만들고, 이어지는 대화로 iterate하며 최종적으로 Claude Code로 핸드오프한다.

## 핵심 요약

- **제공**: Anthropic Labs, research preview
- **플랜**: Pro, Max, Team, Enterprise (Enterprise는 기본 OFF)
- **UI**: 좌측 Chat · 우측 Canvas
- **차별점**: 조직 디자인 시스템 자동 상속 + Claude Code로 바로 핸드오프

## 철학

> "Treat it as a design collaborator, not just a generator." — 공식 문서

**첫 생성은 출발점일 뿐**이고 iteration이 실질적 가치 창출 단계다. Chat(광범위) / Inline Comment(세부) / "Save & try different approach"(분기 탐색) 3가지 인터랙션 채널을 상황에 맞게 조합한다.

## 해결하는 3가지 문제

1. **비디자이너 진입 장벽** — 피그마 없이도 UI를 만들 수 있다.
2. **브랜드 일관성 반복 작업** — 조직 디자인 시스템이 프로젝트 생성 시 자동 주입된다.
3. **디자인→개발 핸드오프 마찰** — Export 경로 중 "Handoff to Claude Code"가 있어 코드 전환이 원스텝이다.

## 핵심 개념

### Project — 컨텍스트 컨테이너

프로젝트 단위로 **디자인 시스템 + 첨부 자료 + 대화 히스토리 + 캔버스 결과물**이 묶인다.

첨부 가능한 컨텍스트:
- 시각 자료: 스크린샷, 이미지, 에셋, 슬라이드 덱
- 구조 자료: 코드 레포 링크, 기존 디자인 파일

> [!warning] 대형 레포 주의
> 모노레포 전체를 연결하면 lag/브라우저 이슈. **특정 서브디렉토리만** 링크 권장.

### 3채널 인터랙션

| 채널 | 위치 | 용도 | 예시 |
|------|------|------|------|
| **Chat** | 좌 | 광범위 변경 | "Make the color scheme darker" |
| **Canvas** | 우 | 결과 뷰 + Comment 대상 | (직접 대상 아님) |
| **Inline Comment** | 캔버스 요소 핀 | 세부 변경 | "Make this button padding larger" |

> [!warning] Inline Comment 유실 이슈
> 가끔 Claude에게 전달되기 전 사라짐 → Chat에 같은 피드백 붙여넣기가 공식 워크어라운드.

### 프롬프트 4요소

좋은 프롬프트는 **Goal + Layout + Content + Audience**를 담는다.

| 요소 | 의미 |
|------|------|
| Goal | 무엇을 만드는가 |
| Layout | 어떻게 배치하는가 |
| Content | 어떤 정보를 보여주는가 |
| Audience | 누가 쓰는가 |

부족하면 Claude가 clarifying question을 먼저 던진다.

### Export & Handoff

| 형식 | 용도 |
|------|------|
| zip | 에셋 통째로 |
| PDF / PPTX | 스테이크홀더 리뷰 · 프레젠테이션 |
| Send to Canva | 디자이너 추가 편집 |
| standalone HTML | 단일 파일 공유·데모 |
| **Handoff to Claude Code** | ⭐ local coding agent 또는 Claude Code Web |

## 아키텍처 개요

> [!example] End-to-End 파이프라인
> `[Project 생성]` → `[컨텍스트 주입]` → `[프롬프트]` → `[Canvas 생성]` → `[Iteration 루프]` → `[Export/Handoff]`
>
> ```
> Organization Design System (자동 상속)
>               │
>               ▼
>        Project (컨텍스트 컨테이너)
>         ├─ Chat (좌)
>         ├─ Canvas (우)
>         └─ Inline Comment
>               │
>               ▼
>       Export / Handoff (6경로)
> ```

## 프로젝트 유형 (관찰 — 공식 문서 미명시)

프로젝트 생성 시 UI에서 4개 탭 제공:

| 탭 | 설명 |
|----|------|
| **Prototype** | 동작하는 화면 UI. `Wireframe` (저충실도) / `High fidelity` (고충실도) 2종 |
| **Slide deck** | 프레젠테이션 슬라이드 |
| **From template** | 미리 만들어진 템플릿 기반 |
| **Other** | 자유 형식 |

> [!info] Wireframe vs High fidelity
> UX 업계 표준 어휘(low-fi/hi-fi)를 Claude Design이 그대로 가져온 것으로 추정. 공식 문서에는 이 구분이 명시되지 않았다.
> - **Wireframe**: 그레이스케일·플레이스홀더 중심. 레이아웃 합의용. 빠름.
> - **High fidelity**: 실제 컬러·타이포·콘텐츠까지 완성. **조직 디자인 시스템 자동 반영**. 핸드오프에 유리.

## 베스트 프랙티스

> [!tip] 공식 문서 권장
> 1. **Start simple, then layer in complexity** — 핵심 레이아웃부터, 인터랙션·엣지케이스·폴리시는 나중.
> 2. **Be specific in your feedback** — "This doesn't look right" ❌ / "Tighten form field spacing to 8px" ✅
> 3. **Reference your design system** — "Use the Primary Button component"처럼 컴포넌트 이름 지정.
> 4. **Think about responsiveness early** — 모바일/태블릿/데스크톱 중 어디 필요한지 명시.
> 5. **Ask for variations** — "Show me 2–3 options"로 대안 비교.
> 6. **Ask Claude for feedback** — Claude에게 accessibility·대비·정보 위계·사용성 리뷰 요청 가능.

## 알려진 제약 (research preview)

| 이슈 | 워크어라운드 |
|------|------------|
| Compact view 저장 에러 | Full view로 전환 후 재시도 |
| 대형 레포 lag | 서브디렉토리만 링크 |
| Chat upstream error | 같은 프로젝트 내 새 chat 탭 |
| Inline Comment 유실 | Chat에 같은 피드백 붙여넣기 |

## 실습 경험 — 첫 인상

2026-04-18 URL shortener 3화면 샘플을 **Prototype > High fidelity**로 생성한 기록:
- [[../../Sources/docs/claude-design/2026-04-18_get-started-with-claude-design|Sources 원문 + 실습 기록]]

**얻은 교훈**:
- 프롬프트에 구체적으로 명시한 QR 코드·Top bar가 **첫 생성에서는 누락**됨 → iteration 필수.
- Claude가 도메인 관습(업계 표준 기능, KPI 카드 등)을 **자발적으로 주입**함 → "Start simple" 유지하려면 의도적 배제 명시 필요.
- 브랜드명("snip.sh")도 자체 생성 → 디자인 시스템이 없는 개인 계정에서는 Claude의 기본 해석이 결과를 결정한다.

## 관련 개념

- [[../배경지식/Tool/Obsidian|Obsidian]] — Knowledge base와 디자인 결과물 연결
- [[../배경지식/People/Andrej Karpathy|Andrej Karpathy]] — "IDE는 Obsidian, LLM이 프로그래머" 철학과 유사 패턴 (Claude Design의 "chat이 IDE")

## 참고

- 공식 문서: [Get started with Claude Design](https://support.claude.com/en/articles/14604416-get-started-with-claude-design)
- 관련 문서: Set up your design system in Claude Design (링크만 언급됨)
- 과금: Claude Design subscription usage and pricing (별도 문서)

## 학습 예정 (Layer 4 Deep Dive)

- 조직 디자인 시스템 **세팅 방법** (design lead 전용)
- Claude Code 핸드오프 **실제 워크플로우**
- 코드 레포 링크 시 **어떤 파일을 읽는지** 범위
- Inline Comment가 사라지는 **근본 원인**
