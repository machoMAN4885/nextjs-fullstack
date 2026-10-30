# Docker Desktop 로컬 개발

## 처음 실행

Docker Desktop을 켜고 프로젝트 루트인 `nextjs-fullstack/` 또는 그 복사본 디렉터리에서 실행한다. `.env`가 이미 있다면 덮어쓰지 말고 `.env.example`의 필요한 이름만 반영한다.

```bash
cp .env.example .env
docker compose up -d --build
docker compose exec tools node --version
docker compose exec tools npm ci
docker compose exec tools npm run db:validate
docker compose exec db psql -U sdd -d sdd -c 'SELECT 1;'
```

`tools`는 명령을 실행할 Node.js 24 작업 컨테이너다. 앱 소스가 없어도 유지된다. `db`는 PostgreSQL이며 건강 상태 확인을 통과한 뒤 작업 컨테이너가 시작된다. [Compose 시작 순서 기준](https://docs.docker.com/compose/how-tos/startup-order/).

호스트 편집기로 파일을 고치면 `/workspace`에 반영된다. `tools`의 기본 작업 위치는 `/workspace/apps/web`이다. `node_modules`와 `.next`는 Linux 전용 Docker 볼륨에 있으므로 호스트 설치물과 섞이지 않는다. 호스트 편집기에서 모듈 타입을 찾지 못하면 컨테이너에 연결해 작업하거나 호스트에서도 Node.js 24를 사용해 `apps/web/`에서 `npm ci`를 실행한다. 실제 검증 기준은 컨테이너다.

## 두 환경 파일의 역할

- 루트 `.env`: Compose 프로젝트 이름과 호스트 포트만 설정한다.
- `apps/web/.env`: 호스트에서 앱을 직접 실행할 때 사용하는 DB 연결·앱 변수다. Docker만 사용하면 필수 파일이 아니다.

호스트 Node.js 24에서 실행하려면 먼저 루트에서 `docker compose up -d db`를 실행한 뒤 아래 순서로 앱 환경을 준비한다. 기존 `.env`는 덮어쓰지 않는다.

```bash
cd apps/web
cp .env.example .env
npm ci
npm run db:validate
```

이후 호스트 npm 명령은 `apps/web/`에서 실행한다. Docker Compose 명령을 실행하려면 프로젝트 루트로 돌아간다.

## 주소

| 용도 | 호스트 | 컨테이너 내부 |
| --- | --- | --- |
| 앱 생성 후 개발 서버 | `http://localhost:3000` | `0.0.0.0:3000` |
| PostgreSQL | `localhost:55432` | `db:5432` |
| Prisma Studio 실행 후 | `http://localhost:5555` | `5555` |

DB 이름과 사용자는 `sdd`, 비밀번호는 로컬 전용 예시값이다. 포트는 호스트의 `127.0.0.1`에만 공개한다. `.env`에서 `APP_PORT`, `POSTGRES_PORT`, `STUDIO_PORT`를 변경할 수 있다. 호스트에서 Prisma를 실행한다면 `apps/web/.env`의 `DATABASE_URL`과 `DIRECT_URL` 포트도 함께 바꾼다. 컨테이너 내부 DB 포트는 항상 5432다.

여러 복사본을 동시에 실행하려면 `.env`의 `COMPOSE_PROJECT_NAME`과 세 호스트 포트를 각각 다르게 지정한다. 기존 DB 볼륨을 사용 중이면 프로젝트 이름 변경으로 새 볼륨이 선택된다는 점도 확인한다.

## 앱 생성 후

먼저 `context/features/000-project-bootstrap.md`를 구현한다. 현재 템플릿에는 `apps/web/src/app/`가 없으므로 다음 명령으로 웹 앱이 바로 실행되지는 않는다.

```bash
# 이 터미널은 개발 서버를 실행한 채 유지한다.
docker compose exec tools npm run dev
```

별도 터미널에서 검증한다.

```bash
docker compose exec tools npm run lint
docker compose exec tools npm run typecheck
docker compose exec tools npm run build
```

테스트를 작성한 뒤에는 `docker compose exec tools npm run test`를 실행한다. 테스트가 없는 현재 상태에서는 Vitest의 실패 종료가 정상이며 통과로 기록하지 않는다. 개발 서버와 빌드가 같은 `.next`를 사용하므로 빌드할 때는 개발 서버를 `Ctrl+C`로 멈추고 완료 후 다시 시작한다.

## 모델을 추가한 뒤

```bash
docker compose exec tools npm run db:migrate -- --name init
docker compose exec tools npm run db:generate
docker compose exec tools npm run db:status
docker compose exec tools npm run db:studio
```

마이그레이션 파일은 소스와 함께 보관한다. 현재 빈 스키마에서는 마이그레이션이 필요 없다. 잠금 파일의 Prisma 7은 `npm run db:generate`의 기본 명령으로 모델 없는 Client를 생성한다. 이 명령은 `apps/web/src/generated/prisma/`에 생성 파일을 만들지만 제품 코드는 생성하지 않는다. 설치된 버전의 `prisma generate --help`를 기준으로 하며, 이 버전에서 지원하지 않는 `--allow-no-models` 옵션은 추가하지 않는다.

Prisma 7의 연결 설정은 `apps/web/prisma.config.ts`에서 관리한다. [Prisma 7 기준](https://www.prisma.io/docs/guides/upgrade-prisma-orm/v7).

## 의존성과 재시작

잠금 파일이 바뀌면 `docker compose exec tools npm ci`를 실행한다. 패키지를 추가할 때도 컨테이너에서 `npm install`을 실행해 `apps/web/package.json`과 `apps/web/package-lock.json`을 함께 갱신한다. 메이저 버전은 프로젝트에서 확정한 범위를 유지한다.

```bash
docker compose ps
docker compose logs --tail 100 db
docker compose stop
docker compose start
docker compose down
```

`down`은 컨테이너와 네트워크를 제거하고 데이터 볼륨을 보존한다. `down --volumes`는 DB 데이터까지 지우므로 초기화가 필요하고 데이터를 버려도 되는 경우에만 사용한다.

## 문제 확인

- 연결 실패: Docker Desktop 실행 여부와 `docker compose ps`, DB 로그를 확인한다.
- 포트 충돌: `.env`에서 사용하지 않는 포트로 바꾸고 `docker compose up -d`를 다시 실행한다.
- 호스트 `localhost`를 컨테이너 DB 주소로 사용하지 않는다. 컨테이너 안의 주소는 `db`다.
- 새 환경 변수: 루트 `.env`는 Compose 치환용이며 앱에 자동 전달되지 않는다. `apps/web/.env`는 앱/Prisma가 읽는 파일이며 환경 변경 후 해당 프로세스를 재시작한다. 필요한 앱 변수를 `compose.yaml`의 `tools.environment`에 추가하고 컨테이너를 재생성한다. 로컬 DB URL 고정값은 유지한다.
- 컨테이너 메모리 부족: Docker Desktop의 자원 설정과 현재 실행 중인 다른 컨테이너를 확인한다.
