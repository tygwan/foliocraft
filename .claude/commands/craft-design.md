# /craft-design — Phase 2 Only

Phase 1 분석 결과(Markdown)를 읽어 design-profile.yaml을 생성합니다.

## Usage

```
/craft-design <project-name>
```

## Arguments

- `project-name`: workspace 내 프로젝트명 (필수, analysis/ 존재해야 함)

## Prerequisites

- `workspace/{project}/analysis/SUMMARY.md` 존재
- `workspace/{project}/analysis/architecture.md` 존재
- `workspace/{project}/analysis/stack-profile.md` 존재

## Process

1. `analysis/SUMMARY.md` 및 개별 분석 파일 읽기
2. `analysis/stack-profile.md`에서 추천 템플릿 확인
3. `design/domain-profiles/index.yaml`에서 도메인 프로파일 매칭
4. `design/palettes/`, `design/typography/`, `design/layouts/`에서 프리셋 선택
5. 프로젝트 특성에 맞게 커스터마이즈
6. `workspace/{project}/design-profile.yaml` 생성

## Output

```yaml
# design-profile.yaml
project: n8n
template: sveltekit-dashboard
domain: automation

palette:
  bg:
    primary: "#0d1117"
    secondary: "#161b22"
    card: "#1c2130"
  text:
    primary: "#e6edf3"
    secondary: "#8b949e"
    muted: "#484f58"
  accent:
    primary: "#ea4b71"
    secondary: "#3ddbd9"
  border: "rgba(255,255,255,0.08)"

typography:
  heading: "'Chakra Petch', sans-serif"
  body: "'Manrope', sans-serif"
  mono: "'JetBrains Mono', monospace"

layout:
  type: dashboard  # or landing
  radius: "18px"
  max_width: "1200px"

sections:
  - hero
  - features
  - architecture
  - tech-stack
  - metrics
  - timeline
```

## After Generation

생성 후 사용자에게 검토 요청:
- "design-profile.yaml을 생성했습니다. 검토 후 수정할 부분이 있으면 알려주세요."
- 사용자가 확정하면 Phase 3 진행 가능

## Example

```
/craft-design n8n
```
