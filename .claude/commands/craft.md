# /craft — Full Pipeline

전체 포트폴리오 생성 파이프라인을 실행합니다.

## Usage

```
/craft <repo-path> [--deploy github-pages|vercel|netlify]
```

## Arguments

- `repo-path`: 분석할 Git 레포 경로 (필수)
- `--deploy`: 배포 대상 (선택, 미지정 시 사전 질문)

## Pre-flight Questions

배포 옵션이 미지정인 경우 사전 질문:
1. **배포 방식**: GitHub Pages / Vercel / Netlify / 로컬만
2. **커스텀 도메인**: 사용 여부 및 도메인명
3. **base path**: GitHub Pages인 경우 `/{repo-name}/` 자동 설정

## Pipeline

### Phase 1: Analyze
3개 에이전트를 **병렬** 실행합니다:
- `code-analyst` → `workspace/{project}/analysis/architecture.md`
- `story-analyst` → `workspace/{project}/analysis/narrative.md`
- `stack-detector` → `workspace/{project}/analysis/stack-profile.md`

완료 후 Lead가 `analysis/SUMMARY.md`를 작성하여 종합합니다.

### Phase 2: Design
분석 결과를 종합하여 `workspace/{project}/design-profile.yaml`을 생성합니다.
- `design/domain-profiles/index.yaml`에서 도메인 프로파일 참조
- `design/palettes/`, `design/typography/`에서 프리셋 선택/커스터마이즈
- 사용자 검토 후 확정

### Phase 3: Build
- `page-writer` → content.json + tokens.css + site/ 인스턴스화
- `figure-designer` → diagrams/ 생성
- 빌드 검증: `cd workspace/{project}/site && npm install && npm run build`

### Phase 4: Deploy
선택된 배포 방식에 따라 실행합니다.

## Example

```
/craft /home/coffin/dev/n8n --deploy github-pages
```
