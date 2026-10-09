# 🥐 빵생빵사 (Crumb) — Frontend

> 내 주변 빵집을 지도로 찾고, 취향에 맞는 숨은 빵집을 추천받는 모바일 앱  
> 2025 SEASONTHON TEAM 43 · React Native(Expo) 프론트엔드 저장소

<p align="center">
  <img src="./assets/images/logo.png" width="280" alt="빵생빵사 로고" />
</p>

## 📱 주요 기능

| 화면 | 설명 |
| --- | --- |
| **카카오 로그인** | Kakao SDK로 로그인한 뒤, 발급받은 토큰을 백엔드(`/api/kakao/login-bread`)와 교환합니다. |
| **온보딩** | 이용약관 동의 → 닉네임 입력 → 내 지역 찾기(다음 우편번호 검색 + Kakao 주소→좌표 변환) |
| **빵지도** | Kakao 지도 위에 주변 빵집을 마커로 표시하고, 빵집 검색과 바텀시트 상세 정보(카카오맵 웹뷰)를 제공합니다. |
| **빵발견** | 현재 위치를 기반으로 FastAPI 추천 서버에서 취향에 맞는 빵집을 추천받습니다. |
| **프로필** | 내 정보 확인 및 로그아웃 |

## 🛠 기술 스택

- **Framework**: Expo SDK 53, React Native 0.79 (New Architecture), React 19
- **Routing**: expo-router (file-based, typed routes)
- **Styling**: NativeWind 4 (Tailwind CSS)
- **Network**: axios
- **Map / Auth**: Kakao Maps JS SDK (WebView), `@react-native-kakao/core`, `@react-native-kakao/user`
- **Storage**: expo-secure-store, AsyncStorage
- **UI**: @gorhom/bottom-sheet, react-native-reanimated, react-native-svg
- **Build**: EAS Build

## 🔗 연동 서버

| 서버 | 용도 | 환경변수 |
| --- | --- | --- |
| Spring 백엔드 | 로그인/로그아웃, 빵집 목록(`/api/bakery/list`), 검색(`/api/bakery/search`) | `EXPO_PUBLIC_API_BASE_URL` |
| FastAPI 추천 서버 | 빵집 추천(`/recommend?lat=&lng=`) | `EXPO_PUBLIC_FASTAPI_BASE_URL` |
| Kakao API | 지도, 주소→좌표 변환, 로그인 | `EXPO_PUBLIC_KAKAO_*` |

## 📂 폴더 구조

```
app/                    # expo-router 화면
├─ (auth)/login.tsx         # 카카오 로그인
├─ (onboarding)/            # 약관 동의 · 추가 정보 · 지역 검색
├─ (tabs)/                  # map(빵지도) · course(빵발견) · diary(프로필)
├─ detail_webview_[number].tsx
└─ _layout.tsx              # 토큰/온보딩 여부에 따른 초기 라우팅
components/             # 공통 · 화면별 컴포넌트 (KakaoMap, BottomSheet 등)
context/AuthContext.tsx # 로그인 상태 관리
remote/                 # axios 인스턴스, API 요청/응답 타입
utils/geocodeAddress.ts # Kakao 주소 → 좌표 변환
app.config.ts           # Expo 설정 (Kakao 네이티브 앱 키, 패키지명 등)
eas.json                # EAS 빌드 프로필
```

---

## 🚀 실행 방법 (Windows + Android)

> ⚠️ 이 앱은 Kakao 네이티브 SDK를 사용하므로 **Expo Go 앱으로는 실행되지 않습니다.**  
> 반드시 `npx expo run:android`로 만든 **개발 빌드(Dev Client)** 또는 EAS로 빌드한 APK로 실행해야 합니다.

### 1. 사전 준비

| 항목 | 버전 / 비고 |
| --- | --- |
| Node.js | 20 LTS 이상 (22 권장) |
| JDK | **17** (Android Studio 내장 JBR 사용 가능) |
| Android Studio | SDK Platform 35/36, Build-Tools, Platform-Tools, Emulator, NDK, CMake |

**Windows 환경변수 설정** (시스템 속성 → 환경 변수)

```
JAVA_HOME     = C:\Program Files\Android\Android Studio\jbr   (또는 설치한 JDK 17 경로)
ANDROID_HOME  = C:\Users\<사용자>\AppData\Local\Android\Sdk
Path 추가     = %ANDROID_HOME%\platform-tools
               %ANDROID_HOME%\emulator
```

설정 후 새 터미널에서 확인합니다.

```bash
java -version   # 17.x
adb --version
```

### 2. Android 에뮬레이터(AVD) 만들기

1. Android Studio → **More Actions → Virtual Device Manager**
2. **Create Virtual Device** → `Pixel 8` 등 선택
3. System Image: **API 34 이상, `Google Play` 포함 x86_64 이미지** 선택  
   (카카오 로그인 시 카카오톡/웹 로그인을 위해 Play Store 이미지 권장)
4. 생성 후 ▶ 버튼으로 실행 → `adb devices`에 `emulator-5554`가 보이면 준비 완료
5. 에뮬레이터 위치 설정: 에뮬레이터 우측 `⋯` → **Location** 에서 좌표 지정 후 *Set Location*  
   (빵지도/빵발견은 현재 위치를 사용합니다.)

### 3. 프로젝트 설치 및 환경변수

```bash
git clone https://github.com/9oormthon-univ/2025_SEASONTHON_TEAM_43_FE.git
cd 2025_SEASONTHON_TEAM_43_FE
npm install
cp .env.example .env   # 값 채우기
```

`.env` 항목

| 변수 | 설명 |
| --- | --- |
| `EXPO_PUBLIC_API_BASE_URL` | Spring 백엔드 주소 (끝에 `/` 없이) |
| `EXPO_PUBLIC_FASTAPI_BASE_URL` | FastAPI `/recommend` 전체 URL (`lat`, `lng`는 앱이 쿼리로 추가) |
| `EXPO_PUBLIC_KAKAO_MAP_JS_KEY` | Kakao JavaScript 키 (온보딩 지도) |
| `EXPO_PUBLIC_KAKAO_REST_API_KEY` | Kakao REST API 키 (주소 → 좌표) |
| `EXPO_PUBLIC_NATIVE_APP_KEY` | Kakao 네이티브 앱 키 |

백엔드 주소는 실행 환경에 따라 다릅니다.

| 백엔드 위치 | 에뮬레이터 | 실기기 |
| --- | --- | --- |
| 배포 서버(HTTPS) | `https://<host>` | `https://<host>` |
| 내 PC에서 실행 | `http://10.0.2.2:8080` | `http://<PC 내부 IP>:8080` (같은 Wi-Fi, 방화벽 8080 허용) |

> `EXPO_PUBLIC_*` 값은 번들에 포함되므로, 변경 후에는 `npx expo start -c`로 캐시를 지우고 다시 실행하세요.

> **코드에 직접 들어있는 Kakao 키** — 아래 값은 `.env`가 아니라 코드에 하드코딩되어 있으므로, 다른 Kakao 앱을 쓰려면 함께 수정해야 합니다.
> - `app.config.ts` → `plugins[@react-native-kakao/core].nativeAppKey`, `extra.kakaoMapJsKey` (빵지도 탭)
> - `app/(auth)/login.tsx` → `initializeKakaoSDK("...")`

### 4. 실행

```bash
# 에뮬레이터를 먼저 켠 뒤
npx expo run:android          # 최초 1회: android/ 폴더 생성 + 네이티브 빌드 + 설치 (수 분 소요)

# 이후 JS만 수정했다면 Metro만 다시 띄우면 됩니다
npx expo start --dev-client
```

### 5. 실기기(안드로이드 폰)에서 실행

**방법 A — USB 디버깅 (개발용)**

1. 휴대폰 **설정 → 휴대전화 정보 → 소프트웨어 정보 → 빌드번호 7회 탭** → 개발자 옵션 활성화
2. 개발자 옵션 → **USB 디버깅** 켜기 → PC와 USB 연결 후 디버깅 허용
3. `adb devices`로 기기 확인 후
   ```bash
   npx expo run:android --device
   ```
4. 이후에는 PC와 폰이 **같은 Wi-Fi**에 있으면 USB 없이 Dev Client 앱에서 Metro 서버에 접속할 수 있습니다.

**방법 B — APK 설치 (테스트 배포용)**

```bash
npm install -g eas-cli
eas login
eas build -p android --profile preview   # 완료 후 나오는 링크/QR로 APK 설치
```

> `preview` 프로필은 `.env`가 아니라 `eas.json`의 `env` 값을 사용합니다. 백엔드 주소가 바뀌면 `eas.json`도 수정하세요.  
> 릴리스 APK는 기본적으로 `http://` 통신이 차단되므로 백엔드는 **HTTPS**로 배포되어 있어야 합니다.

### 6. 카카오 로그인 설정 (Kakao Developers)

카카오 로그인이 동작하려면 [Kakao Developers](https://developers.kakao.com) 콘솔의 **내 애플리케이션 → 앱 설정 → 플랫폼 → Android**에 아래 정보가 등록되어 있어야 합니다.

- **패키지명**: `com.seoyeon241.crumb`
- **키 해시**: 빌드에 사용한 서명 키마다 다릅니다 (디버그 키 / EAS 키 각각 등록)
  - 앱에서 로그인 버튼을 누르면 로그에 `[KAKAO KEY HASH] ...`가 출력되니 그 값을 등록하면 됩니다.
  - 또는 직접 계산:
    ```bash
    keytool -exportcert -alias androiddebugkey -keystore android/app/debug.keystore -storepass android -keypass android | openssl sha1 -binary | openssl base64
    ```
- **카카오 로그인 활성화** 및 **Redirect URI / 동의항목** 설정 확인
- 지도가 하얗게 나온다면 **플랫폼 → Web**에 사이트 도메인이 등록되어 있는지, JavaScript 키가 올바른지 확인하세요.

### 🧯 트러블슈팅

| 증상 | 해결 |
| --- | --- |
| `Filename longer than 260 characters` / CMake 경로 오류 | 프로젝트를 `C:\dev\crumb`처럼 짧은 경로로 옮기거나, 관리자 PowerShell에서 `New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" -Name LongPathsEnabled -Value 1 -PropertyType DWORD -Force` 실행 |
| `SDK location not found` | `ANDROID_HOME` 확인, 또는 `android/local.properties`에 `sdk.dir=C\:\\Users\\<사용자>\\AppData\\Local\\Android\\Sdk` 추가 |
| 카카오 로그인 `invalid android key hash` | 6번의 키 해시 등록 확인 |
| 로그인은 되는데 `로그인 실패(서버)` | `EXPO_PUBLIC_API_BASE_URL`, 백엔드 실행 여부 확인 (Render 무료 플랜은 첫 요청 시 콜드스타트로 수십 초 걸릴 수 있음) |
| 네이티브 설정(app.config.ts) 변경이 반영 안 됨 | `npx expo prebuild --clean` 후 `npx expo run:android` |

---

## Git Convention & Branch Strategy

### 📌 Commit Message Convention
커밋 메시지는 아래 규칙을 따릅니다. 모든 메시지는 **`~합니다.`** 로 끝맺습니다.  

- **feat**: 새로운 기능을 개발합니다.  
  - 예시: `feat: 지도 기반 검색 기능을 추가합니다.`  
- **fix**: 버그를 수정합니다.  
  - 예시: `fix: 상세 페이지 에러를 수정합니다.`  
- **style**: CSS 스타일을 수정합니다.  
- **format**: 코드 줄 간격, 줄 위치, 따옴표 등 코드 스타일을 수정합니다. (동작에는 영향 없음)  
- **docs**: 문서를 작성하거나 수정합니다.  
- **refactor**: 리팩토링합니다. (기능 변경 없음, 예: 변수명 변경, 코드 가독성 개선 등)  
- **lib**: 라이브러리를 업데이트하거나 추가합니다.  
- **config**: 환경 변수, TS config 등 설정을 변경합니다.  

---

### 📌 Branch Strategy
브랜치는 아래와 같이 관리합니다.  

- **main**: 배포용 브랜치  
- **develop**: 개발 통합 브랜치 (기본 작업 시작점)  
- **feature/[branch-name]**: 새로운 기능 개발 브랜치  
- **fix/[branch-name]**: 버그 수정 브랜치  

#### 작업 흐름
1. `develop` 브랜치에서 분기합니다.  
2. `feature/[branch-name]` 브랜치에서 작업을 진행합니다.  
3. 작업 완료 후, **base branch를 `develop`으로 PR**을 생성합니다.  

#### 예외 사항
- 간단한 오타 수정 등 사소한 경우에는 `fix` 브랜치를 새로 만들지 않고, 팀 내 합의하에 **feature 브랜치에서 바로 커밋 처리**할 수 있습니다.  
