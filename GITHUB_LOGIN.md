# GitHub 로그인 설정

GitShelf 0.2.0: 브라우저 코드 승인 → 암호화된 기기 토큰 → 내 저장소 목록 → 저장소 선택.

## 앱 소유자 최초 등록

1. https://github.com/settings/applications/new 에서 OAuth 앱 등록.
2. Application name: GitShelf
3. Homepage URL: https://github.com/20wiz/gitshelf-android
4. Authorization callback URL: https://github.com/20wiz/gitshelf-android
5. 앱 등록 후 Enable Device Flow 활성화.
6. Client ID만 사용. Client Secret·사용자 PAT를 APK에 넣지 않기.

빌드 시 공용 Client ID 지정:

~~~sh
./gradlew :app:assembleDebug -PGITHUB_OAUTH_CLIENT_ID=YOUR_CLIENT_ID
~~~

Client ID는 공개 식별자이며 비밀값 아님. 환경변수 GITHUB_OAUTH_CLIENT_ID도 지원. 기본 ID가 없는 개발 APK는 첫 로그인 시 앱 등록·ID 입력 안내. 이후 기기에 보관.

## 앱 사용

저장소 탭 → GitHub 로그인 → 코드 복사·GitHub 열기 → GitHub에 코드 입력·승인 → GitShelf로 복귀.
로그인 완료 후 저장소 목록 자동 표시. 내 소유 / 참여 저장소 포함 필터, 불러온 저장소 검색, 100개씩 추가 로딩. 저장소 선택 후 폴더·브랜치 탐색.
계정 설정에서 비공개 저장소 포함 여부 선택. 포함 시 repo read:user, 공개만 사용 시 public_repo read:user 범위 요청. 토큰으로 연결도 계속 지원.

## 동작과 검증

로그인 코드는 메모리에만 보관. 새 로그인·취소 시 이전 인증 요청 중단. GitHub 요청 간격, slow_down 증가, 승인 거부·코드 만료·네트워크 실패 처리.
토큰 수신 후 /user로 계정 확인하고 Android Keystore AES-GCM 암호화 저장. 브라우저·WebView·앱 로그에 토큰 전달 없음. 만료 토큰은 재로그인 필요. 앱 등록에서 만료 토큰을 강제하지 않은 기본 구성 권장; refresh token 교환은 현재 미지원.
로그인 성공 시 내 저장소 탭으로 이동. 참여 저장소는 GitHub 승인 범위·조직 정책에 따라 표시.

인증 요청은 GitHub HTTPS 고정 주소, 리다이렉트와 자동 재시도 차단. 테스트에서는 전용 TLS MockWebServer로 승인 → 계정 확인 → 저장소 선택 → 폴더 목록 전체 흐름 검증. 실제 계정 브라우저 승인은 유효한 Client ID·사용자 승인 필요.

[GitHub 공식 Device flow](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps#device-flow)
