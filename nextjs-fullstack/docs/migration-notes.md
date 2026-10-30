# 이식 근거와 조정 내역

작성일: 2026-10-30

## 기획 자료 분해 스킬 추가

기존 PRD·유저 플로우·정책 등 여러 문서·폴더와 선택적 Figma 링크에서 여러 기능 스펙을 만드는 `prd-to-specs`를 별도로 작성했다. 원본 강의에서 가져온 스킬이 아니라 이 템플릿의 작업 흐름에 맞춘 한국어 스킬이다.

폴더의 하위 자료를 읽고 Figma MCP로 확인한 화면을 요구사항·유저 플로우·정책과 연결해 분해 계획·개별 스펙 초안·의존관계·구현 순서를 작성한다. `계획만` 요청도 지원한다. 제품 구현은 기존 `feature`로 이어가며, 스킬은 총 5개다.

## 템플릿 분리와 개발 영역 배치

같은 날 추가 요청에 따라 이 템플릿을 `스펙주도개발-템플릿/nextjs-fullstack/`으로 묶었다. 상위 폴더는 템플릿 목록만 관리하며 다른 기술 구성은 형제 폴더로 추가한다.

개발 소스·Prisma·패키지·앱 설정은 `apps/web/`로 모았다. 스펙·스킬·문서는 프로젝트 루트에 유지하고 Docker 작업 위치, Vercel Root Directory, 스킬의 탐색 경로를 함께 변경했다. 루트 환경 변수 예시는 Docker 이름·포트용, 앱 환경 변수 예시는 `apps/web/.env.example`로 분리했다. 제품 소스는 계속 포함하지 않는다.

## 확인한 원본

사용자가 지정한 `learning-hub/courses/udemy/Coding with AI/devstash-final`의 `CLAUDE.md`, `context/`, `docs/` 구성을 기준으로 삼았다. 해당 디렉터리에는 `.claude/skills/`나 `.agents/skills/`가 없었다. 따라서 스킬 원본을 그대로 복사했다고 보지 않는다.

같은 강의 자료에서 아래 내용을 확인하고 **한국어 스킬로 재구성**했다.

| 자료 | 반영한 내용 |
| --- | --- |
| `module 7/03 Custom Slash Commands and Skills.md` | `list-components` |
| `module 7/04 Designing the Feature Skill Workflow.md` | 기능 작업 흐름 |
| `module 7/05 Implementing the Feature Skill.md` | `feature`와 동작별 참조 파일 |
| `module 7/07 Cleanup Skill.md` | `cleanup` 점검 항목 |
| `module 9/01 Research Skill and Project Documentation.md` | `research`, 조사 요청과 결과 분리 |
| `module 9/03 Adding Vitest to the Feature Workflow.md` | `feature test`, 의미 있는 서버 로직 검증 |

## 유지한 뼈대

- 프로젝트 개요 / 코딩 규칙 / AI 협업 규칙 / 현재 기능의 네 가지 공통 컨텍스트
- 기능 스펙, 조사 요청, 수정 기록, 사람을 위한 구조 문서의 분리
- 현재 기능의 목표·메모와 누적 완료 이력
- 불러오기 → 구현 → 검토 → 테스트 → 설명 → 완료 흐름

## 변경한 부분

- DevStash 제품 요구사항을 제거하고 새 서비스의 고객·문제·최소 기능을 적는 템플릿으로 바꿨다.
- Next.js 16 / TypeScript 5 / Node.js 24 / Prisma 7 / Tailwind CSS 4, Vercel / Neon을 기준으로 고정했다.
- React는 Next.js 안에서 사용하고 NestJS나 독립 SPA를 만들지 않도록 명시했다.
- 로컬 Docker 작업 환경과 PostgreSQL을 추가했다. Neon 프로젝트 ID·브랜치 ID·연결값은 옮기지 않았다.
- Prisma 7 방식으로 CLI 연결 URL, Client 생성 경로, 드라이버 어댑터를 정리했다.
- 원본의 새 브랜치 → 병합 → 브랜치 삭제를 현재 저장소의 `main` 작업 규칙으로 바꿨다.
- `complete`는 검증과 이력 정리이며 커밋·푸시는 요청 범위에 따른다. `cleanup run`도 이미 허용된 정리를 반복 확인하지 않도록 조정했다.
- 스킬 본문·설명·동작별 문서·출력을 한국어로 바꿨다. 기존 호출 이름을 유지하면서 한국어 동작 별칭을 추가했다.
- 코드 스캐너·인증 감사 자료는 별도 하위 에이전트 예시여서 이 스킬팩에 자동 등록하지 않았다. 인증 도구도 아직 선택하지 않았다.

## 제외한 것

원본의 `src/`, 프로토타입·이미지, 모델·시드·마이그레이션, 앱 전용 스크립트, 기능별 완성 스펙, 완료 이력, 빌드 결과, 의존성 잠금 파일, 비밀값을 복사하지 않았다. 패키지와 잠금 파일은 새 기술 구성으로 작성했다.

원본 문서에서 가져온 구조와 지침의 라이선스 고지는 `LICENSE.upstream`에 원문 그대로 보존했다. 라이선스 법적 원문은 스킬 번역 대상이 아니다.

## 기술 확인 자료

- [Next.js 설치와 버전 요구사항](https://nextjs.org/docs/app/getting-started/installation)
- [Prisma 7 변경 사항](https://www.prisma.io/docs/guides/upgrade-prisma-orm/v7)
- [Neon과 Prisma 연결](https://neon.com/docs/guides/prisma)
- [Tailwind CSS 4의 Next.js 설정](https://tailwindcss.com/docs/installation/framework-guides/nextjs)
- [Vercel Node.js 버전](https://vercel.com/docs/functions/runtimes/node-js/node-js-versions)
- [Docker Compose 시작 순서](https://docs.docker.com/compose/how-tos/startup-order/)
