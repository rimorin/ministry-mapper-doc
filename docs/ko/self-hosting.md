# Ministry Mapper 자체 호스팅

!!! warning "자체 호스팅 권장하지 않음"
    Ministry Mapper 자체 호스팅은 상당한 기술 전문 지식과 지속적인 유지 관리가 필요합니다. **대신 [ministry-mapper.com](https://ministry-mapper.com)에서 호스팅 서비스를 사용하는 것을 권장합니다.** 다음 조건을 갖춘 경우에만 자체 호스팅을 진행하세요:

    - 경험 있는 시스템 관리자 또는 개발자
    - 지속적인 유지 관리 및 업데이트를 위한 리소스
    - 보안 모범 사례에 대한 이해
    - 개인정보 보호 규정 준수 능력
    - 호스팅 서비스로 충족할 수 없는 특정 요구 사항

## 자체 호스팅을 권장하지 않는 이유

Ministry Mapper는 오픈 소스이지만 자체 호스팅에는 어려움이 있습니다:

- **기술적 복잡성**: Docker, 데이터베이스, 웹 서버 및 클라우드 인프라에 대한 지식 필요
- **보안 책임**: 모든 것을 안전하고 최신 상태로 유지해야 함
- **유지 관리 부담**: 정기적인 업데이트, 백업 및 모니터링 필수
- **개인정보 보호 규정 준수**: GDPR, CCPA 및 기타 개인정보 보호법 준수 보장 필요
- **비용**: 서버 비용, API 비용 및 시간 투자가 종종 호스팅 서비스 요금을 초과
- **지원**: 자체 호스팅 문제에 대한 커뮤니티 지원 제한

## 자체 호스팅 전제 조건

권장 사항에도 불구하고 자체 호스팅을 결정했다면 다음을 확인하세요:

### 기술 요구 사항
- Linux 서버 관리 경험
- Docker 및 컨테이너화에 대한 이해
- 환경 변수 및 구성에 대한 친숙함
- 웹 서버 구성에 대한 지식 (Nginx/Apache)
- SSL/TLS 인증서 경험
- DNS 및 도메인 구성에 대한 이해

### 인프라 요구 사항
- 클라우드 호스팅 서비스 (Railway, Render, DigitalOcean, AWS 등)
- 인스턴스용 도메인 이름
- 알림용 이메일 서비스 (선택 사항)
- 선택적 API 비용을 위한 예산 (Sentry 등)

### 시간 투자
- 초기 설정: 2-4시간
- 지속적인 유지 관리: 월 2-4시간
- 문제 발생 시 긴급 지원

## 아키텍처 개요

Ministry Mapper는 두 개의 별도 구성 요소로 구성됩니다:

1. **백엔드 (ministry-mapper-be)**: Go 확장이 포함된 PocketBase 서버
   - 저장소: [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - 기술: Go, PocketBase, SQLite
   - 포트 8080 (Docker) 또는 8090 (로컬)에서 실행

2. **프론트엔드 (ministry-mapper-v2)**: React 웹 애플리케이션
   - 저장소: [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - 기술: React, TypeScript, Vite
   - 정적 파일이 호스팅 서비스에 배포됨

둘 다 함께 작동하도록 배포하고 구성해야 합니다.

## 백엔드 자체 호스팅 가이드

### 환경 구성

다음 설정으로 `.env` 파일을 생성하세요:

```bash
# 애플리케이션 설정
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-domain.com

# 초기 관리자 계정 (첫 로그인 후 비밀번호 변경!)
PB_ADMIN_EMAIL=admin@your-domain.com
PB_ADMIN_PASSWORD=change_this_secure_password

# 이메일 구성 (SMTP)
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=your_app_password
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# 보안 설정
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# 인증 (선택 사항)
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# 오류 추적 (선택 사항이지만 권장)
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# 서비스 통합 (선택 사항)
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Docker 배포

1. **저장소 복제**
```bash
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be
```

2. **Docker 이미지 빌드**
```bash
docker build -t ministry-mapper-backend .
```

3. **컨테이너 실행**
```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**중요 참고 사항:**
- 컨테이너는 내부적으로 포트 8080에서 실행됩니다
- 데이터 보존을 위해 `/app/pb_data`에 영구 볼륨 마운트
- 데이터베이스는 `pb_data` 폴더에 있습니다

4. **관리자 패널 접근**
`https://your-backend-domain.com/_/`로 이동하여 관리자 자격 증명으로 로그인하세요.

### 로컬 개발

로컬 머신에서 테스트하려면:

```bash
# 저장소 복제
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# 종속성 설치
./scripts/install.sh

# 환경 구성
cp .env.sample .env
# .env를 설정으로 편집

# 애플리케이션 시작
./scripts/start.sh
```

백엔드는 기본적으로 `http://localhost:8090`에서 실행됩니다.

### 데이터베이스 백업

데이터는 매우 중요합니다. 자동 백업을 설정하세요:

```bash
# 수동 백업
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/

# 자동 일일 백업 (crontab에 추가)
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

### 보안 체크리스트

- [ ] 기본 관리자 비밀번호 즉시 변경
- [ ] HTTPS만 사용 (HTTP 절대 안 됨)
- [ ] `PB_ALLOW_ORIGINS`를 특정 도메인으로 설정 (`*` 아님)
- [ ] `PB_HIDE_CONTROLS=true` 활성화
- [ ] `PB_ENABLE_RATE_LIMITING=true` 활성화
- [ ] 접근 제한을 위한 방화벽 구성
- [ ] 자동 백업 설정
- [ ] 오류 모니터링을 위한 Sentry 구성
- [ ] 강력하고 고유한 비밀번호 사용
- [ ] 종속성 최신 상태 유지
- [ ] PocketBase 보안 모범 사례 검토

## 프론트엔드 자체 호스팅 가이드

### 환경 구성

`.env` 파일 생성:

```bash
# 시스템 환경
VITE_SYSTEM_ENVIRONMENT=production

# 버전
VITE_VERSION=$npm_package_version

# PocketBase 백엔드 URL (후행 슬래시 없음)
VITE_POCKETBASE_URL=https://your-backend-domain.com

# 법적 페이지 (필수)
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms
VITE_ABOUT_URL=https://your-domain.com/about

# 오류 추적 (선택 사항이지만 권장)
VITE_SENTRY_DSN=your_sentry_dsn
```

### 프로덕션용 빌드

```bash
# 저장소 복제
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# 종속성 설치
npm install

# 빌드
npm run build
```

출력은 `build/` 디렉토리에 있습니다.

### 배포 옵션

#### 정적 호스팅 서비스 (가장 쉬움)

**Vercel:**
1. GitHub 저장소 가져오기
2. 프레임워크: Vite
3. 빌드 명령: `npm run build`
4. 출력 디렉토리: `build`
5. 환경 변수 추가

**Netlify:**
1. Git에서 새 사이트
2. 빌드 명령: `npm run build`
3. 게시 디렉토리: `build`
4. 환경 변수 추가

**Cloudflare Pages:**
1. Pages 프로젝트 생성
2. 프레임워크: Vite
3. 빌드 명령: `npm run build`
4. 출력 디렉토리: `build`
5. 환경 변수 추가

#### 자체 호스팅 웹 서버

**Nginx 구성:**

```nginx
server {
    listen 443 ssl http2;
    server_name your-domain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 캐싱
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # 보안 헤더
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

**Apache 구성:**

```apache
<VirtualHost *:443>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    SSLEngine on
    SSLCertificateFile /path/to/cert.pem
    SSLCertificateKeyFile /path/to/key.pem

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

### Sentry 설정 (선택 사항)

1. [sentry.io](https://sentry.io)에서 계정 생성
2. React 프로젝트 생성
3. DSN 얻기
4. `.env`에 `VITE_SENTRY_DSN`으로 추가

## 유지 관리 및 업데이트

### 정기 유지 관리 작업

**매주:**
- Sentry에서 오류 확인
- 애플리케이션 로그 검토
- 백업 작동 확인

**매월:**
- 종속성 업데이트: `npm install` 및 `go get -u`
- 보안 권고 검토
- 재해 복구 절차 테스트
- 디스크 공간 사용량 확인
- 사용자 피드백 검토

**분기별:**
- 보안 감사
- 성능 검토
- 문서 업데이트
- 개인정보 보호정책 검토 및 업데이트
- 모든 기능 철저히 테스트

### 새 버전으로 업데이트

**백엔드 업데이트:**
```bash
cd ministry-mapper-be
git pull origin main
docker build -t ministry-mapper-backend .
docker stop ministry-mapper
docker rm ministry-mapper
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**프론트엔드 업데이트:**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# build/ 폴더를 호스팅 서비스에 배포
```

**업데이트 전 항상 백업하세요!**

### 모니터링

**모니터링할 항목:**
- 서버 가동 시간 및 상태
- 애플리케이션 오류율 (Sentry)
- 데이터베이스 크기 증가
- 백업 성공/실패
- SSL 인증서 만료
- 사용자 인증 문제
- 성능 메트릭

**도구:**
- 오류 추적을 위한 Sentry
- API 사용량을 위한 Google Cloud Console
- 서버 모니터링 (Prometheus, Grafana 등)
- 가동 시간 모니터 (UptimeRobot, Pingdom)
- 로그 집계 (ELK 스택, CloudWatch)

## 자체 호스팅 인스턴스 문제 해결

### 백엔드 문제

**사용 중인 포트:**
```bash
lsof -i :8080
kill -9 <process_id>
```

**데이터베이스 잠김:**
- 데이터베이스에 접근하는 모든 인스턴스 중지
- 하나의 백엔드 프로세스만 실행되는지 확인
- 중단된 프로세스 확인

**이메일 전송 안 됨:**
- SMTP 자격 증명 확인
- 방화벽이 SMTP 포트를 차단하지 않는지 확인
- Gmail의 경우 앱 비밀번호 사용
- `pb_data/logs/`에서 이메일 로그 검토

**관리자 패널 접근 불가:**
- URL은 `https://your-domain.com/_/`여야 함 (밑줄 주의)
- `PB_HIDE_CONTROLS` 설정 확인
- 백엔드가 실행 중인지 확인
- 브라우저 캐시 지우기

### 프론트엔드 문제

**백엔드에 연결 불가:**
- `VITE_POCKETBASE_URL`이 올바른지 확인
- 백엔드의 CORS 설정 확인
- 백엔드에 접근 가능한지 확인
- 브라우저 콘솔에서 오류 확인

**빌드 실패:**
- Node.js 버전 22+ 확인
- `node_modules` 삭제 후 재설치
- 모든 환경 변수가 설정되었는지 확인
- 빌드 오류 메시지 검토

### 성능 문제

**느린 응답 시간:**
- 서버 리소스 확인 (CPU, RAM, 디스크)
- 데이터베이스 크기 및 쿼리 검토
- 데이터베이스 최적화 활성화
- 네트워크 지연 시간 확인
- 느린 쿼리에 대한 PocketBase 로그 검토

## 개인정보 보호 및 법적 준수

### 자체 호스터로서의 책임

자체 호스팅 시 데이터 컨트롤러로서 다음을 수행해야 합니다:

- **개인정보 보호법 준수**: GDPR (EU), CCPA (캘리포니아) 등
- **개인정보 보호정책 작성**: 데이터 수집 및 사용 설명
- **서비스 약관 작성**: 사용자 책임 정의
- **데이터 보안 구현**: 암호화, 접근 제어, 백업
- **데이터 요청 처리**: 사용자 접근, 삭제, 이동 권리
- **데이터 침해 보고**: 법적 기한 내
- **기록 유지**: 데이터 처리 활동
- **DPO 임명**: 규정에서 요구하는 경우

### 필수 법적 문서

제공해야 하는 문서:

1. **개인정보 보호정책** - 법적으로 필수, 설명해야 하는 내용:
   - 수집하는 데이터
   - 사용 방법
   - 보관 기간
   - 사용자 권리
   - 연락 방법

2. **서비스 약관** - 정의하는 내용:
   - 허용 가능한 사용
   - 사용자 책임
   - 책임 제한
   - 해지 정책

3. **쿠키 정책** - 쿠키/추적 사용 시

### 데이터 보호 조치

- 모든 곳에서 HTTPS 사용
- 저장 시 데이터 암호화
- 접근 제어 구현
- 정기 보안 감사
- 백업 암호화
- 안전한 비밀번호 정책
- 다단계 인증
- 활동 로깅
- 정기 보안 업데이트

## 비용 고려 사항

### 인프라 비용

**백엔드 호스팅:**
- VPS: $10-50/월 (DigitalOcean, Linode)
- PaaS: $10-30/월 (Railway, Render)
- 클라우드: 가변 (AWS, GCP)

**프론트엔드 호스팅:**
- 정적 호스팅: $0-10/월 (Vercel, Netlify, Cloudflare)
- CDN 비용: 보통 포함

**도메인:**
- $10-20/년

**SSL 인증서:**
- 무료 (Let's Encrypt) 또는 $0-100/년

### 서비스 비용

**이메일 서비스:**
- Gmail SMTP: 무료 (제한)
- MailerSend: 무료 티어 제공
- SendGrid: 무료 티어 제공

**오류 추적:**
- Sentry: 무료 티어 제공
- 유료: $26-80/월

**백업 저장소:**
- 크기에 따라 $5-20/월

### 시간 투자

**초기 설정:**
- 백엔드: 2-3시간
- 프론트엔드: 1-2시간
- 구성: 1-2시간
- 테스트: 2-3시간
- **총계: 6-10시간**

**월간 유지 관리:**
- 모니터링: 1-2시간
- 업데이트: 1-2시간
- 지원: 1-3시간
- **총계: 3-7시간**

### 총 소유 비용 (연간)

**소규모 회중 (< 100명 사용자):**
- 인프라: $240-720
- API: $0-100 (보통 무료 티어)
- 서비스: $0-300
- 도메인/SSL: $10-20
- **총계: $250-1,140 + 연간 48-96시간**

커밋하기 전에 호스팅 서비스 구독과 비교하세요.

## 도움 받기

### 커뮤니티 지원

- GitHub Issues: [ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues) 및 [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- 새 이슈를 만들기 전에 기존 이슈 읽기
- 문제 보고 시 자세한 정보 제공

### 지원 요청에 포함할 내용

1. 버전 번호 (백엔드 및 프론트엔드)
2. 배포 환경 (Docker, 로컬, 호스팅 서비스)
3. 오류 메시지 (전체 텍스트)
4. 재현 단계
5. 예상 vs 실제 동작
6. 관련 로그
7. 구성 (비밀 정보 제외)

### 제한된 지원

!!! info "지원 제한"
    자체 호스팅 지원은 커뮤니티 기반이며 제한적입니다. Ministry Mapper 팀은 호스팅 서비스를 우선시합니다. 전문 지원을 원하시면 대신 호스팅 서비스 사용을 고려하세요.

## 자체 호스팅의 대안

자체 호스팅 전에 고려하세요:

1. **[ministry-mapper.com](https://ministry-mapper.com)에서 공식 호스팅 서비스 사용**
   - 유지 관리 부담 없음
   - 전문 지원
   - 자동 업데이트
   - 더 나은 신뢰성
   - 규정 준수 처리
   - 지금 바로 사용 가능!

2. **기능 요청**
   - 사용자 정의가 필요하면 메인 프로젝트에 기능 요청
   - 모든 사람에게 이점
   - 팀에서 유지 관리
   - 호스팅 서비스에 추가될 수 있음

3. **프로젝트에 기여**
   - 메인 코드베이스 개선
   - 다른 사람 돕기
   - 커뮤니티 지원 받기

## 결론

Ministry Mapper 자체 호스팅은 가능하지만 상당한 전문 지식, 시간 및 리소스가 필요합니다. **추가 복잡성을 정당화하는 특정 요구 사항이 없는 한 [ministry-mapper.com](https://ministry-mapper.com)에서 호스팅 서비스를 사용하는 것을 강력히 권장합니다.**

### 대신 호스팅 서비스 사용

**[ministry-mapper.com](https://ministry-mapper.com)**을 방문하세요:
- 즉시 설정 - 기술 지식 불필요
- 필요할 때 전문 지원
- 자동 백업 및 보안 업데이트
- 법적 준수 지원
- 더 나은 신뢰성 및 가동 시간
- 더 낮은 총 소유 비용

### 그래도 자체 호스팅하시겠습니까?

자체 호스팅을 진행하는 경우:
- 모든 보안 모범 사례 따르기
- 시스템 최신 상태 유지
- 정기 백업 유지
- 법적 준수 보장
- 시스템 상태 모니터링
- 지속적인 비용 예산 책정
- 유지 관리를 위한 시간 할당

이 전체 가이드를 읽고 의미를 이해했는지 확인하세요. 행운을 빕니다!
