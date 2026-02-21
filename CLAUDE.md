# foliocraft

**Git 레포를 분석하여 프로젝트별 고유 디자인의 포트폴리오 페이지를 생성하는 Agent Teams 파이프라인.**

각 프로젝트의 코드, 히스토리, 도메인 특성을 분석하여 그 프로젝트에 최적화된 디자인과 콘텐츠를 자동 생성합니다.

---

## Pipeline

```
Phase 1: Analyze          Phase 2: Design          Phase 3: Build           Phase 4: Deploy
───────────────────       ──────────────────       ──────────────────       ──────────────────
 code-analyst ─┐                                    page-writer
 story-analyst ─┼─→ SUMMARY.md ─→ design-profile ─→ content.json  ─→ site/ ─→ GitHub Pages
 stack-detector─┘       .yaml          tokens.css     figure-designer     Vercel / Netlify
                    (Lead 종합)     (사람 검토/수정)   (Mermaid/SVG)
```

### Phase별 산출물 형식

| Phase | 산출물 | 형식 | 이유 |
|-------|--------|------|------|
| 1 — Analyze | architecture.md, narrative.md, stack-profile.md | **Markdown** | 사람이 검토. 근거(파일:라인) 포함 서술형 |
| 2 — Design | design-profile.yaml | **YAML** | 사람이 수정 가능. 주석 지원. 구조화 |
| 3 — Build | content.json | **JSON** | 템플릿 코드가 import. 기계 소비용 |
| 3 — Build | tokens.css | **CSS** | 브라우저가 직접 소비 |
| 3 — Build | site/ | **HTML/CSS/JS** | 빌드 가능한 정적 사이트 |

---

## Hybrid Design System

모든 포트폴리오 사이트는 두 파일로 디자인이 결정됩니다:

### tokens.css — 디자인 토큰
```css
:root {
  --fc-bg-primary: #0d1117;
  --fc-text-primary: #e6edf3;
  --fc-accent: #ea4b71;
  /* ... */
}
```
- 모든 커스텀 프로퍼티는 `--fc-` 프리픽스 사용
- `design-profile.yaml`의 palette + typography를 CSS로 변환

### content.json — 콘텐츠 데이터
```json
{
  "meta": { "title": "n8n", "tagline": "..." },
  "hero": { "headline": "...", "description": "..." },
  "sections": [...]
}
```
- 템플릿 컴포넌트가 import하여 렌더링
- Phase 1 분석 결과를 구조화된 데이터로 변환

---

## Agents

| Agent | Phase | 입력 | 출력 | 역할 |
|-------|-------|------|------|------|
| code-analyst | 1 | Git repo | `analysis/architecture.md` | 기술스택, 아키텍처, 코드 메트릭 분석 |
| story-analyst | 1 | Git repo | `analysis/narrative.md` | 내러티브, 마일스톤, 임팩트 추출 |
| stack-detector | 1 | Git repo | `analysis/stack-profile.md` | 프레임워크 감지, 템플릿/스택 추천 |
| page-writer | 3 | design-profile.yaml | `content.json` + `tokens.css` + `site/` | 사이트 빌드 |
| figure-designer | 3 | analysis/*.md | `diagrams/` | Mermaid 다이어그램, SVG 시각화 |

Phase 2 (design-profile.yaml 생성)는 Lead가 직접 수행합니다.

---

## Slash Commands

| Command | 설명 |
|---------|------|
| `/craft` | 전체 파이프라인 실행 (Phase 1→2→3→4) |
| `/craft-analyze` | Phase 1만 실행 (3 agents → Markdown) |
| `/craft-design` | Phase 2만 실행 (Markdown → design-profile.yaml) |
| `/craft-preview` | 로컬 빌드 + 서빙 |
| `/craft-deploy` | 배포 (GitHub Pages / Vercel / Netlify) |
| `/craft-sync` | 메인 포트폴리오(`dev/portfolio`)와 데이터 동기화 |

---

## Template Stacks

프로젝트 성격에 따라 최적의 프레임워크를 선택합니다.

### 현재 사용 가능

| 템플릿 | 스택 | 대상 프로젝트 타입 | 선택 이유 |
|--------|------|-------------------|-----------|
| sveltekit-dashboard | **SvelteKit** | 인터랙티브 대시보드, 워크플로우 시각화 | 내장 transition, 작은 번들, 애니메이션 |
| astro-landing | **Astro** | 정적 제품 랜딩, 연구 쇼케이스 | 0KB JS 기본, GitHub Pages 최적 |

### 추후 추가 예정

| 템플릿 | 스택 | 대상 |
|--------|------|------|
| nextjs-app | Next.js | React 생태계 프로젝트 (resumely) |
| hugo-research | Hugo | 학술/논문 프로젝트 |
| html-terminal | Plain HTML | CLI/devtool 프로젝트 |
| nuxt-showcase | Nuxt | Vue 프로젝트 |

### 프로젝트별 스택 매핑

| 프로젝트 | 템플릿 | 이유 |
|----------|--------|------|
| n8n | sveltekit-dashboard | 워크플로우 시각화, 애니메이션, 다크 대시보드 |
| DXTnavis | astro-landing | 깔끔한 제품 랜딩, 정적 콘텐츠 |
| bim-ontology | astro-landing | 학술/리서치 스타일 |
| resumely | nextjs-app | 기존 React/Next.js 프로젝트 |
| ai-master-class | sveltekit-dashboard | 커리큘럼 네비게이션, 진행도 애니메이션 |
| physical-unity | sveltekit-dashboard | 시뮬레이션 비주얼 쇼케이스 |

---

## Directory Structure

```
foliocraft/
├── CLAUDE.md                          ← 이 파일
├── .claude/
│   ├── agents/                        ← 에이전트 정의
│   │   ├── code-analyst.md
│   │   ├── story-analyst.md
│   │   ├── stack-detector.md
│   │   ├── page-writer.md
│   │   └── figure-designer.md
│   └── commands/                      ← 슬래시 커맨드
│       ├── craft.md
│       ├── craft-analyze.md
│       ├── craft-design.md
│       ├── craft-preview.md
│       └── craft-deploy.md
├── design/                            ← 디자인 프리셋
│   ├── palettes/                      ← 컬러 팔레트 (YAML)
│   ├── typography/                    ← 타이포그래피 (YAML)
│   ├── layouts/                       ← 레이아웃 아키타입 (YAML)
│   └── domain-profiles/               ← 도메인 → 디자인 매핑
│       └── index.yaml
├── templates/
│   ├── _tokens/                       ← 토큰/콘텐츠 스키마
│   │   ├── tokens.schema.yaml
│   │   └── content.schema.yaml
│   ├── sveltekit-dashboard/           ← SvelteKit 템플릿
│   └── astro-landing/                 ← Astro 템플릿
├── workspace/                         ← 프로젝트별 작업 공간 (gitignored site/)
│   └── {project}/
│       ├── analysis/                  ← Phase 1 Markdown 산출물
│       ├── design-profile.yaml        ← Phase 2
│       ├── content.json               ← Phase 3
│       ├── tokens.css                 ← Phase 3
│       └── site/                      ← Phase 3 빌드 결과 (gitignored)
├── references/                        ← 디자인 패턴 참조
└── examples/                          ← 예시
```

---

## Portfolio Integration

foliocraft로 생성된 사이트는 메인 포트폴리오(`/home/coffin/dev/portfolio`)와 연동됩니다.

### 데이터 흐름

```
foliocraft                              portfolio
workspace/{project}/                    src/data/
├── content.json  ──→ /craft-sync ──→   ├── projects.ts  (PortfolioSite[])
├── deploy.yaml   ──→             ──→   └── resume.ts    (mainProjects[].live)
└── site/         ──→ 배포 URL
```

### 매핑 규칙

| foliocraft (content.json) | portfolio (PortfolioSite) |
|--------------------------|--------------------------|
| `meta.title` | `title` |
| `meta.tagline` | `desc` |
| `tech_stack[].name` | `tech[]` |
| `deploy.url` | `url` |
| `deploy.status` | `status` |
| `features[].title` (top 3) | `projects[]` |

### deploy.yaml

`/craft-deploy` 실행 후 자동 생성. `/craft-sync`가 이 파일을 읽어 portfolio를 업데이트합니다.

---

## Conventions

- **CSS 프리픽스**: 모든 디자인 토큰은 `--fc-` 프리픽스 사용
- **파일 형식**: Phase 1 → Markdown, Phase 2 → YAML, Phase 3 → JSON/CSS
- **근거 포함**: 분석 문서의 모든 주장은 `파일:라인` 형태의 근거 포함
- **PLACEHOLDER 패턴**: 템플릿의 `content.json`, `tokens.css`는 PLACEHOLDER로 시작, page-writer가 교체
- **Workspace 격리**: 각 프로젝트 작업물은 `workspace/{project}/`에 격리
- **커밋 스타일**: Conventional Commits (`feat:`, `fix:`, `docs:` 등)
