# GitShelf Android

GitHub Markdown·코드 뷰어와 편집기.

[GitShelf 0.2.0 APK 다운로드](https://github.com/20wiz/gitshelf-android/releases/download/v0.2.0/GitShelf-0.2.0-debug.apk) · [릴리스](https://github.com/20wiz/gitshelf-android/releases/tag/v0.2.0)

Android 8 이상 · 디버그 서명 테스트 버전. 이전 0.1.0과 같은 서명으로 업데이트 가능.

0.2.0: 문서 태그·경로는 스크롤 시 숨김, 3점 메뉴는 상단 우측. GitHub 브라우저 승인 로그인 후 내 저장소 자동 목록·검색·선택. 내 소유/참여 저장소 필터·100개씩 추가 로딩.

**현재 APK는 기본 OAuth Client ID 미설정. 최초 로그인에서 OAuth 앱 등록·Client ID 입력 필요.** [로그인 최초 설정](GITHUB_LOGIN.md). PAT 연결도 지원.

검증: core 15개·Android 기기 6개 테스트 통과, Lint 오류 0. 로그인 승인·계정 확인·저장소 선택 흐름은 전용 TLS 테스트 서버 검증. 실제 계정 OAuth 승인은 앱 등록·사용자 승인 필요.

릴리스에 APK, SHA256SUMS.txt, 재빌드 소스, Sora Editor 0.23.6 원본 소스 제공. 라이선스와 재빌드 절차는 소스 ZIP의 THIRD_PARTY.md·REBUILDING.txt 참고.
