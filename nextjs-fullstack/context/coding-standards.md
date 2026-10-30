# 코딩 규칙

이 문서의 파일 경로는 프로젝트 루트 기준이다. 앱 소스와 설정은 `apps/web/`에 모은다. 아래 npm 명령은 그 폴더에서 실행하거나 프로젝트 루트에서 `docker compose exec tools`를 앞에 붙인다.

## TypeScript와 Next.js

- TypeScript 5의 `strict`를 유지한다. `any` 대신 정확한 타입이나 `unknown`과 검증을 사용한다.
- 함수 컴포넌트를 사용하고 Server Component를 기본으로 한다. 상태·이벤트·브라우저 API가 필요한 경계에만 `"use client"`를 둔다.
- 서버 컴포넌트는 서버 전용 데이터 계층을 직접 호출한다. 같은 앱의 Route Handler를 HTTP로 우회 호출하지 않는다.
- 앱 내부 폼과 데이터 변경은 Server Action으로, 웹훅·외부 클라이언트·HTTP 상태/헤더 계약은 Route Handler로 처리한다.
- DB 모듈에는 `server-only` 경계를 두며 비밀값·Prisma Client를 클라이언트로 보내지 않는다.
- 입력 검증과 접근 권한 확인은 각 서버 진입점에서 수행한다. 화면이나 `proxy.ts`의 접근 제어만으로 대체하지 않는다.
- Next.js 16의 비동기 요청 API와 라우트 매개변수는 해당 버전 공식 문서에 맞춰 처리한다.
- 긴 작업은 요청 제한 시간을 확인하고 필요하면 별도 작업 처리 방식을 스펙으로 설계한다.

## Tailwind CSS 4

`apps/web/postcss.config.mjs`에서 `@tailwindcss/postcss`를 사용한다. 초기 앱의 `apps/web/src/app/globals.css`에는 `@import "tailwindcss";`를 두고 테마는 CSS의 `@theme`에서 정의한다. v3 방식의 `tailwind.config.js`를 새로 만들지 않는다. 색상·컴포넌트 라이브러리·다크 모드는 제품 스펙에서 정한다.

## 데이터

- Prisma 7 CLI의 URL은 `apps/web/prisma.config.ts`에서 `DIRECT_URL`로 설정한다. `apps/web/prisma/schema.prisma`의 datasource에 URL을 넣지 않는다.
- 앱은 `DATABASE_URL`과 `@prisma/adapter-pg`로 Prisma Client를 만든다. 이 TCP 어댑터를 로컬 PostgreSQL과 Neon에 공통으로 사용한다.
- `prisma-client` 생성기를 사용하고 결과는 `apps/web/src/generated/prisma`에 생성한다. 생성 파일은 Git에서 제외한다.
- 스키마 변경은 `npm run db:migrate -- --name <변경명>`으로 기록한다. `db push`로 마이그레이션을 생략하지 않는다.
- Prisma 7에서는 마이그레이션 뒤 `npm run db:generate`를 명시적으로 실행한다.
- 배포는 `npm run db:deploy`로 기존 마이그레이션만 적용한다. 운영에서 `migrate dev`, 초기화, 개발 시드를 실행하지 않는다.
- 조회 범위·정렬·페이지네이션, 소유권, 제약조건, 트랜잭션은 기능의 수용 기준에 맞춰 설계한다.

## 파일 배치 — 앱 생성 후

| 경로 | 역할 |
| --- | --- |
| `apps/web/src/app/` | 페이지, 레이아웃, Route Handler |
| `apps/web/src/components/<기능>/` | UI 컴포넌트 |
| `apps/web/src/actions/<기능>.ts` | Server Action |
| `apps/web/src/lib/db/` | 서버 전용 데이터 접근 |
| `apps/web/src/lib/prisma.ts` | Prisma Client 재사용과 연결 |
| `apps/web/src/lib/` | 공통 서버 로직과 유틸리티 |
| `apps/web/src/types/` | 공유 타입 |
| `apps/web/prisma/` | 스키마와 마이그레이션 |

## 오류와 검증

예상 가능한 오류는 일관된 결과 타입으로 반환하고 사용자에게 복구 방법을 안내한다. 원시 DB 오류나 스택·비밀값을 응답에 노출하지 않는다. Next.js의 `redirect` 같은 제어 흐름을 일반 오류로 삼키지 않는다.

Vitest는 권한·입력 검증·데이터 변경·유틸리티의 의미 있는 동작을 검증한다. 테스트는 대상 파일 옆에 `<이름>.test.ts`로 둔다. 외부 API와 DB 대역을 사용하되 실제 연결·마이그레이션 검증은 별도로 기록한다. 단순 CSS 변경에 구현을 따라 쓴 테스트를 추가하지 않는다.

`npm run lint`, `npm run typecheck`, `npm run build`를 구분한다. Next.js 16 빌드는 린트를 대신하지 않는다. UI가 생기면 실제 브라우저로 기본 흐름과 반응형 화면도 확인한다.
