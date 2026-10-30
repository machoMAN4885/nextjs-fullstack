# 애플리케이션 구조

## 저장소 안의 위치

템플릿 모음 안에서는 `nextjs-fullstack/`이 독립 프로젝트 루트다. 개발 영역은 `apps/web/`, 스펙·진행 기록은 `context/`, 운영·구조 문서는 `docs/`로 나눈다. 앱 내부 상대 경로와 `@/` 별칭은 `apps/web/`의 설정을 기준으로 한다. 자세한 파일 배치는 [사용 가이드](../사용가이드.md)를 따른다.

## 하나의 Next.js 풀스택 앱

브라우저가 Next.js 앱에 접근하고, 서버 측 코드가 Prisma를 통해 PostgreSQL에 접근한다. React는 Next.js 내부의 UI 구성에 사용하며 별도 프런트엔드 서비스로 분리하지 않는다. NestJS 서버는 없다.

```text
브라우저
  → Next.js 16 (Vercel / Node.js 24)
      ├─ Server Component: 페이지와 조회
      ├─ Client Component: 브라우저 상호작용
      ├─ Server Action: 앱 내부 변경
      └─ Route Handler: 외부 HTTP 계약
           → 서버 전용 데이터 계층
               → Prisma 7 + @prisma/adapter-pg
                   → PostgreSQL (운영: Neon / 로컬: Docker)
```

## 연결과 경계

- 브라우저와 Client Component에는 DB 연결 정보가 전달되지 않는다.
- 앱의 Prisma Client는 `DATABASE_URL`을 사용한다. Neon에서는 풀링 주소를 사용한다.
- Prisma CLI는 `apps/web/prisma.config.ts`의 `DIRECT_URL`을 사용한다. Neon에서는 동일한 DB·브랜치의 직접 연결 주소를 사용한다.
- 로컬 컨테이너에서는 두 URL 모두 `db:5432`를 사용한다. 호스트에서 접근하면 `localhost:55432`를 사용한다.
- 로컬과 운영 모두 TCP 연결용 `@prisma/adapter-pg`를 사용한다. 이 템플릿은 Edge 런타임 전용 구조를 전제하지 않는다.
- DB 모델·권한·인증·외부 서비스는 제품 스펙에서 추가한다.

## 환경 구분

| 환경 | 앱 | DB | 스키마 변경 |
| --- | --- | --- | --- |
| 로컬 | Docker Node.js 24 | Docker PostgreSQL 17 | `migrate dev` |
| 미리보기 | Vercel Preview | 별도 Neon 개발/미리보기 브랜치 | `migrate deploy` |
| 운영 | Vercel Production | Neon 운영 브랜치 | 검증된 `migrate deploy` |

로컬 PostgreSQL 메이저 버전은 운영 Neon DB와 맞춰 유지한다. 기존 로컬 데이터 볼륨이 있다면 이미지의 메이저 숫자만 바꾸지 말고 별도 업그레이드 절차를 사용한다.

관련 기준: [Next.js 설치](https://nextjs.org/docs/app/getting-started/installation), [Prisma 7 전환 안내](https://www.prisma.io/docs/guides/upgrade-prisma-orm/v7), [Neon Prisma 연결](https://neon.com/docs/guides/prisma).
