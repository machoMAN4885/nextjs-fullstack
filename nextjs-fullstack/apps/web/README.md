# 개발 영역: Next.js 풀스택 앱

이 폴더에 앱 개발 파일을 모은다. 현재는 실행 설정과 빈 DB 스키마만 있으며 제품 소스는 없다.

- `src/`: 이후 구현할 화면, Server Action, Route Handler, 서버 로직, 테스트
- `public/`: 이후 추가할 정적 파일
- `prisma/`: DB 스키마와 이후 마이그레이션
- `package.json`과 설정 파일: 이 앱의 의존성과 실행·빌드 설정
- `.env.example`: 호스트 실행용 환경 변수 예시

AI 도구와 Docker Compose는 두 단계 위 프로젝트 루트에서 사용한다. Docker의 `tools`는 자동으로 이 앱 폴더에서 명령을 실행한다. 호스트에서 직접 `npm`을 사용할 때는 이 폴더에서 실행한다.

[전체 사용 가이드](../../사용가이드.md) · [로컬 개발](../../docs/local-development.md)
