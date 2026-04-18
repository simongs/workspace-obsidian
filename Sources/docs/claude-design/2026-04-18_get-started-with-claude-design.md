---
created: 2026-04-18
tags:
  - claude-design
  - claude-code
  - anthropic-labs
  - docs
source-type: docs
author: "[[Anthropic]]"
url: "https://support.claude.com/en/articles/14604416-get-started-with-claude-design"
topics:
  - "[[Claude Design]]"
wiki:
  - "[[Wiki/Claude Code/claude-design]]"
---

# Get started with Claude Design — 학습 및 실습 기록

공식 문서를 Mode B (Tech Progressive Learning) 방식으로 학습한 뒤, URL shortener 3화면 샘플을 직접 실습한 기록.

---

## 공식 문서 원문

> 출처: https://support.claude.com/en/articles/14604416-get-started-with-claude-design
> 2026-04-18 defuddle로 수집

Claude Design by Anthropic Labs lets you create designs, interactive prototypes, presentations, and more by having a conversation with Claude. This guide walks you through creating your first project, iterating on designs, and getting the most out of the tool.

Claude Design by Anthropic Labs is now available in research preview to Pro, Max, Team, and Enterprise plans. This capability is default off for Enterprise plans.

This guide assumes your organization's design system has already been set up, so everything you create will automatically use your brand's colors, typography, and component patterns. If you're a design lead who needs to set up or modify the design system itself, see **Set up your design system in Claude Design**.

### Layout

Claude Design has two main areas: a chat interface on the left and a canvas on the right. You describe what you want in the chat, and Claude generates a working design on the canvas. From there, you iterate—refining through conversation and inline comments until it's right.

The typical flow is:
1. Create a project and add any relevant context (screenshots, a codebase).
2. Describe what you want to build.
3. Review what Claude generates on the canvas.

### Project context

When you create a project, it automatically inherits your organization's design system. You don't need to upload brand assets or configure anything—your brand colors, fonts, and components are already in place.

The more context you give Claude, the better your output will be. You can attach reference material at any point during a project.

- **Screenshots, images, or existing assets:** Upload screenshots of existing designs, competitor products, wireframes, or visual inspiration. You can also attach an existing slide deck or document with a design style you want to replicate. Useful for "make it look like this" requests.
- **Codebases and existing design files:** Link a code repository so Claude understands your existing components, architecture, and styling patterns. This makes prototypes more production-ready from the start. Import also supports multiple ways to upload existing product design work.

### Writing good prompts

A good prompt includes the **goal** (what you're building), the **layout** (how things should be arranged), the **content** (what information to display), and the **audience** (who will use it). Claude will also ask clarifying questions if it needs more information.

Examples of prompts that work well:
- "Create a dashboard showing monthly revenue with filters for region and product line."
- "Design a mobile app onboarding flow with 4 screens that walks users through our core features."
- "Build a landing page for our new API product with a hero section, code examples, and pricing."
- "Create a form for collecting customer feedback with conditional questions based on category."
- "Design an internal tool for our ops team to review and approve content submissions."

### Iterating

The first generation is a starting point. The real value comes from iterating.

**Chat** is best for broad changes:
- "Make the color scheme darker and more minimal."
- "Rearrange the dashboard so metrics are in the top row and the chart is below."
- "Add a settings panel on the right side."
- "Show me 2–3 alternative layouts for this page."

**Inline comments** for element-level changes:
- "Make this button padding larger."
- "Change this to a dropdown instead of radio buttons."
- "Use the primary brand color here."
- "Make this section collapsible."

**Note:** If your comments aren't being picked up, paste the feedback directly into the chat instead. This is a known workaround for an intermittent issue where comments can disappear before Claude reads them.

If you want to explore a different direction without losing your current work, tell Claude: "Save what we have and try a completely different approach." Claude will save your current project and confirm where it's saved, so you can reference earlier iterations in the conversation easily.

### Export

Use the "Export" button in the upper right corner when viewing your project to choose from the following export formats:

- Download as .zip
- Export as PDF
- Export as PPTX
- Send to Canva
- Export as standalone HTML
- Handoff to Claude Code
  - Send to local coding agent
  - Send to Claude Code Web

### Best practices

- **Start simple, then layer in complexity.** Begin with the core layout and content, then add interactions, edge cases, and polish. Claude responds well to incremental requests.
- **Be specific in your feedback.** "This doesn't look right" is hard to act on. "Tighten the spacing between form fields to 8px" gives Claude exactly what it needs.
- **Reference your design system.** If you know a component exists in your brand's system, mention it by name: "Use the Primary Button component" or "Apply the Card layout pattern."
- **Think about responsiveness early.** Mention whether your design needs to work on mobile, tablet, and desktop, or just one of those.
- **Ask for variations.** If you're unsure about a direction, ask Claude to show you 2–3 options. Comparing alternatives is much faster than guessing.
- **Ask Claude for feedback.** Claude can review your design for accessibility, contrast ratios, information hierarchy, and general usability. Treat it as a design collaborator, not just a generator.

### Known limitations (research preview)

- **Save errors in compact view:** The compact layout mode can trigger save errors. If this happens, switch to full view and retry.
- **Large codebases:** Linking very large repositories may cause lag or browser issues. Link specific subdirectories rather than entire monorepos.
- **Chat errors:** If you hit a "chat upstream error," try starting a new chat tab within the same project.

---

## 학습 Q&A (Mode B Layer 0-3)

공식 문서를 Bird's Eye View → Problem & Value → Core Concepts → Architecture 순서로 층위별로 학습.

### Layer 0 — Bird's Eye View (공식 문서 기반)

- **카테고리**: AI 기반 디자인 생성 + 프로토타이핑 도구
- **제공 주체**: Anthropic Labs (research preview)
- **사용 가능 플랜**: Pro, Max, Team, Enterprise (Enterprise는 기본 OFF)
- **주요 사용자**: 디자인 리드, PM, 개발자, 비디자이너 모두
- **생태계**: Claude + Claude Code + Canva + 조직 디자인 시스템

### Layer 1 — 해결하려는 3가지 문제

> [!important] 공식 문서에서 직접 읽힌 문제
> **① "디자이너가 아니면 디자인을 시작하기 어렵다"**
> > "You don't need to be a designer to get great results."
>
> **② "브랜드 일관성 유지가 반복 작업이다"**
> > "automatically inherits your organization's design system. You don't need to upload brand assets or configure anything."
>
> **③ "디자인→개발 핸드오프가 끊어진다"**
> > "Handoff to Claude Code — Send to local coding agent / Send to Claude Code Web"

> [!tip] 제품 철학
> > "Treat it as a design collaborator, not just a generator."
> 단순 생성기가 아니라 디자인 파트너. 반복(iterate)이 기본 전제.

### Layer 2 — 3개 핵심 개념

**① Project (컨텍스트 컨테이너)**
- 조직 디자인 시스템 자동 주입
- 스크린샷 · 이미지 · 에셋 · 슬라이드 · 코드 레포 · 기존 디자인 파일 첨부 가능
- "Save what we have and try a completely different approach" → 스냅샷 + 분기 탐색

**② Chat / Canvas / Inline Comment (3채널 인터랙션)**

| 채널 | 용도 |
|------|------|
| Chat (좌) | 광범위 변경 (톤, 레이아웃 구조, 분기 탐색) |
| Canvas (우) | 결과물 뷰, Inline Comment 대상 |
| Inline Comment | 세부 변경 (버튼 패딩, 드롭다운 전환 등) |

> [!warning] 알려진 이슈
> Inline Comment가 가끔 Claude에게 전달되기 전에 사라짐 → Chat에 같은 피드백 붙여넣기가 공식 워크어라운드.

**③ Export & Handoff (6가지 출력 경로)**

| 형식 | 용도 |
|------|------|
| zip / PDF / PPTX | 공유, 스테이크홀더 리뷰 |
| Send to Canva | 디자이너 추가 작업 |
| standalone HTML | 단일 파일 공유/데모 |
| **Handoff to Claude Code** | ⭐ local agent 또는 Web — 디자인→코드 |

### Layer 3 — Architecture

> [!example] Claude Design End-to-End 파이프라인
> `[프로젝트 생성]` → `[컨텍스트 주입]` → `[프롬프트]` → `[캔버스 생성]` → `[Iteration 루프]` → `[Export / Handoff]`

---

## 실습 기록 — URL Shortener 3화면 샘플 (2026-04-18)

### 사용한 프롬프트 v1

```
Create a URL shortener web app with 3 screens:

Screen 1 — Login
Minimal centered form with email + password fields, a primary "Sign in" button,
and a small "Create account" text link below. Clean SaaS style.

Screen 2 — User Dashboard (after login)
- Top bar: service logo on the left, user avatar + logout on the right
- Center: a large input field with placeholder "Paste a long URL here"
  and a primary "Shorten" button next to it
- Result card (shown after shortening): the generated short URL in a monospace
  style, a "Copy" button, and a QR code image on the right side of the card
- Below the result: a compact list of "My recent short URLs"

Screen 3 — Admin Dashboard
- Top bar: "Admin" title + search input on the right
- A table listing all short URLs with columns:
  Short URL, Original URL (truncated), Created by (email), Created at, Click count
- Sort by Click count descending by default
- Row hover reveals a subtle "View details" action

Style: Clean SaaS aesthetic inspired by Linear and Vercel. Light mode.
Generous whitespace. Modern sans-serif typography. Rounded corners (8px). Subtle shadows.

Audience: Regular users for Screens 1–2. Service operators for Screen 3.
```

### 선택한 프로젝트 탭

**Prototype > High fidelity** 선택.

### 첫 생성 결과

![[Attachments/claude-design/2026-04-18_sample-user-dashboard.png]]

![[Attachments/claude-design/2026-04-18_sample-admin-dashboard.png]]

### 관찰 — 프롬프트 vs 결과 gap

> [!success] Claude가 자발적으로 추가한 요소
> - 브랜드명 **"snip.sh"** 자체 생성
> - `Auto slug / Custom slug / Expires / Password` 옵션 칩 (URL shortener 업계 표준 기능 반영)
> - Admin **4개 KPI 카드** (Total Links · Total Clicks · Active Users · CTR) + 델타 %
> - 클릭 수 옆 분포 bar 시각화
> - "Last synced 2m ago", 아바타 이니셜+컬러, Export CSV 버튼
> - 하단 화면 전환 탭 `1. Login / 2. Dashboard / 3. Admin` (프로토타입 내비게이션)

> [!failure] 프롬프트에 있었지만 첫 생성에서 빠진 요소
> - **QR 코드** (Screen 2 결과 카드의 핵심 요소)
> - **Copy 버튼** (결과 카드 자체가 렌더링 안 됨 — 입력 전 상태만 생성)
> - **Top bar (로고 + avatar + logout)** — Screen 2
> - **Admin 검색창** → 드롭다운 필터로 재해석됨

### 교훈 — 2026-04-18 실습에서 얻은 것

> [!insight] 첫 생성은 출발점이다
> 공식 문서가 말한 "The first generation is a starting point"가 그대로 적용됨. 프롬프트를 꽤 구체적으로 짰음에도 핵심 요구사항(QR 코드)이 누락될 수 있다. Iteration이 도구의 본체다.

> [!insight] Claude는 도메인 관습을 주입한다
> "URL shortener"라는 키워드 하나로 Auto slug / Expires / Password / KPI 카드 같은 업계 표준을 자발적으로 추가함. 이건 프롬프트 "Start simple" 원칙과 충돌할 수 있으므로 의도적으로 빼야 할 때는 명시 필요.

> [!insight] High fidelity의 조직 디자인 시스템 부재
> 개인 계정에 조직 디자인 시스템이 없으므로 기본 디자인(인디고 primary, 라이트 모드 웜톤)으로 생성됨. 브랜드 토큰을 프롬프트에서 지정해야 적용됨.

---

## 결론

- Claude Design은 **"대화로 디자인을 만들고, 대화로 고치고, 코드로 넘기는"** 대화 중심 도구다.
- Mode B 학습 완료 후 Mode A(Document Ingest)로 샘플 이미지를 정리한 실습 사이클이 잘 작동함.
- Layer 4 (Deep Dive): research preview라 공식 자료가 부족하므로 학습 **예정**으로 남김.
