# 템플릿 검증 기록

검증일: 2026-10-30

## 여러 문서·폴더 입력 확장 후 확인

- 스킬 5개의 형식 검증, UI 메타데이터, Codex 상대 심볼릭 링크 확인: 통과.
- 템플릿 내부 문서 링크 31개와 코드 블록 확인: 통과. 사용 가이드에는 외부 경로나 외부 링크가 없음.
- 입력 자료 목록·읽기 상태·유저 플로우 대응표와 폴더 사용 예시를 추가함. PRD가 없는 입력도 지침에 포함함.
- 실제 제품 자료와 Figma 링크는 제공되지 않았으므로 다중 문서 분석·MCP 조회의 실제 실행은 미검증. 제품 스펙과 앱 소스는 생성하지 않음.

## PRD 분해 스킬 추가 후 확인

- `prd-to-specs`를 포함한 스킬 5개의 `quick_validate.py` 검증 통과.
- 한국어 UI 메타데이터와 Codex 상대 심볼릭 링크 확인.
- 스킬 참조 문서·사용 가이드 등 프로젝트 내부 상대 링크와 코드 블록 확인.
- 실제 PRD가 없는 상태에서 분해 결과나 제품 소스를 임의로 생성하지 않았음.

이번 확인은 스킬 파일 형식과 문서 연결에 대한 검사다. 실제 PRD·Figma 파일은 제공되지 않아 MCP 파일 접근이나 실제 스펙 분해 실행을 검증한 것은 아니다.

## 폴더 구조 변경 후 재검증

`nextjs-fullstack/apps/web/` 배치로 바꾼 뒤 별도 Compose 프로젝트 `sdd-layout-check`를 기동해 다음을 다시 확인했다.

- Docker 이미지 빌드와 PostgreSQL 건강 상태: 통과.
- `tools`의 실제 작업 위치 `/workspace/apps/web`: 확인.
- 앱 패키지·Prisma 스키마와 상위 스펙·스킬 링크의 컨테이너 접근: 확인.
- `npm pkg get`이 `apps/web/package.json`을 읽는 것과 패키지·잠금 파일 일치: 확인.
- 로컬 DB 주소가 `db`이고 PostgreSQL `SELECT 1`이 성공함: 확인.
- 문서 링크, 스킬 4개, 상대 심볼릭 링크, 디렉터리 경계: 검증 통과.

이번 변경은 파일 배치·실행 경로·안내 문서 변경이다. 아래의 앱 빌드·Prisma 연결 검증은 초기 구성에서 수행한 기록이며, 이번에는 위의 경로·환경 검증을 수행했다. 개발 소스는 새로 추가하지 않았다.

## 환경

Docker Desktop의 Linux ARM64 환경에서 별도 Compose 프로젝트 `sdd-template-check`를 사용했다. 사용자 개발용 기본 포트와 충돌하지 않도록 검증 때만 앱 33071, PostgreSQL 55471, Studio 55571 포트를 사용했다.

| 항목 | 확인한 버전 |
| --- | --- |
| Node.js | 24.22.2 |
| npm | 11.19.1 |
| PostgreSQL | 17.10 |
| Next.js | 16.4.3 |
| TypeScript | 5.9.3 |
| Prisma / Client / PostgreSQL 어댑터 | 7.10.1 |
| Tailwind CSS | 4.3.3 |
| React / React DOM | 19.3.1 |
| Vitest | 4.1.12 |

실제 설치 버전은 `package-lock.json`을 기준으로 재현한다. Docker 이미지 태그는 메이저 버전을 유지하면서 패치가 갱신될 수 있다.

## 실제 확인한 결과

| 검증 | 결과 |
| --- | --- |
| Docker Compose 설정 해석 | 통과 |
| Node.js 24 개발 이미지 빌드 | 통과 |
| PostgreSQL 건강 상태와 SQL 실행 | 통과 |
| 잠금 파일 생성과 `npm ci` 설치 | 통과 |
| `prisma validate` | 빈 PostgreSQL 스키마 설정 유효 |
| 한국어 스킬 4개 검증 | `quick_validate.py` 모두 통과 |
| Codex 스킬 상대 심볼릭 링크 | 원본 4개 모두 정상 연결 |
| 문서 링크·코드 블록·패키지 잠금 일치 | 통과 |
| 제품 소스·기존 마이그레이션·실제 `.env` 미포함 | 확인 |

## 임시 앱으로 확인한 호환성

템플릿의 설정과 의존성을 컨테이너 안의 임시 디렉터리에 복사하고, 검증용 최소 페이지·CSS·Route Handler만 만들어 실행했다. **이 소스는 전달하는 템플릿에 포함하지 않는다.**

- 빈 스키마에서 Prisma Client 생성: 통과. Prisma 7.10.1은 기본 `prisma generate`를 사용한다.
- `npm run lint`: 오류·경고 없이 통과.
- `npm run typecheck`: Next.js 라우트 타입 생성과 TypeScript 검사 통과.
- `npm run build`: 최적화 빌드와 정적 페이지 생성 통과.
- `npm run start`: 실행 성공, 시작 페이지 HTTP 200 확인.
- 페이지가 참조하는 CSS에서 Tailwind의 `p-4`, `text-xl` 생성 확인.
- Route Handler에서 `PrismaPg`와 생성 Client로 로컬 PostgreSQL에 `SELECT 1` 실행, HTTP 200과 `{"ok":1}` 응답 확인.

이 결과는 **설정 조합의 호환성 검증**이다. 아직 없는 제품의 기능 테스트나 브라우저 UI 검증으로 간주하지 않는다. Vercel 실제 배포·Neon 실제 연결은 프로젝트와 환경 변수를 설정한 뒤 별도로 확인해야 한다.

## 설치 안내 메시지

검증 환경의 npm 11은 일부 의존성 설치 스크립트의 `allowScripts` 선언에 대한 안내를 출력했으며 설치와 실행은 통과했다. npm 메이저 버전을 바꿀 때는 설치 스크립트 처리 정책도 확인한다. ESLint는 Next.js의 React·접근성 플러그인 peer dependency와 맞는 9 계열을 사용한다. 설치 시 지원 종료 안내가 있었으므로 해당 플러그인이 새 메이저를 지원하면 함께 갱신한다.

## 정리

검증용 컨테이너·네트워크·볼륨은 검증 후 제거했다. 임시 앱은 컨테이너 내부에만 있었으며 제품 소스로 남기지 않았다. 사용자용 환경은 README의 명령으로 새로 시작한다.
