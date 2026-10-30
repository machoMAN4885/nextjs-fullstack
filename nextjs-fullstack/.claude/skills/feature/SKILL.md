---
name: feature
description: 스펙과 현재 작업 기록을 기준으로 기능 불러오기, 구현, 검토, 테스트, 설명, 완료를 수행한다. 이 프로젝트의 기능 개발 흐름을 진행할 때 사용한다.
---

# 스펙 중심 기능 작업

프로젝트 루트의 `CLAUDE.md`와 `context/ai-interaction.md`를 읽는다. 기준 파일은 `context/current-feature.md`이며 모든 기록과 설명은 한국어로 작성한다.

PRD·유저 플로우·정책 등 여러 문서나 자료 폴더를 기능 스펙으로 나누는 작업은 `prd-to-specs`가 담당한다. 이 스킬은 작성된 개별 스펙을 구현·검증할 때 사용한다.

요청한 동작의 지침만 읽고 수행한다. 사용자가 여러 동작을 함께 요청했다면 그 범위 안에서 이어서 진행한다. 동작이 없으면 아래 사용법을 안내한다.

| 한국어 동작 | 기존 동작 | 지침 |
| --- | --- | --- |
| 불러오기 | `load` | [기능 불러오기](actions/load.md) |
| 시작 | `start` | [구현 시작](actions/start.md) |
| 검토 | `review` | [요구사항 검토](actions/review.md) |
| 테스트 | `test` | [검증](actions/test.md) |
| 설명 | `explain` | [구현 설명](actions/explain.md) |
| 완료 | `complete` | [완료 기록](actions/complete.md) |

```text
$feature 불러오기 000-project-bootstrap.md
$feature 시작
$feature 검토
$feature 테스트
$feature 설명
$feature 완료
```

Claude Code에서는 `$feature` 대신 `/feature`를 사용한다. 파일명과 함께 주어진 설명은 추가 요구사항으로 읽되 스펙과 충돌하면 확인한다. 도구가 인수를 `$ARGUMENTS`로 전달하면 같은 방식으로 해석한다.

현재 기능 상태는 `대기 → 진행 중 → 검토 중 → 완료`로 관리한다. 이전 기능이 진행 중인데 새 기능을 불러오면 기존 기록을 덮어쓰지 않고 전환 의도를 확인한다. 완료 이력은 누적하며 오래된 것부터 최신 순서로 유지한다.

새 브랜치 생성·자동 병합·자동 푸시를 수행하지 않는다. 작업 범위는 이 템플릿 프로젝트이며 상위 강의 자료는 포함하지 않는다.
