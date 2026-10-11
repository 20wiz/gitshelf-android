# GitShelf APK 게시 가이드

현재 배포: 0.3.0 · 공개 저장소: https://github.com/20wiz/gitshelf-android · Android 8 이상. Git Bash 기준. APK와 재빌드 소스, Sora 원본 소스, SHA256SUMS를 같은 릴리스에 게시.

## 로그인 기본 설정

OAuth 앱 등록은 앱 배포자가 한 번 수행. 사용자는 설치 후 GitHub 코드 승인만 필요. 기본 공개 식별자는 config/github-oauth.properties의 client_id에 보관. 0.3.0 기본값: Ov23liQ3ntooNbf2T6OH. Client Secret이나 개인 토큰은 이 파일에 저장 금지.

설정 우선순위: Gradle 속성 GITHUB_OAUTH_CLIENT_ID → 같은 이름의 환경변수 → config/github-oauth.properties. 다음 빌드에도 유지하려면 파일에 저장. 이전 APK에 임의 ID를 입력했던 기기는 계정 설정의 ‘앱 배포자용 로그인 설정’에서 등록된 ID로 변경.

[로그인 사용·앱 등록](GITHUB_LOGIN.md). 등록 앱의 Enable Device Flow 활성화 필요. 게시 스크립트가 GitHub 코드 발급을 직접 확인. 사용자 승인·토큰 수신·내 저장소 조회는 휴대폰에서 마지막 확인.

## 버전과 서명

app/build.gradle.kts의 versionName과 versionCode를 모두 증가. 이미 공개된 APK는 교체하지 않고 새 버전으로 게시. 현재 versionName=0.3.0, versionCode=3.

기존 설치본 업데이트는 동일 서명 필요. 배포에 사용 중인 로컬 debug.keystore 유지. 서명 SHA-256:

~~~text
9603d89f1be0fb2651b7d90dec5e60f085164efdfc7805fc8b587c74d3605972
~~~

패키징 도구가 이 서명을 확인. 키 파일은 소스 묶음에 포함하지 않음. 서명 변경이 필요하면 업데이트 호환성과 초안 보존을 먼저 검토.

## 빌드·검증

프로젝트 루트에서 실행. JDK 17 이상, Android SDK 35, Python 3.9 이상, Node 20 이상 필요. local.properties의 sdk.dir 지정. Android 테스트용 에뮬레이터를 시작한 뒤 연결 번호 확인.

~~~sh
adb devices
ANDROID_SERIAL=emulator-5554 ./gradlew --no-daemon :core:test :app:assembleDebug :app:lintDebug :app:connectedDebugAndroidTest -Pandroid.injected.device.serial=emulator-5554
node --test scripts/release-lib.test.mjs
~~~

emulator-5554는 테스트용 기기 번호로 교체. core 15개 이상, Android 7개 이상 통과·누락/실패/건너뛰기 없음·Lint 오류 0개가 게시 조건. 소스 변경 후에는 빌드와 해당 검증 다시 실행.

로그인 코드 표시·취소, 내 저장소 선택, Markdown 목차, 코드 편집·키보드, 스크롤 태그 숨김·상단 메뉴를 화면에서 확인. 실제 GitHub 승인과 Commit은 자동 TLS 테스트와 구분해 릴리스 노트에 기록.

## 릴리스 자료 생성

새 버전의 docs/releases/<버전>.md 작성. 주요 변경, 검증 결과와 아직 실제 계정에서 확인하지 않은 항목 명시. docs/GITHUB_LOGIN.md와 docs/DESIGN_REVIEW.md도 변경 내용에 맞춰 수정.

~~~sh
python scripts/package-release.py
~~~

출력: dist/releases/<버전>/manifest.json과 APK·GitShelf 재빌드 소스 ZIP·Sora 0.23.6 원본 ZIP·SHA256SUMS.txt. 실제 BuildConfig의 Client ID, APK 버전·서명, 검증 결과 최신성 확인. 개인정보·로컬 설정·키·캐시를 제외한 허용 파일만 소스 묶음에 수록. Sora 원본은 b1777d0d465b42515739ffe529b4ec1634ac2175 고정.

Client ID 누락은 기본 차단. 의도적인 설정/PAT 전용 테스트 배포에만 package-release.py와 publish-release.mjs 양쪽에 --allow-unconfigured-oauth 지정하고 노트에 ‘Client ID 미설정’ 표기. 정상 로그인 배포에는 이 옵션 사용 불필요.

## GitHub 게시

Git Credential Manager 또는 gh auth login으로 20wiz/gitshelf-android 쓰기 권한 계정 연결. 기존 인증 재사용. 토큰을 명령줄·문서에 붙여넣을 필요 없음.

현재 0.3.0 예시. 다음 게시에는 경로의 버전을 변경.

~~~sh
node scripts/publish-release.mjs --manifest dist/releases/0.3.0/manifest.json --notes docs/releases/0.3.0.md
~~~

동작: 자료 해시 확인 → Device Flow 등록 확인 → Draft 릴리스 생성 → 4개 자료 업로드·서버 해시 확인 → 공개 → 태그로 릴리스 재조회 → 인증 없이 APK 다운로드·SHA-256 확인 → 문서·최신 링크 갱신.

Draft 업로드 응답의 임시 다운로드 URL은 배포 링크로 사용하지 않음. 공개 후 태그 조회의 정식 releases/download/v<버전>/ 경로만 기록. 게시 중 중단되면 동일 명령 재실행: 일치하는 Draft 자료 재사용. 공개 릴리스도 해시가 같으면 검증·문서 갱신 가능. 다른 해시 자료는 덮어쓰지 않으며 새 버전 필요.

## 완료 기록

로컬 기록: dist/releases/<버전>/publication.json, dist/publication-v<버전>.json. 원격 기록: LATEST_RELEASE.json. APK 크기·SHA-256·서명·검증 수·정식 링크·익명 다운로드 확인 저장.

원격 README.md, GITHUB_LOGIN.md, PUBLISH_GUIDE.md, DESIGN_REVIEW.md 자동 갱신. 게시 완료 뒤 공개 릴리스의 APK 링크와 문서 링크 확인.

0.3.0: 기본 OAuth Client ID 내장, 실제 GitHub 코드 발급 확인, core 15개·Android 7개 통과, Lint 오류 0개. 실제 사용자 브라우저 승인과 계정의 원격 Commit은 휴대폰 확인 항목.

근거: [GitHub Device Flow](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps#device-flow), [릴리스 자료 API](https://docs.github.com/en/rest/releases/assets?apiVersion=2022-11-28).
