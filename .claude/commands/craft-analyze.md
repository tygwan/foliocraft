# /craft-analyze — Phase 1 Only

Phase 1 분석만 실행합니다. 3개 에이전트가 병렬로 Git 레포를 분석합니다.

## Usage

```
/craft-analyze <repo-path>
```

## Arguments

- `repo-path`: 분석할 Git 레포 경로 (필수)

## Process

1. `workspace/{project}/analysis/` 디렉토리 생성
2. 3개 에이전트 **병렬** 실행:
   - `code-analyst` → `analysis/architecture.md`
   - `story-analyst` → `analysis/narrative.md`
   - `stack-detector` → `analysis/stack-profile.md`
3. Lead가 3개 산출물을 읽고 `analysis/SUMMARY.md` 작성

## Output

```
workspace/{project}/analysis/
├── architecture.md    ← 기술 아키텍처 분석
├── narrative.md       ← 내러티브, 마일스톤, 임팩트
├── stack-profile.md   ← 스택 감지, 템플릿 추천
└── SUMMARY.md         ← Lead 종합 (핵심 인사이트 + 추천)
```

## SUMMARY.md Format

```markdown
# {프로젝트명} Analysis Summary

## Key Insights
- ...

## Recommended Template
`{template-name}` — 근거

## Design Direction
- Palette: ...
- Typography: ...
- Layout: ...

## Notable
특이사항, 주의점, 추가 조사 필요 항목.
```

## Example

```
/craft-analyze /home/coffin/dev/n8n
```
