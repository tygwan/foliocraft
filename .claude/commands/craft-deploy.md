# /craft-deploy — Deploy Site

생성된 사이트를 배포합니다.

## Usage

```
/craft-deploy <project-name> <target>
```

## Arguments

- `project-name`: workspace 내 프로젝트명 (필수)
- `target`: 배포 대상 — `github-pages` | `vercel` | `netlify` (필수)

## Prerequisites

- `workspace/{project}/site/` 존재 (Phase 3 완료)
- 로컬 빌드 성공 확인 (`/craft-preview` 권장)

## Deploy Targets

### GitHub Pages

1. 빌드 결과물을 별도 브랜치(`gh-pages`)로 푸시하거나 GitHub Actions 사용
2. 템플릿별 설정:
   - **sveltekit-dashboard**: `svelte.config.js`에서 `paths.base` 설정
   - **astro-landing**: `astro.config.mjs`에서 `site`, `base` 설정

```bash
# GitHub Actions 워크플로우 생성
# .github/workflows/deploy-{project}.yml
```

### Vercel

```bash
cd workspace/{project}/site
npx vercel --prod
```

### Netlify

```bash
cd workspace/{project}/site
npx netlify deploy --prod --dir=dist
```

## Post-deploy

- 배포 URL 출력
- 브라우저에서 확인 안내
- workspace에 배포 정보 기록

## Example

```
/craft-deploy n8n github-pages
/craft-deploy DXTnavis vercel
```
