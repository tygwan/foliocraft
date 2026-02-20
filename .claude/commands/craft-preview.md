# /craft-preview — Local Build & Serve

생성된 사이트를 로컬에서 빌드하고 프리뷰합니다.

## Usage

```
/craft-preview <project-name>
```

## Arguments

- `project-name`: workspace 내 프로젝트명 (필수, site/ 존재해야 함)

## Prerequisites

- `workspace/{project}/site/` 존재 (Phase 3 완료)
- `workspace/{project}/site/package.json` 존재

## Process

1. `workspace/{project}/site/` 디렉토리 확인
2. 의존성 설치: `npm install`
3. 빌드: `npm run build`
4. 프리뷰: `npm run preview` 또는 `npx serve dist/`

## Template-specific Commands

### sveltekit-dashboard
```bash
cd workspace/{project}/site
npm install
npm run build    # → .svelte-kit/output/
npm run preview  # → localhost:4173
```

### astro-landing
```bash
cd workspace/{project}/site
npm install
npm run build    # → dist/
npm run preview  # → localhost:4321
```

## Validation Checks

빌드 전 확인:
- [ ] `content.json`이 PLACEHOLDER가 아닌지 확인
- [ ] `tokens.css`가 PLACEHOLDER가 아닌지 확인
- [ ] `package.json`의 scripts에 build, preview 존재

## Example

```
/craft-preview n8n
```
