# Next.js 풀스택 스펙 주도 개발 템플릿

DevStash의 문서 중심 개발 흐름을 바탕으로 만든 새 프로젝트용 뼈대다. **개발 소스와 앱 설정은 `apps/web/`, 기획·스펙·스킬은 템플릿 루트에 둔다.** 현재는 앱 설정만 있으며 실제 화면·제품 기능 소스는 없다.

처음 사용할 때는 [사용 가이드](사용가이드.md)를 읽는다.

## 기술 구성

| 구분 | 선택 |
| --- | --- |
| 애플리케이션 | Next.js 16 App Router 기반 풀스택 |
| 언어 / 실행 환경 | TypeScript 5 / Node.js 24 |
| 데이터 / 스타일 | PostgreSQL · Prisma 7 / Tailwind CSS 4 |
| 운영 환경 | Vercel / Neon PostgreSQL |
| 로컬 환경 | Docker Desktop / Node.js 24 / PostgreSQL 17 |

React는 Next.js 내부 UI 라이브러리로 사용한다. UI와 서버 로직 모두 `apps/web/`에 들어간다. 인증·결제·파일 저장소는 서비스 스펙에서 정한다.

## 구조

```text
nextjs-fullstack/                 ← 프로젝트 루트: AI 도구와 터미널을 여기에 연다
├── apps/
│   └── web/                      ← 개발 영역 / npm·Vercel 앱 루트
│       ├── README.md
│       ├── package.json
│       ├── package-lock.json
│       ├── .env.example          ← 앱 DB 연결 변수
│       ├── prisma/               ← 스키마, 이후 마이그레이션
│       ├── prisma.config.ts
│       └── 각종 앱 설정 파일
│       # src/와 public/은 첫 구현 때 이 위치에 생성
├── context/                      ← 개요·규칙·현재 작업·기능 스펙
├── docs/                         ← 구조·로컬 개발·배포·검증
├── .claude/skills/               ← 한국어 스킬 원본 5개
├── .agents/skills/               ← 같은 원본을 가리키는 상대 링크
├── CLAUDE.md / AGENTS.md          ← AI 작업 지침
├── compose.yaml / Dockerfile.dev ← 공통 로컬 환경
├── .env.example                  ← Docker 이름·포트
└── 사용가이드.md
```

`apps/`는 개발 파일을 구분하는 상위 폴더다. 현재 앱은 `web` 하나이며 별도 API 앱이나 npm 워크스페이스 설정은 필요하지 않다.

## 빠른 시작

먼저 PRD·유저 플로우·정책 등 문서나 자료 폴더와 선택적 Figma 링크를 제공해 스펙을 나눈다. 폴더는 하위 자료까지 함께 분석하며 PRD는 필수가 아니다. Figma가 있으면 연결된 MCP로 화면을 분석한다.

```text
$prd-to-specs context/inputs/ Figma: [디자인 링크, 없으면 생략]
```

분해 계획과 개별 스펙 초안을 검토한 뒤 `$feature`로 하나씩 구현한다. [사용 가이드](사용가이드.md)에 전체 흐름과 요청 예시가 있다.

구현·검증할 환경을 준비할 때는 현재 강의 저장소 루트에서:

```bash
cd 스펙주도개발-템플릿/nextjs-fullstack
cp .env.example .env
docker compose up -d --build
docker compose exec tools npm ci
docker compose exec tools npm run db:validate
```

이미 `.env`가 있으면 복사로 덮어쓰지 않는다. Docker Desktop이 실행 중이어야 한다. `tools`의 작업 위치는 자동으로 `apps/web/`에 맞춰진다. 루트에서 `npm ci`를 실행하는 구조가 아니다.

지금은 작업 환경과 DB만 실행된다. 앱 초기화는 [첫 앱 생성 스펙](context/features/000-project-bootstrap.md)을 사용한다.

```text
$feature 불러오기 000-project-bootstrap.md
$feature 시작
$feature 검토
$feature 테스트
$feature 설명
$feature 완료
```

Claude Code는 `$prd-to-specs`, `$feature` 대신 `/prd-to-specs`, `/feature`를 사용한다. 스킬은 이 템플릿 루트에서 새 세션을 열어 사용한다. 목록에 없으면 해당 `SKILL.md` 경로를 직접 지정한다.

| 스킬 | 역할 |
| --- | --- |
| `prd-to-specs` | 여러 문서·폴더·Figma MCP 화면 분석 → 분해 계획·의존관계·개별 스펙 초안 |
| `feature` | 스펙 불러오기·구현·검토·검증·설명·완료 |
| `research` | 조사 요청을 근거 있는 한국어 문서로 정리 |
| `cleanup` | 작업 기록·코드·문서·환경 변수 이름 점검과 정리 |
| `list-components` | `apps/web/src/components/`의 실제 구성 확인 |

## 상세 안내

- [사용 가이드](사용가이드.md): 기존 문서·자료 폴더·선택적 Figma 분석 → 기능 스펙 작성 → AI 구현 → 검증·완료 기록
- [로컬 개발](docs/local-development.md): Docker와 호스트 명령, 환경 변수 위치
- [배포](docs/deployment.md): Vercel 앱 루트, Neon 연결, 마이그레이션
- [구조](docs/architecture.md) / [이식 기록](docs/migration-notes.md) / [검증 기록](docs/verification.md)
