# Vercel과 Neon 배포

이 문서는 앱과 첫 모델을 구현한 뒤 사용할 절차다. 현재 템플릿은 배포된 앱이 아니며, 외부 프로젝트·DB를 자동 생성하지 않는다.

## 연결 준비

1. Neon에 프로젝트와 PostgreSQL DB를 준비한다. 로컬 PostgreSQL과 메이저 버전을 맞춘다.
2. 운영과 미리보기는 다른 Neon 브랜치 또는 별도 DB를 사용한다. 미리보기 앱이 운영 DB를 변경하지 않게 한다.
3. 각 환경에서 풀링 연결 문자열과 직접 연결 문자열을 얻는다. 두 문자열의 브랜치·DB·사용자가 같은 대상인지 확인한다.
4. Neon에서 제공한 TLS 옵션을 유지한다. 연결 실패를 해결하기 위해 인증서 검증을 끄지 않는다.

| 환경 변수 | 사용 위치 | 값 |
| --- | --- | --- |
| `DATABASE_URL` | 앱의 Prisma 어댑터 | 해당 환경의 Neon 풀링 주소 |
| `DIRECT_URL` | Prisma CLI | 같은 환경의 Neon 직접 연결 주소 |

Vercel의 Production과 Preview 환경에 값을 각각 설정한다. 로컬 Compose용 변수는 Vercel에 필요하지 않다. 실제 연결 문자열은 저장소에 넣지 않는다. [Neon 공식 연결 안내](https://neon.com/docs/guides/prisma).

## Vercel 프로젝트 설정

- Framework Preset: Next.js
- Root Directory: 이 저장소를 그대로 연결하면 `스펙주도개발-템플릿/nextjs-fullstack/apps/web`, 템플릿을 독립 저장소로 복사했다면 `apps/web`
- Node.js Version: 24.x (`apps/web/package.json`의 `engines.node`도 24.x)
- Install Command: `npm ci`
- Build Command: `npm run build`
- Output Directory: Next.js 기본값

`apps/web/vercel.json`에 프레임워크와 설치·빌드 명령을 기록했다. Dockerfile은 로컬 작업용이며 Vercel은 Next.js를 직접 빌드한다. [Vercel Node.js 버전 설정](https://vercel.com/docs/functions/runtimes/node-js/node-js-versions).

아래 npm 명령은 앱 루트인 `apps/web/`를 작업 위치로 실행한다. Vercel도 위 Root Directory 안의 패키지·설정·잠금 파일을 사용한다.

## 스키마와 앱 적용 순서

1. 로컬에서 모델을 바꾸고 `npm run db:migrate -- --name <변경명>`으로 마이그레이션을 만든다.
2. `npm run db:generate`, 관련 테스트·린트·타입 검사·빌드를 확인한다.
3. 미리보기 DB에서 `npm run db:deploy`, `npm run db:status`를 실행하고 미리보기 앱을 검증한다.
4. 운영 배포 시 변경 SQL과 데이터 영향을 확인한다. 기존 앱도 동작할 수 있는 스키마 변경부터 적용한다.
5. 운영 환경 변수를 가진 신뢰할 수 있는 Node.js 24 배포 작업에서 `npm ci`, `npm run db:deploy`, `npm run db:status`를 실행한 뒤 앱을 배포한다. 로컬 Compose의 `tools`는 항상 로컬 DB를 가리키므로 운영 적용용으로 사용하지 않는다.
6. 배포 후 주요 사용자 흐름·서버 오류·DB 연결을 확인한다.

일반 빌드 명령에는 마이그레이션을 묶지 않았다. 미리보기 빌드마다 운영 DB를 변경하거나 여러 빌드가 동시에 스키마를 바꾸지 않도록 **DB 적용 → 앱 배포**를 배포 작업에서 명시적으로 연결한다. 첫 배포 이후에도 새 마이그레이션이 있으면 이 단계를 생략하지 않는다.

앱 되돌리기가 DB 스키마를 되돌려 주지는 않는다. 컬럼 삭제·데이터 변환처럼 되돌리기 어려운 변경은 호환 단계와 복구 방법을 해당 기능 스펙에 기록한다.
