# 첫 앱 생성

## 상태

대기 — 템플릿 준비 완료와 앱 구현 완료는 별개다.

## 목표

기존 설정을 사용해 하나의 Next.js 16 풀스택 앱을 시작하고, Docker PostgreSQL에 Prisma 7로 연결할 수 있음을 확인한다.

## 범위

빈 시작 화면, 공통 스타일, 서버 전용 Prisma 연결 모듈까지 구현한다. 제품 기능, 로그인, 결제, AI, NestJS, 별도 React 앱은 만들지 않는다. `create-next-app@latest`로 현재 문서·설정·메이저 버전을 덮어쓰지 않는다.

## 구현 계획

개발 파일은 모두 `apps/web/` 아래에 만든다. 스펙과 현재 기능 기록은 프로젝트 루트의 `context/`에 유지한다. 아래 npm 명령은 `apps/web/`에서 실행하거나 루트에서 `docker compose exec tools`를 앞에 붙인다.

1. `apps/web/src/app/layout.tsx`, `apps/web/src/app/page.tsx`, `apps/web/src/app/globals.css`를 만든다. 문서 언어는 한국어, 페이지는 서비스 시작용 최소 화면으로 한다.
2. CSS에서 `@import "tailwindcss";`를 사용한다. 기존 PostCSS·TypeScript·Next.js 설정을 유지한다.
3. 빈 스키마를 유지하고 `npm run db:generate`를 실행한다. 잠금 파일의 Prisma 7에서는 기본 명령으로 모델 없는 연결 확인용 Client를 생성한다. 검증을 위한 가짜 제품 모델을 만들지 않는다.
4. 첫 도메인 모델과 마이그레이션은 이후 해당 기능 스펙에서 추가한다. 이 초기화 단계에는 스키마 변경이 없으므로 마이그레이션이 필요하지 않다.
5. `apps/web/src/lib/prisma.ts`에 `server-only`를 선언하고 생성된 Client와 `PrismaPg`를 사용한다. 어댑터에는 `DATABASE_URL`을 넘기고, 개발 핫 리로드에서도 연결을 재사용한다. 로컬은 일반 TCP, Neon은 제공된 TLS 연결 문자열을 사용하며 인증서 검증을 끄지 않는다.
6. 읽기 전용 `SELECT 1`로 Prisma 연결을 검증한다. 점검용 공개 API를 불필요하게 만들지 않는다.

## 수용 기준

- [ ] AC-1: Docker Node.js 버전이 24이며 의존성이 지정 메이저 범위를 유지한다.
- [ ] AC-2: 컨테이너에서 `npm run dev` 실행 후 `http://localhost:3000`에서 시작 화면과 Tailwind 스타일을 확인한다.
- [ ] AC-3: `npm run lint`, `npm run typecheck`, `npm run build`가 통과한다.
- [ ] AC-4: 모델 없는 Client 생성과 Prisma를 통한 로컬 DB 연결 확인이 통과한다.
- [ ] AC-5: `.env`, 생성 Client, 빌드 결과가 Git에 포함되지 않는다.

## 검증

`docs/local-development.md`의 앱 생성 후 명령을 사용한다. 의미 있는 서버 동작이 생기면 테스트를 작성한다. 화면과 연결 설정만 있는 단계에서는 제품 단위 테스트 없음이라고 기록한다. 실제 DB 연결 확인과 앱 빌드 결과는 별도로 남긴다.
