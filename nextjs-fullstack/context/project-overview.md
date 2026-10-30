# 프로젝트 개요

## 서비스 정의 — 작성 필요

- 서비스 이름: 미정
- 고객: 누구의 어떤 상황을 돕는가?
- 문제: 고객이 지금 겪는 불편과 기존 대안의 한계는 무엇인가?
- 가치: 이 서비스를 쓰면 어떤 결과가 달라지는가?
- 첫 검증: 실제 고객 반응을 무엇으로 확인할 것인가?
- 최소 기능: 첫 실험에 필요한 기능은 무엇인가?
- 범위 밖: 이번에 만들지 않을 것은 무엇인가?
- 운영: 비용, 고객 지원, 데이터 보관 책임은 어떻게 감당할 것인가?

## 기술 결정

| 항목 | 기준 |
| --- | --- |
| 구조 | 하나의 Next.js 16 App Router 풀스택 앱 |
| UI | Next.js 안의 React 컴포넌트, Tailwind CSS 4 |
| 언어 | TypeScript 5, 엄격한 타입 검사 |
| 런타임 | Node.js 24, DB를 사용하는 서버 코드는 Node.js 런타임 |
| 데이터 | Prisma 7 + PostgreSQL |
| 운영 | Vercel + Neon PostgreSQL |
| 로컬 | Docker Desktop의 Node.js 24 + PostgreSQL 17 |

별도 NestJS API 서버나 React SPA는 구성하지 않는다. 화면 조회는 Server Component, 앱 내부 변경은 Server Action, HTTP 계약이 필요한 요청은 Route Handler를 기본으로 한다. 상세 경계는 `docs/architecture.md`를 따른다.

## 아직 결정하지 않은 항목

인증 방식, 권한 모델, 요금제, 결제, 메일, 파일 저장소, AI 연동은 서비스 요구사항이 생길 때 스펙으로 정한다. 원본 DevStash의 제품 모델과 외부 서비스는 기본값이 아니다.

## 현재 상태

프로젝트 루트에는 스펙·스킬·문서와 Docker 구성을 두고, 개발 소스·DB 스키마·앱 설정은 `apps/web/`에 모은다. 같은 템플릿 모음의 다른 템플릿과 개발 파일을 공유하지 않는다.

문서·스킬·개발 환경 설정만 준비되어 있다. 웹 앱, 제품 모델, 마이그레이션, 제품 테스트는 아직 없다. 첫 작업 후보는 `context/features/000-project-bootstrap.md`다.
