# 스펙 주도 개발 작업 지침

이 폴더 안의 프로젝트 작업에 적용한다. 상위 저장소의 교육 자료를 앱 요구사항으로 해석하지 않는다.

작업 전에 다음 문서를 순서대로 읽는다.

1. `context/project-overview.md`
2. `context/coding-standards.md`
3. `context/ai-interaction.md`
4. `context/current-feature.md`

Next.js 16 / TypeScript 5 / Node.js 24 / Prisma 7 / Tailwind CSS 4를 사용한다. 운영은 Vercel과 Neon PostgreSQL, 로컬은 Docker Desktop이다. UI와 서버 로직은 `apps/web/`의 하나의 Next.js 앱 안에 둔다.

이 폴더는 프로젝트 루트이며 AI 도구와 Docker Compose의 실행 위치다. 개발 소스·DB 스키마·패키지·앱 설정은 `apps/web/` 아래에만 작성한다. 루트에 `src/`, `app/`, `prisma/`, `package.json`을 만들지 않는다. 호스트 npm 명령은 `apps/web/`에서 실행하고, `docker compose exec tools`는 자동으로 그 위치에서 실행된다. 구조와 시작 순서는 `사용가이드.md`를 따른다.

PRD·유저 플로우·정책 등 여러 문서나 자료 폴더를 기능 스펙으로 나누는 요청에는 `.claude/skills/prd-to-specs/SKILL.md`를 따른다. 지정한 폴더의 하위 자료를 함께 읽고 자료 목록·읽기 상태·충돌을 기록한다. PRD는 필수가 아니다. Figma 링크가 있으면 MCP로 화면을 분석해 분해 근거로 사용한다. 분해 계획은 `context/spec-plan.md`, 개별 스펙은 `context/features/`에 기록하며 구현은 `feature` 흐름으로 이어간다.

현재는 개발 소스가 없는 템플릿이다. 앱 초기화를 요청받으면 `context/features/000-project-bootstrap.md`를 사용한다. 다른 서비스의 기능·모델·완료 이력을 임의로 추가하지 않는다.

스펙은 `context/features/`, 조사 요청은 `context/research/`, 조사 결과는 `docs/`, 수정 기록은 `context/fixes/`에 둔다. 현재 작업과 누적 이력은 `context/current-feature.md`에서 관리한다.

명령은 `apps/web/package.json`과 `docs/local-development.md`를 기준으로 실행한다. 호스트의 Node.js 버전이 달라도 검증은 Docker의 Node.js 24에서 한다. 실행하지 못한 검증은 미검증으로 보고한다.

답변·스펙·스킬 안내는 한국어로 작성한다. Git 작업은 `context/ai-interaction.md`를 따른다.
