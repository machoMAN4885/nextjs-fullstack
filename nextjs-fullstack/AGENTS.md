# Codex 지침

이 디렉터리의 `CLAUDE.md`를 읽고 따른다. 모든 프로젝트 상대 경로는 이 디렉터리를 기준으로 해석한다. 상위 강의 저장소의 다른 파일을 기능 구현 범위에 포함하지 않는다.

개발 파일은 `apps/web/` 안에 둔다. 스펙·현재 작업은 `context/`, 설명 문서는 `docs/`에 둔다. AI 세션은 이 프로젝트 루트에서 시작한다.

스킬 원본은 `.claude/skills/`, Codex 진입점은 `.agents/skills/`의 상대 심볼릭 링크다. 원본을 수정하고 두 벌로 복사해 관리하지 않는다.

| Codex | Claude Code | 역할 |
| --- | --- | --- |
| `$prd-to-specs` | `/prd-to-specs` | 여러 문서·폴더·Figma 화면을 기능 스펙으로 분해 |
| `$feature` | `/feature` | 스펙 중심 기능 작업 |
| `$research` | `/research` | 조사·문서 작성 |
| `$cleanup` | `/cleanup` | 정리 점검·수정 |
| `$list-components` | `/list-components` | 컴포넌트 목록 확인 |

설치한 스킬은 새 세션에서 확인한다. Git 커밋·푸시는 `main`에서만 수행하며 새 브랜치를 만들지 않는다.
