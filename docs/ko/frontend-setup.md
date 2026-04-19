# 프론트엔드 설정 가이드

!!! warning "자체 호스팅 권장하지 않음"
    이 가이드는 Ministry Mapper를 자체 호스팅하려는 고급 사용자를 위한 것입니다. 기술적 복잡성, 유지 관리 부담 및 보안 책임으로 인해 자체 호스팅을 권장하지 않습니다.

    **대부분의 사용자는**: **[ministry-mapper.com](https://ministry-mapper.com)**에서 호스팅 서비스를 사용해야 합니다.

!!! info "사용자 문서를 찾고 계신가요?"
    Ministry Mapper 사용법을 배우려는 일반 사용자라면 대신 [시작하기](getting-started.md) 또는 [사용자 가이드](user-guide.md)를 참조하세요.

## 개요

Ministry Mapper 프론트엔드 (ministry-mapper-v2)는 다음을 제공하는 React 기반 웹 애플리케이션입니다:

- 사용자 인증 및 계정 관리
- 대화형 구역 관리 인터페이스
- 대화형 매핑 기능
- 실시간 데이터 동기화
- 모바일 반응형 디자인
- 다국어 지원 (8개 언어)
- 프로그레시브 웹 앱 기능

**이 가이드는 다음을 갖추고 있다고 가정합니다**:
- Node.js 및 npm 경험
- 환경 변수에 대한 이해
- 정적 사이트 배포에 대한 친숙함
- 웹 애플리케이션 보안에 대한 지식

전체 자체 호스팅 지침은 [자체 호스팅 가이드](self-hosting.md)를 참조하세요.

## 빠른 참조

이 페이지는 프론트엔드 구성에 대한 빠른 참조를 제공합니다. **전체 설정 지침은 [자체 호스팅 가이드](self-hosting.md)를 참조하세요.**

## 빠른 참조

이 페이지는 프론트엔드 구성에 대한 빠른 참조를 제공합니다. **전체 설정 지침은 [자체 호스팅 가이드](self-hosting.md)를 참조하세요.**

### 기술 스택

- **React 19**: 현대적인 UI 라이브러리
- **TypeScript**: 타입 안전 JavaScript
- **Vite 7**: 빠른 빌드 도구 및 개발 서버
- **Bootstrap 5**: 반응형 CSS 프레임워크
- **Leaflet**: 대화형 매핑
- **PocketBase SDK**: 백엔드 연결
- **Sentry**: 오류 추적
- **i18next**: 다국어 지원
- **Vite PWA**: 프로그레시브 웹 앱 기능

---

!!! note "전체 설정 지침"
    아래 섹션은 프론트엔드 구성에 대한 기술 참조를 제공합니다. 배포 옵션 및 문제 해결을 포함한 단계별 설정 지침은 **[자체 호스팅 가이드](self-hosting.md)**를 참조하세요.

---

## 환경 변수

루트 디렉토리에 다음 설정으로 `.env` 파일을 생성하세요:

### 필수 변수

```bash
# 시스템 환경 - 배포 환경 지정
VITE_SYSTEM_ENVIRONMENT=production

# 버전 - 자동으로 package.json 버전 사용
VITE_VERSION=$npm_package_version

# PocketBase 백엔드 URL - 후행 슬래시 없음
VITE_POCKETBASE_URL=https://your-backend-url.com

# 법적 페이지 - 프로덕션에 필수
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about

# 지도 및 라우팅
VITE_OPENROUTE_API_KEY=your_openrouteservice_api_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_api_key
```

### 선택적 변수

```bash
# 오류 추적 (Sentry) - 프로덕션에 권장
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456
# 소스맵 업로드를 위한 빌드 시 Sentry 토큰
SENTRY_AUTH_TOKEN=your_sentry_auth_token
SENTRY_ORG=your_sentry_org_slug
SENTRY_PROJECT=your_sentry_project_slug

# 유지 관리 모드 - 사용자에게 유지 관리 배너 표시
VITE_MAINTENANCE_MODE=false
```

### 변수 세부 정보

#### VITE_SYSTEM_ENVIRONMENT

- **목적**: 앱이 실행 중인 환경 결정
- **값**:
  - `local` - 로컬 머신에서 개발
  - `production` - 라이브 배포
- **영향**: Sentry 오류 추적 구성 및 로깅에 영향

#### VITE_VERSION

- **목적**: 오류 보고서에서 애플리케이션 버전 추적
- **값**: `$npm_package_version` (package.json에서 자동 읽기)
- **현재 버전**: 1.9.1 (package.json 기준)
- **영향**: Sentry 보고서에 표시되며 어떤 버전에 문제가 있는지 추적하는 데 도움

#### VITE_POCKETBASE_URL

- **목적**: PocketBase 백엔드 서버의 URL
- **형식**: 후행 슬래시 없는 전체 URL
- **예**: `https://api.ministry-mapper.com` 또는 로컬의 경우 `http://localhost:8090`
- **중요**: 프론트엔드가 배포된 곳에서 접근 가능해야 함

#### VITE_PRIVACY_URL

- **목적**: 개인정보 보호정책 페이지 링크
- **필수**: 예, 법적 준수를 위해 (GDPR, CCPA 등)
- **예**: `https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **목적**: 서비스 약관 페이지 링크
- **필수**: 예, 법적 준수를 위해
- **예**: `https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **목적**: Ministry Mapper 인스턴스에 대한 정보 링크
- **필수**: 아니오, 하지만 권장
- **예**: `https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **목적**: 모니터링을 위해 JavaScript 오류를 Sentry로 전송
- **얻는 곳**: [sentry.io](https://sentry.io) (무료 티어 제공)
- **필수**: 아니오, 하지만 프로덕션에 강력히 권장
- **이점**: 오류 추적, 성능 모니터링, 문제 알림 받기
- **참고**: VITE_SYSTEM_ENVIRONMENT가 "production"일 때만 활성화

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **목적**: `npm run build` 중 소스맵을 Sentry에 업로드하여 축소된 스택 추적을 원본 소스 코드로 다시 해결할 수 있도록 사용
- **필수**: 아니오 — 없으면 소스맵이 로컬에서 생성되지만, Sentry에 업로드하면 프로덕션에서 오류 디버깅이 크게 향상됨
- **얻는 방법**: Sentry 조직 설정의 **설정 → 인증 토큰**에서 인증 토큰 생성

#### VITE_OPENROUTE_API_KEY

- **목적**: 대화형 지도에서 턴바이턴 라우팅 및 길 안내(자동차, 도보, 자전거) 제공
- **얻는 곳**: [openrouteservice.org](https://openrouteservice.org) (무료 티어 제공)
- **필수**: 예

#### VITE_LOCATIONIQ_API_KEY

- **목적**: 지오코딩(주소를 좌표로) 및 역 지오코딩(좌표를 주소로)
- **얻는 곳**: [locationiq.com](https://locationiq.com) (무료 티어 제공)
- **필수**: 예

#### VITE_MAINTENANCE_MODE

- **목적**: `true`로 설정하면 사용자에게 유지 관리 배너 표시
- **값**: `true` 또는 `false`
- **기본값**: `false`
- **사용 사례**: 백엔드 업그레이드 또는 계획된 다운타임 중 일시적으로 `true`로 설정

## 로컬 개발 설정

### 전제 조건

Node.js **>=24.0.0**이 필요합니다:

```bash
# Node.js 버전 확인
node --version
```

Node.js 24+가 없다면 다음에서 다운로드하세요: [nodejs.org](https://nodejs.org)

### 설정 단계

#### 1. 저장소 복제

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. 종속성 설치

```bash
npm install
```

인터넷 연결에 따라 몇 분이 걸릴 수 있습니다.

#### 3. 환경 파일 생성

```bash
# 예제 환경 파일 복사
cp .env.example .env
```

`.env`를 다음 설정으로 편집하세요:

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=http://localhost:8090
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
VITE_OPENROUTE_API_KEY=
VITE_LOCATIONIQ_API_KEY=
VITE_MAINTENANCE_MODE=false
```

**참고**: 포트 8090에서 실행 중인 PocketBase 백엔드가 필요합니다. 백엔드 설정은 ministry-mapper-be 저장소를 참조하세요.

#### 4. 개발 서버 시작

```bash
npm start
```

앱이 `http://localhost:3000`에서 열립니다 (포트 3000은 vite.config.js에서 구성됨).

### 개발 기능

- **Hot Module Replacement (HMR)**: 전체 페이지 새로고침 없이 변경 사항 즉시 반영
- **TypeScript 검사**: 터미널 및 브라우저 콘솔에 오류 표시
- **소스맵**: 원본 소스 코드 참조로 디버깅 용이
- **Fast Refresh**: 상태를 잃지 않고 React 컴포넌트 업데이트

### 개발 명령

```bash
# 개발 서버 시작 (포트 3000)
npm start

# Vitest로 테스트 실행
npm test

# 코드 서식 검사
npm run prettier

# 서식 문제 자동 수정
npm run prettier:fix

# 프로덕션용 빌드
npm run build

# 프로덕션 빌드 로컬 미리보기
npm run serve
```

### 코드 품질 도구

**Prettier** (코드 서식):

- `.prettierrc`에서 구성
- Husky를 통해 커밋 시 자동 서식 지정
- `npm run prettier:fix`로 수동 실행

**ESLint** (코드 린팅):

- `eslint.config.mjs`에서 구성
- React 및 TypeScript 규칙 활성화
- 일반적인 오류 및 코드 품질 문제 검사

**Husky** (git 훅):

- 커밋 전 스테이징된 파일에 Prettier 실행
- 일관된 코드 서식 보장
- commitlint로 커밋 메시지 검증

## 프로덕션 배포

### 프로덕션용 빌드

```bash
# 최적화된 프로덕션 파일 빌드
npm run build
```

**빌드 중 수행되는 작업:**

- TypeScript를 JavaScript로 컴파일
- React 코드 최적화 및 축소
- CSS 처리 및 축소
- 소스맵 생성
- 더 빠른 로딩을 위해 코드를 청크로 분할
- 출력은 `build/` 디렉토리로
- PWA 지원을 위한 서비스 워커 생성

**빌드 출력:**

- `build/index.html` - 메인 HTML 파일
- `build/assets/` - JavaScript, CSS 및 기타 자산
- 캐싱을 위해 파일 이름에 콘텐츠 해시 포함
- 총 크기는 일반적으로 1MB 미만 (외부 종속성 제외)

### 배포 옵션

Ministry Mapper 프론트엔드는 정적 웹 애플리케이션입니다. 모든 정적 호스팅 서비스에 배포할 수 있습니다.

#### 옵션 1: Vercel (권장)

빠르고, 무료 티어, 자동 배포.

**설정:**

1. [vercel.com](https://vercel.com)에서 계정 생성
2. "New Project" 클릭
3. GitHub 저장소 가져오기
4. 구성:
   - 프레임워크 프리셋: Vite
   - 빌드 명령: `npm run build`
   - 출력 디렉토리: `build`
   - 설치 명령: `npm install`
5. Vercel 대시보드에서 환경 변수 추가
6. 배포

**Vercel 기능:**

- git push 시 자동 배포
- Pull request에 대한 미리보기 배포
- 사용자 정의 도메인
- 기본 HTTPS
- 글로벌 CDN
- 개인 프로젝트에 무료

#### 옵션 2: Netlify

훌륭한 기능을 갖춘 간단한 배포.

**설정:**

1. [netlify.com](https://netlify.com)에서 계정 생성
2. "Add new site" → "Import an existing project"
3. GitHub 저장소 연결
4. 빌드 설정:
   - 빌드 명령: `npm run build`
   - 게시 디렉토리: `build`
5. 환경 변수 추가
6. 배포

**Netlify 기능:**

- 자동 배포
- 폼 처리
- 서버리스 함수 (필요한 경우)
- 사용자 정의 도메인
- 기본 HTTPS

#### 옵션 3: Cloudflare Pages

넉넉한 무료 티어를 갖춘 빠른 CDN.

**설정:**

1. [cloudflare.com](https://cloudflare.com)에서 계정 생성
2. Pages → "Create a project"로 이동
3. GitHub 저장소 연결
4. 빌드 설정:
   - 프레임워크 프리셋: Vite
   - 빌드 명령: `npm run build`
   - 빌드 출력 디렉토리: `build`
5. 환경 변수 추가
6. 배포

**Cloudflare 기능:**

- 글로벌 CDN 네트워크
- DDoS 보호
- 분석
- 클라이언트 측 추적 없는 웹 분석

#### 옵션 4: AWS S3 + CloudFront

더 많은 제어, 대규모 배포에 적합.

**설정:**

1. S3 버킷 생성
2. 정적 웹사이트 호스팅 활성화
3. `build/` 콘텐츠 업로드
4. CloudFront 배포 생성
5. 사용자 정의 도메인 구성
6. SSL 인증서 설정

**AWS 고려 사항:**

- 더 복잡한 설정
- 사용량에 따른 지불 (보통 매우 저렴)
- 더 많은 구성 옵션
- 기업 배포에 적합

#### 옵션 5: 자체 호스팅

자체 서버에서 호스팅.

**Nginx 사용:**

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # gzip 압축 활성화
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # 정적 자산 캐시
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**Apache 사용:**

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # React Router 활성화
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

**자체 호스팅 시 중요 사항:**

- HTTPS를 통해 제공 (PWA 기능에 필수)
- 백엔드가 다른 도메인에 있는 경우 적절한 CORS 헤더 구성
- 정적 자산에 대한 적절한 캐싱 헤더 설정
- SPA 라우팅 작동 확인 (모든 경로가 index.html 제공)

### 배포 시 환경 변수

**중요**: Vite가 빌드 시 환경 변수를 주입하기 때문에 환경 변수는 런타임이 아닌 빌드 시에 설정해야 합니다.

**Vercel/Netlify/Cloudflare의 경우:**

- 플랫폼 대시보드에서 환경 변수 추가
- 빌드 프로세스 중에 사용 가능

**자체 호스팅의 경우:**

- `npm run build` 실행 전에 환경 변수 설정
- 또는 설정이 포함된 `.env.production` 파일 생성

## Sentry 설정 (선택 사항이지만 권장)

Sentry는 JavaScript 오류와 성능을 모니터링합니다.

### 1. Sentry 계정 생성

1. [sentry.io](https://sentry.io)로 이동
2. 가입 (넉넉한 무료 티어 제공)
3. 새 프로젝트 생성
4. 플랫폼 선택: "React"

### 2. DSN 얻기

1. 프로젝트 생성 후 DSN이 표시됨
2. 또는 설정 → 프로젝트 → [프로젝트] → 클라이언트 키 (DSN)로 이동
3. DSN URL 복사

### 3. 환경에 추가

```bash
VITE_SENTRY_DSN=https://your_key@your_org.sentry.io/your_project_id
```

**참고**: Sentry는 `VITE_SYSTEM_ENVIRONMENT`가 `production`으로 설정된 경우에만 활성화됩니다.

### 4. Sentry 구성 (선택 사항)

**소스맵**: 빌드 프로세스가 자동으로 소스맵을 생성하여 축소된 프로덕션 코드를 디버깅하는 데 도움이 됩니다.

**알림**: Sentry 대시보드에서 설정:

- 새 이슈에 대한 이메일 알림
- Slack/Discord 통합
- 중요 오류에 대한 사용자 정의 알림 규칙
- 릴리스 추적

## 다국어 지원

Ministry Mapper에는 기본적으로 7개 언어가 포함되어 있습니다.

### 지원 언어

- 영어 (`en`)
- 스페인어 (`es` - Español)
- 일본어 (`ja` - 日本語)
- 한국어 (`ko` - 한국어)
- 중국어 (`zh` - 中文)
- 인도네시아어 (`id` - Bahasa Indonesia)
- 말레이어 (`ms` - Bahasa Melayu)

### 언어 감지 작동 방식

- `i18next-browser-languagedetector` 사용
- 첫 방문 시 브라우저 설정에서 자동 감지
- 사용자는 네비게이션 바의 언어 선택기를 통해 수동으로 언어 전환 가능
- 선택은 `localStorage`에 저장되며 브라우저 설정보다 우선
- 선택한 언어가 지원되지 않으면 영어로 폴백

### 새 언어 추가

1. **번역 파일 생성**

   - `src/i18n/locales/`로 이동
   - 새 파일 생성: `[언어-코드].json` (예: 프랑스어의 경우 `fr.json`)
   - `en.json`에서 구조 복사

2. **모든 문자열 번역**

   - 모든 키-값 쌍 번역
   - 동일한 JSON 구조 유지
   - `{{variable}}`과 같은 자리 표시자 변수 유지

3. **언어 등록**
   - `src/i18n/index.ts` 편집
   - 번역 파일 가져오기
   - resources 객체에 추가

프랑스어 예:

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... 다른 언어
}
```

## 프로그레시브 웹 앱 (PWA)

Ministry Mapper는 Vite PWA 플러그인을 통해 PWA를 지원합니다.

### PWA 기능

- **설치 가능**: 모바일/데스크톱 홈 화면에 설치 가능
- **오프라인 자산**: 더 빠른 로딩을 위해 정적 파일 캐시
- **서비스 워커**: 자동 생성 및 등록
- **캐시 전략**: 외부 자산에 StaleWhileRevalidate, 폰트에 CacheFirst

### PWA 구성

`vite.config.js`의 구성:

```javascript
VitePWA({
  registerType: "autoUpdate", // 서비스 워커 자동 업데이트
  manifest: false, // 매니페스트 없음 (필요한 경우 추가 가능)
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // 새 서비스 워커 즉시 활성화
    clientsClaim: true, // 클라이언트 즉시 제어
    cleanupOutdatedCaches: true, // 오래된 캐시 제거
  },
});
```

### 캐싱 전략

**외부 자산** (예: assets.ministry-mapper.com):

- 전략: StaleWhileRevalidate
- 캐시 이름: "external-assets"
- 만료: 7일, 최대 50개 항목

**폰트**:

- 전략: CacheFirst
- 캐시 이름: "fonts"
- 만료: 30일, 최대 30개 항목

**참고**: 앱은 데이터 작업 (PocketBase 연결)을 위해 인터넷이 필요합니다. 정적 자산만 오프라인으로 캐시됩니다.

## 배포 테스트

### 기능 체크리스트

- [ ] 홈페이지가 올바르게 로드됨
- [ ] 가입 페이지 작동
- [ ] 이메일 인증 작동
- [ ] 로그인 페이지 작동
- [ ] OTP 인증 작동 (활성화된 경우)
- [ ] 비밀번호 재설정 작동
- [ ] 구역 선택기 표시됨 (인도자/관리자의 경우)
- [ ] 지도가 올바르게 로드되고 표시됨
- [ ] 구역 조회 가능
- [ ] 주소 상태 업데이트 가능
- [ ] 배정 링크 작동
- [ ] 링크가 올바르게 만료됨
- [ ] 모바일 뷰가 반응형
- [ ] 언어 감지 작동
- [ ] 모든 모달이 올바르게 열리고 닫힘

### 성능 검사

- [ ] 초기 페이지 로드 3초 미만
- [ ] Lighthouse 점수 > 90
- [ ] 콘솔 오류 없음
- [ ] 이미지가 빠르게 로드됨
- [ ] 지도가 반응형
- [ ] 부드러운 스크롤 및 네비게이션

### 보안 검사

- [ ] HTTPS 적용 (HTTP 접근 없음)
- [ ] 개인정보 보호정책 링크 작동
- [ ] 서비스 약관 링크 작동
- [ ] 브라우저 코드에 API 키 노출 안 됨
- [ ] 백엔드에 대한 CORS가 올바르게 구성됨
- [ ] PocketBase 연결이 안전함

### 브라우저 테스트

여러 브라우저에서 테스트:

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] 모바일 브라우저

## 문제 해결

### 백엔드 연결 실패

**문제**: "Cannot connect to server" 또는 "Network Error"

**해결 방법:**

- `VITE_POCKETBASE_URL`이 올바른지 확인 (후행 슬래시 없음)
- PocketBase 백엔드가 실행 중인지 확인
- 브라우저에서 백엔드 URL 직접 테스트
- PocketBase의 CORS 설정 확인 (프론트엔드 도메인 허용 필요)
- 브라우저 DevTools → 네트워크 탭을 열어 정확한 오류 확인
- 배포 위치에서 백엔드에 접근 가능한지 확인

### 빌드 오류

**문제**: `npm run build` 실패

**해결 방법:**

- 모든 환경 변수가 올바르게 설정되었는지 확인
- `npm install`을 실행하여 종속성 새로고침
- `node_modules` 삭제 후 재설치: `rm -rf node_modules && npm install`
- Node.js 버전 확인: `node --version` (24+ 필요)
- TypeScript 오류 확인: 특정 오류 메시지 확인
- `npm run prettier:fix`를 시도하여 서식 문제 수정
- Vite 캐시 지우기: `rm -rf node_modules/.vite`

### TypeScript 오류

**문제**: 빌드 중 타입 오류

**해결 방법:**

- 타입 정의를 위해 `src/utils/interface.ts` 확인
- 가져오기가 올바른지 확인
- `npx tsc --noEmit`을 실행하여 모든 타입 오류 확인
- 모든 종속성이 설치되었는지 확인

### 느린 성능

**문제**: 앱이 느리거나 지연됨

**해결 방법:**

- 인터넷 연결 속도 확인
- 브라우저 캐시를 지우고 새로고침
- PocketBase 백엔드가 빠르게 응답하는지 확인
- 브라우저 콘솔에서 JavaScript 오류 확인
- 문제를 격리하기 위해 다른 기기/브라우저에서 테스트
- Lighthouse 성능 점수 확인
- 서비스 워커 작동 확인: DevTools → 애플리케이션 → 서비스 워커

### PWA 설치 문제

**문제**: "홈 화면에 추가"가 나타나지 않음

**해결 방법:**

- HTTPS를 통해 제공되어야 함
- DevTools에서 서비스 워커 등록 확인
- PWA 요구 사항 충족 확인 (매니페스트, 서비스 워커 등)
- 다른 브라우저 시도 (Safari, Chrome은 PWA 지원이 다름)

### 실시간 업데이트 작동 안 함

**문제**: 다른 사용자의 변경 사항이 나타나지 않음

**해결 방법:**

- PocketBase 실시간 구독이 작동하는지 확인
- 브라우저 콘솔에서 SSE (Server-Sent Events) 오류 확인
- 백엔드 SSE 엔드포인트에 접근 가능한지 확인
- 여러 사용자가 실제로 같은 구역을 보고 있는지 확인
- 페이지를 새로고침하여 재연결 강제

## 유지 관리

### 종속성 업데이트 유지

```bash
# 저장소에서 최신 변경 사항 가져오기
git pull origin main

# 업데이트된 종속성 설치
npm install

# 보안 취약점 확인
npm audit

# 보안 문제 자동 수정 (가능한 경우)
npm audit fix

# 오래된 패키지 확인
npm outdated

# 특정 패키지 업데이트
npm update package-name
```

### 모니터링

**Sentry 대시보드:**

- 매주 새 오류 확인
- 오류 트렌드 검토
- 중요한 문제 신속히 수정
- 높은 우선순위 오류에 대한 알림 설정

**Google Cloud Console:**

- API 사용량을 모니터링하여 할당량 내 유지
- 비정상적인 사용량 급증 확인
- 청구 알림 검토
- API가 계속 활성화되어 있는지 확인

**성능:**

- 주기적으로 Lighthouse 감사 실행
- 페이지 로드 시간 모니터링
- Core Web Vitals 확인
- 느린 연결에서 테스트

**사용자 피드백:**

- 회중 구성원의 의견 수렴
- 일반적인 문제 추적
- 해결 방법 문서화
- 필요에 따라 문서 업데이트

### 버전 업데이트

Ministry Mapper는 시맨틱 버전 관리를 사용합니다:

- **메이저 버전** (x.0.0): 주요 변경 사항
- **마이너 버전** (1.x.0): 새 기능
- **패치 버전** (1.9.x): 버그 수정

## 다음 단계

**사용자의 경우:**

- [사용자 가이드](user-guide.md)를 읽어 애플리케이션 사용법 학습

**관리자의 경우:**

- PocketBase 백엔드 설정 (ministry-mapper-be 저장소 참조)
- 회중 설정 구성
- 사용자 초대 및 역할 할당
- 구역 생성 및 주소 추가
