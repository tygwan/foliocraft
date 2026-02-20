<div align="center">

```
  ╔═══════════════════════════════════════════════════════╗
  ║                                                       ║
  ║     ██╗  ██╗  ██████╗  ██╗      ██╗  ██████╗         ║
  ║     ██║  ██║ ██╔═══██╗ ██║      ██║ ██╔═══██╗        ║
  ║     ███████║ ██║   ██║ ██║      ██║ ██║   ██║        ║
  ║     ██╔══██║ ██║   ██║ ██║      ██║ ██║   ██║        ║
  ║     ██║  ██║ ╚██████╔╝ ███████╗ ██║ ╚██████╔╝        ║
  ║     ╚═╝  ╚═╝  ╚═════╝  ╚══════╝ ╚═╝  ╚═════╝        ║
  ║                CRAFT                                  ║
  ║                                                       ║
  ╚═══════════════════════════════════════════════════════╝
```

**Your repo is the design brief.**

Git 레포를 분석하여 프로젝트별 고유 디자인의 포트폴리오 페이지를 자동 생성합니다.

<br />

<a href="https://github.com/tygwan/foliocraft/actions"><img src="https://img.shields.io/github/actions/workflow/status/tygwan/foliocraft/ci.yml?branch=main&style=for-the-badge&logo=github&label=CI" alt="CI"></a>&nbsp;
<a href="https://github.com/tygwan/foliocraft/releases"><img src="https://img.shields.io/github/v/release/tygwan/foliocraft?include_prereleases&style=for-the-badge&logo=semantic-release&color=ea4b71" alt="Release"></a>&nbsp;
<a href="https://github.com/tygwan/foliocraft/blob/main/LICENSE"><img src="https://img.shields.io/github/license/tygwan/foliocraft?style=for-the-badge&color=3ddbd9" alt="License"></a>&nbsp;
<a href="https://claude.ai"><img src="https://img.shields.io/badge/Powered_by-Claude_Code-7c5cff?style=for-the-badge&logo=anthropic" alt="Claude Code"></a>

</div>

<br />

---

## What is foliocraft?

foliocraft는 **Claude Code Agent Teams** 위에서 동작하는 포트폴리오 생성 파이프라인입니다.

기존 포트폴리오 빌더와 다른 점: 템플릿에 데이터를 끼워넣는 게 아니라, **코드 자체를 읽고 이해한 뒤** 그 프로젝트에 맞는 디자인·콘텐츠·시각화를 생성합니다.

```
$ claude /craft /path/to/your-repo

  ✔ Phase 1 — Analyzing repo with 3 agents...
  ✔ Phase 2 — Generating design profile...
  ✔ Phase 3 — Building site...
  ✔ Phase 4 — Deployed to GitHub Pages

  → https://yourname.github.io/your-repo/
```

---

## Pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│                         foliocraft pipeline                        │
│                                                                     │
│  Phase 1: ANALYZE        Phase 2: DESIGN     Phase 3: BUILD        │
│  ┌──────────────┐        ┌──────────────┐    ┌──────────────┐      │
│  │ code-analyst ─┤        │              │    │  page-writer │      │
│  │ story-analyst ┼──→ SUMMARY.md ──→ design  ──→ tokens.css │      │
│  │ stack-detector┤    (Lead 종합)    profile  │  content.json│      │
│  └──────────────┘        │    .yaml     │    │  site/       │      │
│        ↓                 │  (사람 검토)  │    │              │      │
│   3 Markdown files       └──────────────┘    │figure-designer│     │
│   with file:line refs                        │  → diagrams/  │     │
│                                              └───────┬──────┘      │
│                                                      ↓             │
│                                            Phase 4: DEPLOY         │
│                                            GitHub Pages / Vercel   │
└─────────────────────────────────────────────────────────────────────┘
```

### Phase별 산출물

| Phase | 산출물 | 형식 | 소비자 |
|:------|:-------|:-----|:-------|
| **1 — Analyze** | `architecture.md` `narrative.md` `stack-profile.md` | Markdown | 사람이 검토 |
| **2 — Design** | `design-profile.yaml` | YAML | 사람이 수정 가능 |
| **3 — Build** | `tokens.css` + `content.json` + `site/` | CSS / JSON | 템플릿 & 브라우저 |
| **4 — Deploy** | Static site | HTML | 방문자 |

---

## Agent System

5개의 전문 에이전트가 역할을 분담합니다.

| Agent | Phase | What it does | Output |
|:------|:-----:|:-------------|:-------|
| `code-analyst` | 1 | 아키텍처, 모듈 구조, 설계 의사결정 추출. 최소 8개 소스 파일 직접 읽음 | `architecture.md` |
| `story-analyst` | 1 | 프로젝트 내러티브, 마일스톤 타임라인, 임팩트 메트릭 | `narrative.md` |
| `stack-detector` | 1 | 프레임워크/빌드/배포 감지, 도메인 분류, 템플릿 추천 | `stack-profile.md` |
| `page-writer` | 3 | design-profile → `tokens.css` + `content.json` 생성, 템플릿 인스턴스화 | `site/` |
| `figure-designer` | 3 | 아키텍처 다이어그램, 데이터 흐름, 타임라인을 Mermaid/SVG로 | `diagrams/` |

> Phase 1의 3개 에이전트는 **병렬 실행**됩니다. Phase 2는 Lead가 종합 후 사람이 검토합니다.

---

## Templates

프로젝트 성격에 따라 최적의 프레임워크를 자동 선택합니다.

### Available

| Template | Stack | Best for | Key advantage |
|:---------|:------|:---------|:-------------|
| **sveltekit-dashboard** | SvelteKit 5 | 대시보드, 워크플로우, 애니메이션 | 내장 `transition:`, 0 virtual DOM, 작은 번들 |
| **astro-landing** | Astro 5 + Tailwind v4 | 제품 랜딩, 연구 쇼케이스 | 0KB JS 기본, GitHub Pages 최적 |

### Planned

| Template | Stack | Target |
|:---------|:------|:-------|
| `nextjs-app` | Next.js | React 생태계 프로젝트 |
| `hugo-research` | Hugo | 학술/논문 |
| `html-terminal` | Plain HTML | CLI/devtool |
| `nuxt-showcase` | Nuxt | Vue 프로젝트 |

### Project → Template Mapping

| Project | Template | Why |
|:--------|:---------|:----|
| **n8n** | sveltekit-dashboard | 워크플로우 노드 시각화, 다크 대시보드 |
| **DXTnavis** | astro-landing | 깔끔한 제품 페이지, 정적 콘텐츠 |
| **bim-ontology** | astro-landing | 학술/리서치 스타일 |
| **resumely** | nextjs-app | 기존 React/Next.js 프로젝트 |
| **ai-master-class** | sveltekit-dashboard | 커리큘럼 네비게이션, 진행도 |
| **physical-unity** | sveltekit-dashboard | 시뮬레이션 비주얼 쇼케이스 |

---

## Hybrid Design System

모든 사이트의 디자인은 **두 파일**로 결정됩니다:

<table>
<tr>
<td width="50%">

**`tokens.css`** — 디자인 토큰

```css
:root {
  --fc-bg-primary: #0d1117;
  --fc-text-primary: #e6edf3;
  --fc-accent: #ea4b71;
  --fc-accent-2: #3ddbd9;
  --fc-font-heading: 'Chakra Petch';
  --fc-radius: 18px;
}
```

</td>
<td width="50%">

**`content.json`** — 콘텐츠 데이터

```json
{
  "meta": { "title": "n8n" },
  "hero": {
    "headline": "...",
    "badges": ["TypeScript", "Vue 3"]
  },
  "sections": ["features", "architecture"],
  "metrics": [
    { "label": "Commits", "value": "2,847" }
  ]
}
```

</td>
</tr>
</table>

### Design Presets

<table>
<tr><th colspan="4">Palettes</th></tr>
<tr>
  <td>🌑 <b>automation</b><br/><sub>Dark + neon accents</sub><br/><code>#0d1117</code> <code>#ea4b71</code> <code>#3ddbd9</code></td>
  <td>🔵 <b>plugin-tool</b><br/><sub>Light/Dark dual</sub><br/><code>#ffffff</code> <code>#3b82f6</code> <code>#06b6d4</code></td>
  <td>🟣 <b>ai-ml</b><br/><sub>Deep dark + gradient</sub><br/><code>#0a0a1a</code> <code>#8b5cf6</code> <code>#06b6d4</code></td>
  <td>🖥️ <b>terminal</b><br/><sub>CLI aesthetic</sub><br/><code>#0d0d0d</code> <code>#e07a5f</code> <code>#4ecdc4</code></td>
</tr>
</table>

<table>
<tr><th colspan="3">Typography</th></tr>
<tr>
  <td><b>heading-sans</b><br/><sub>Chakra Petch + Manrope + JetBrains Mono</sub></td>
  <td><b>system-clean</b><br/><sub>Inter system stack</sub></td>
  <td><b>mono-terminal</b><br/><sub>JetBrains Mono + Pretendard</sub></td>
</tr>
</table>

---

## Commands

| Command | Description |
|:--------|:-----------|
| `/craft <repo>` | 전체 파이프라인 (Analyze → Design → Build → Deploy) |
| `/craft-analyze <repo>` | Phase 1만 — 3 agents 병렬 분석 |
| `/craft-design <project>` | Phase 2만 — Markdown → design-profile.yaml |
| `/craft-preview <project>` | 로컬 빌드 + 프리뷰 서버 |
| `/craft-deploy <project> <target>` | 배포 (github-pages / vercel / netlify) |

---

## Quick Start

```bash
# 1. Clone
git clone https://github.com/tygwan/foliocraft.git
cd foliocraft

# 2. Open in Claude Code
claude

# 3. Generate a portfolio for your project
/craft /path/to/your-project --deploy github-pages
```

---

## Project Structure

```
foliocraft/
├── CLAUDE.md                      # Pipeline documentation (for agents)
├── .claude/
│   ├── agents/                    # 5 agent definitions
│   └── commands/                  # 5 slash commands
├── design/
│   ├── palettes/                  # 4 color palettes (YAML)
│   ├── typography/                # 3 typography presets (YAML)
│   ├── layouts/                   # 2 layout archetypes (YAML)
│   └── domain-profiles/           # 8 domains → design mapping
├── templates/
│   ├── _tokens/                   # Token & content schemas
│   ├── sveltekit-dashboard/       # SvelteKit template
│   └── astro-landing/             # Astro template
└── workspace/                     # Per-project workspace (generated)
    └── {project}/
        ├── analysis/              # Phase 1 Markdown outputs
        ├── design-profile.yaml    # Phase 2
        ├── content.json           # Phase 3
        ├── tokens.css             # Phase 3
        └── site/                  # Phase 3 built site (gitignored)
```

---

## At a Glance

| | Count |
|:--|------:|
| AI Agents | **5** |
| Slash Commands | **5** |
| Template Stacks | **2** (+ 4 planned) |
| Design Palettes | **4** |
| Typography Presets | **3** |
| Layout Archetypes | **2** |
| Domain Profiles | **8** |
| Target Projects | **6** |

---

## Roadmap

- [x] Project scaffold + pipeline documentation
- [x] Agent definitions (code-analyst, story-analyst, stack-detector, page-writer, figure-designer)
- [x] Slash commands (craft, craft-analyze, craft-design, craft-preview, craft-deploy)
- [x] Design presets (palettes, typography, layouts, domain profiles)
- [x] Template skeletons (sveltekit-dashboard, astro-landing)
- [ ] Run `/craft-analyze` on n8n
- [ ] Run `/craft-analyze` on DXTnavis
- [ ] Component implementation (Hero, Section, Card, etc.)
- [ ] nextjs-app template
- [ ] hugo-research template
- [ ] html-terminal template
- [ ] README audit agent — Codex 기반 레포 분석으로 README 개선점 도출

---

## Philosophy

> **No two projects should look the same.**

기존 포트폴리오 빌더: 같은 템플릿 + 다른 데이터.
foliocraft: **다른 코드 → 다른 분석 → 다른 디자인 → 다른 사이트.**

에이전트가 실제 코드를 읽고, git 히스토리를 추적하고, 아키텍처를 파악한 뒤에야 디자인이 결정됩니다. 워크플로우 자동화 도구는 다크 대시보드가 되고, 브라우저 확장은 깔끔한 제품 랜딩이 됩니다.

---

<div align="center">
<sub>Built with <a href="https://claude.ai/claude-code">Claude Code</a> Agent Teams</sub>
</div>
