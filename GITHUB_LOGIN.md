# GitHub 로그인

GitShelf 0.3.0 APK는 등록된 OAuth Client ID 기본 포함. 일반 사용자의 OAuth 앱 등록·Client ID 입력 불필요.

## 앱 사용

저장소 탭 → GitHub 로그인 → 코드 복사·GitHub 열기 → GitHub에 코드 입력·승인 → GitShelf 복귀. 로그인 완료 후 내 저장소 목록 자동 표시. 저장소 선택으로 폴더·파일·브랜치 탐색.

내 소유 / 참여 저장소 포함 필터, 불러온 저장소 검색, 100개씩 추가 로딩 지원. 계정 설정의 비공개 저장소 포함 선택 시 repo read:user, 공개만 사용 시 public_repo read:user 범위 요청. 조직 저장소는 승인 범위와 조직 정책에 따라 표시.

코드 만료·승인 거부 시 로그인 다시 시작. 취소 시 이전 요청 중단. 앱을 닫은 뒤에는 새 코드로 시작. 토큰이 만료되거나 권한을 취소한 경우 재로그인 필요. 자동 refresh token 교환은 현재 미지원.

토큰 대안: 계정 설정 → 토큰으로 연결. Fine-grained PAT는 선택한 저장소의 Contents 읽기 권한 필요, Commit은 쓰기 권한 추가.

## 배포자 최초 등록

[GitHub OAuth 앱 등록](https://github.com/settings/applications/new) → Application name: GitShelf → Homepage URL·Authorization callback URL: https://github.com/20wiz/gitshelf-android → Enable Device Flow 활성화.

config/github-oauth.properties에 공개 Client ID 저장:

~~~properties
client_id=Ov23liQ3ntooNbf2T6OH
~~~

Client ID는 공개 식별자. Client Secret·개인 토큰을 APK에 넣지 않음. Gradle 속성 -PGITHUB_OAUTH_CLIENT_ID 또는 같은 이름의 환경변수로 빌드 기본값 변경 가능. 앱 계정 설정의 ‘추가 연결 설정 → 앱 배포자용 로그인 설정’은 개발용 변경 경로. 기기에 저장한 ID는 빌드 기본값보다 우선.

Client ID가 없는 개발 APK는 토큰 연결을 먼저 표시하고 GitHub 로그인 버튼 비활성화. 일반 사용자에게 앱 등록 안내를 강제하지 않는 구성. [다음 APK 게시 절차](PUBLISH_GUIDE.md).

## 동작·검증

코드는 메모리에만 보관. 요청 간격과 slow_down, 승인 거부·코드 만료·네트워크 실패 처리. 토큰 수신 후 /user로 계정 확인, Android Keystore AES-GCM으로 암호화 저장. 토큰을 WebView·앱 로그·프로젝트에 전달하지 않는 구조.

0.3.0: 내장 Client ID로 실제 GitHub 로그인 코드 발급 확인. 승인 → 토큰 수신 → 계정 확인 → 저장소 선택·폴더 탐색은 TLS 테스트 서버에서 검증. 실제 사용자 브라우저 승인과 비공개 저장소 조회는 설치 후 확인 필요. 실제 계정 원격 Commit 미실행.

[GitHub 공식 Device Flow](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps#device-flow).
