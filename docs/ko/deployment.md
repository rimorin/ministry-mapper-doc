# 배포 가이드

## 개요

이 가이드는 호스팅 서비스 사용부터 고급 자체 호스팅 구성까지 Ministry Mapper의 다양한 배포 옵션을 다룹니다.

## 빠른 배포 결정 트리

```
┌─────────────────────────────────────┐
│ 기술 전문 지식이 있고               │
│ 특정 자체 호스팅 요구 사항이 있나요? │
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      예              아니오
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ 자체 호스팅  │              │ 호스팅 서비스    │
│              │              │ 사용             │
│ 아래 참조    │              │                  │
└──────────────┘              │ ministry-mapper  │
                              │ .com             │
                              └──────────────────┘
```

## 옵션 1: 호스팅 서비스 (권장)

**최적 대상:** 대부분의 회중, 소규모에서 중규모 조직

**URL:** [ministry-mapper.com](https://ministry-mapper.com)

### 장점

✅ **설정 불필요** - 즉시 사용 시작
✅ **전문 지원** - 필요할 때 도움 받기
✅ **자동 업데이트** - 항상 최신 버전
✅ **관리형 인프라** - 서버 관리 불필요
✅ **더 나은 보안** - 전문 보안 관행
✅ **규정 준수 지원** - GDPR, CCPA 등 지원
✅ **백업 포함** - 자동 데이터 백업
✅ **확장성** - 성장에 따라 자동 처리
✅ **비용 효율적** - 종종 자체 호스팅보다 저렴

### 시작하기

1. [ministry-mapper.com](https://ministry-mapper.com) 방문
2. 계정 생성
3. 이메일 인증
4. 관리자에게 연락하여 회중 접근 요청
5. 즉시 사용 시작

**기술 지식 불필요!**

---

## 옵션 2: 자체 호스팅

**최적 대상:** 기술 전문 지식과 특정 요구 사항이 있는 조직

**요구 사항:**
- 경험 있는 시스템 관리자 또는 개발자
- Docker, 데이터베이스 및 웹 서버에 대한 이해
- 시스템 유지 관리 및 업데이트 능력
- 보안 모범 사례에 대한 지식
- 개인정보 보호법에 대한 규정 준수 전문성

### 자체 호스팅 개요

Ministry Mapper는 두 가지 구성 요소로 구성됩니다:

1. **백엔드** - SQLite를 사용하는 PocketBase Go 애플리케이션
2. **프론트엔드** - React 정적 웹 애플리케이션

둘 다 배포하고 연결해야 합니다.

---

## 백엔드 배포 옵션

### 옵션 1: Railway (자체 호스팅에 권장)

**최적 대상:** 최소 구성으로 쉬운 자체 호스팅

**설정 단계:**

1. **Railway 계정 생성**
   ```
   방문: https://railway.app
   GitHub으로 가입
   ```

2. **백엔드 배포**
   ```bash
   # 저장소 포크
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be

   # Railway에 연결
   railway init
   railway link
   ```

3. **환경 구성**
   ```bash
   # Railway 대시보드에서 환경 변수 추가
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **배포**
   ```bash
   railway up
   ```

**기능:**
- 무료 티어 제공
- 자동 HTTPS
- 쉬운 확장
- 내장 모니터링
- 원클릭 배포

---

### 옵션 2: Render

**설정 단계:**

1. **Render 계정 생성**
   ```
   방문: https://render.com
   가입
   ```

2. **새 웹 서비스 생성**
   - GitHub 저장소 연결
   - `ministry-mapper-be` 선택
   - Docker 배포 선택

3. **환경 구성**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```

4. **배포**
   - Render가 자동으로 빌드 및 배포
   - 첫 배포에 5-10분 소요

**기능:**
- 제한이 있는 무료 티어
- 자동 HTTPS
- 쉬운 확장
- GitHub 통합

---

### 옵션 3: DigitalOcean Droplet

**최적 대상:** 완전한 제어, 프로덕션 배포

**설정 단계:**

1. **Droplet 생성**
   ```bash
   # Ubuntu 22.04 LTS 권장
   # 최소: 1GB RAM, 1 CPU, 25GB SSD
   # 권장: 2GB RAM, 2 CPU, 50GB SSD
   ```

2. **Docker 설치**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **저장소 복제**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **환경 구성**
   ```bash
   cp .env.sample .env
   nano .env  # 설정 편집
   ```

5. **빌드 및 실행**
   ```bash
   docker-compose up -d
   ```

6. **Nginx 리버스 프록시 설정**
   ```nginx
   server {
       listen 80;
       server_name api.your-domain.com;

       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

7. **Certbot으로 SSL 설정**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**기능:**
- 완전한 제어
- 예측 가능한 가격 ($6-12/월)
- 높은 성능
- 사용자 정의 구성

---

### 옵션 4: AWS (고급)

**최적 대상:** 엔터프라이즈 배포, 높은 확장성 요구

**아키텍처:**
```
Route 53 (DNS)
    ↓
CloudFront (CDN)
    ↓
Application Load Balancer
    ↓
ECS Fargate (컨테이너)
    ↓
RDS PostgreSQL (SQLite에서 마이그레이션 시)
    ↓
S3 (파일 저장소)
```

**설정 개요:**

1. **ECS 클러스터 생성**
2. **Docker 이미지를 ECR에 빌드 및 푸시**
3. **작업 정의 생성**
4. **로드 밸런서 구성**
5. **CloudFront CDN 설정**
6. **Route 53 DNS 구성**

**비용:** 사용량에 따라 월 ~$50-200

**복잡성:** 높음 - AWS 전문 지식 필요

---

## 프론트엔드 배포 옵션

### 옵션 1: Vercel (권장)

**최적 대상:** 우수한 DX로 빠르고 쉬운 배포

**설정 단계:**

1. **Vercel 계정 생성**
   ```
   방문: https://vercel.com
   GitHub으로 가입
   ```

2. **저장소 가져오기**
   ```
   - "New Project" 클릭
   - ministry-mapper-v2 가져오기
   - 프레임워크: Vite
   - 빌드 명령: npm run build
   - 출력 디렉토리: build
   ```

3. **환경 변수 구성**
   ```bash
   VITE_SYSTEM_ENVIRONMENT=production
   VITE_VERSION=$npm_package_version
   VITE_OPENROUTE_API_KEY=your_key
   VITE_LOCATIONIQ_API_KEY=your_key
   VITE_POCKETBASE_URL=https://your-backend-url.com
   VITE_PRIVACY_URL=https://your-site.com/privacy
   VITE_TERMS_URL=https://your-site.com/terms
   VITE_SENTRY_DSN=your_sentry_dsn
   ```

4. **배포**
   - Vercel이 푸시 시 자동 배포
   - 2-3분 소요

**기능:**
- 개인 프로젝트에 무료
- 자동 HTTPS
- 엣지 네트워크 (전 세계적으로 빠름)
- PR에 대한 미리보기 배포
- 사용자 정의 도메인

---

### 옵션 2: Netlify

**설정 단계:**

1. **Netlify 계정 생성**
2. **저장소 가져오기**
   - GitHub 연결
   - `ministry-mapper-v2` 선택
3. **빌드 설정**
   ```
   빌드 명령: npm run build
   게시 디렉토리: build
   ```
4. **환경 변수 추가**
5. **배포**

**기능:**
- 무료 티어
- 자동 HTTPS
- 폼 처리
- 서버리스 함수

---

### 옵션 3: Cloudflare Pages

**설정 단계:**

1. **Cloudflare 계정 생성**
2. **Pages 프로젝트 생성**
   - GitHub 연결
   - 저장소 선택
3. **빌드 구성**
   ```
   프레임워크: Vite
   빌드 명령: npm run build
   출력 디렉토리: build
   ```
4. **환경 변수**
5. **배포**

**기능:**
- 무제한 대역폭
- 글로벌 CDN
- DDoS 보호
- 웹 분석

---

### 옵션 4: AWS S3 + CloudFront

**최적 대상:** AWS 중심 배포

**설정 단계:**

1. **S3 버킷 생성**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **정적 호스팅 구성**
   ```bash
   aws s3 website s3://ministry-mapper-frontend \
     --index-document index.html \
     --error-document index.html
   ```

3. **빌드 업로드**
   ```bash
   npm run build
   aws s3 sync build/ s3://ministry-mapper-frontend
   ```

4. **CloudFront 배포 생성**
   - 오리진: S3 버킷
   - 뷰어 프로토콜: HTTP를 HTTPS로 리디렉션
   - 가격 클래스: 모든 엣지 위치 사용

5. **사용자 정의 도메인 구성**

**비용:** 소규모 사이트의 경우 월 ~$1-5

---

## 환경 구성

### 백엔드 환경 변수

**필수:**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```

**SMTP (이메일):**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**보안:**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**모니터링:**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
```

**선택적 서비스:**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### 프론트엔드 환경 변수

**필수:**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://your-backend.com
```

**법적 페이지:**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**선택적 API:**
```bash
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
```

**모니터링:**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

---

## SSL/TLS 인증서 설정

### 옵션 1: Let's Encrypt (무료)

**Certbot 사용:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**자동 갱신:**
```bash
sudo certbot renew --dry-run
```

### 옵션 2: Cloudflare SSL

- Cloudflare에 무료 SSL 포함
- 자동 갱신
- Universal SSL

### 옵션 3: 클라우드 제공업체 SSL

대부분의 클라우드 제공업체 (Vercel, Netlify, Railway)에 자동 SSL 포함

---

## 데이터베이스 백업 전략

### 자동 백업

**일일 백업 스크립트:**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_PATH="/app/pb_data/data.db"

# 백업 생성
cp $DB_PATH $BACKUP_DIR/backup_$DATE.db

# 압축
gzip $BACKUP_DIR/backup_$DATE.db

# 최근 30일 유지
find $BACKUP_DIR -name "backup_*.gz" -mtime +30 -delete
```

**Cron 일정:**
```bash
0 2 * * * /path/to/backup-script.sh
```

### 클라우드 백업

**AWS S3:**
```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

**Render:**
- 자동 백업 포함
- 일일 스냅샷
- 7일 보존

---

## 모니터링 및 로깅

### Sentry 설정

1. [sentry.io](https://sentry.io)에서 계정 생성
2. 새 프로젝트 생성 (React + Go)
3. DSN 복사
4. 환경 변수에 추가
5. 알림 구성

**이점:**
- 실시간 오류 추적
- 성능 모니터링
- 릴리스 추적
- 사용자 피드백

### 애플리케이션 모니터링

**추적할 메트릭:**
- 오류율
- 응답 시간
- 데이터베이스 크기
- 활성 사용자
- API 호출
- 메모리 사용량
- 디스크 공간

**도구:**
- Grafana + Prometheus
- Datadog
- New Relic
- 클라우드 제공업체 모니터링

---

## 보안 모범 사례

### 백엔드 보안

1. **강력한 비밀번호 사용**
   - 최소 12자
   - 문자, 숫자, 기호 혼합

2. **속도 제한 활성화**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **CORS 구성**
   ```bash
   PB_ALLOW_ORIGINS=https://your-frontend-url.com
   ```

4. **정기 업데이트**
   ```bash
   # 월별 업데이트 확인
   docker pull your-image:latest
   ```

5. **방화벽 규칙**
   ```bash
   # 포트 80, 443, 22 (SSH)만 허용
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### 프론트엔드 보안

1. **환경 변수**
   - `.env` 파일 절대 커밋 안 함
   - 플랫폼 환경 변수 UI 사용

2. **API 키 제한**
   - 특정 API로 API 키 제한

3. **Content Security Policy**
   ```html
   <meta http-equiv="Content-Security-Policy"
         content="default-src 'self';
                  script-src 'self' 'unsafe-inline';
                  style-src 'self' 'unsafe-inline';">
   ```

4. **HTTPS만 사용**
   - 프로덕션에서 HTTPS 적용
   - HSTS 헤더 설정

---

## 성능 최적화

### 백엔드 최적화

1. **Gzip 압축 활성화**
2. **데이터베이스 인덱싱** (이미 구성됨)
3. **연결 풀링**
4. **캐싱 헤더**

### 프론트엔드 최적화

1. **코드 분할** (Vite로 자동)
2. **이미지 최적화**
3. **정적 자산용 CDN**
4. **서비스 워커 캐싱**

**Lighthouse 점수 목표:**
- 성능: >90
- 접근성: >95
- 모범 사례: >95
- SEO: >90

---

## 확장 고려 사항

### 현재 제한 사항

- SQLite 단일 쓰기자
- 1000명 미만의 동시 사용자에 적합
- 소규모에서 중규모 회중에 적합

### 확장 옵션

**수직 확장:**
- 서버 리소스 증가
- 더 많은 RAM, CPU, 저장소

**수평 확장:**
- PocketBase 읽기 복제본
- 프론트엔드용 CDN
- 로드 밸런서

**데이터베이스 마이그레이션:**
- 더 큰 규모를 위해 PostgreSQL로 마이그레이션
- PocketBase는 PostgreSQL 지원

---

## 배포 문제 해결

### 백엔드 문제

**문제:** 데이터베이스 잠김
**해결책:** 하나의 인스턴스만 실행되는지 확인

**문제:** 이메일이 전송되지 않음
**해결책:** SMTP 자격 증명 및 포트 확인

**문제:** 높은 메모리 사용량
**해결책:** 서버 RAM 증가 또는 쿼리 최적화

### 프론트엔드 문제

**문제:** 빌드 실패
**해결책:** Node.js 버전 확인 (22+ 필요)

**문제:** CORS 오류
**해결책:** 백엔드에서 `PB_ALLOW_ORIGINS` 구성

---

## 비용 추정

### 호스팅 서비스
- 가격은 ministry-mapper.com에 문의
- 지원, 업데이트, 백업 포함

### 자체 호스팅 비용

**최소 설정:**
- Railway/Render: $0-5/월 (무료 티어)
- Vercel/Netlify: $0/월 (무료 티어)
- **총계: $0-5/월**

**권장 설정:**
- 백엔드 (Railway): $5/월
- 프론트엔드 (Vercel): $0/월
- Sentry: $0/월 (무료 티어)
- **총계: $5/월**

**프로덕션 설정:**
- DigitalOcean Droplet: $12/월
- CloudFront CDN: $2/월
- Sentry Pro: $26/월
- **총계: $40/월**

**엔터프라이즈 설정:**
- AWS 인프라: $100-500/월
- 지원 계약: 가변
- **총계: $100-500+/월**

**시간 투자:**
- 초기 설정: 4-8시간
- 월간 유지 관리: 2-4시간
- 긴급 지원: 필요에 따라

---

## 다음 단계

### 배포 후

1. **철저히 테스트**
   - 테스트 계정 생성
   - 샘플 구역 생성
   - 모든 기능 테스트
   - 이메일 작동 확인

2. **사용자 교육**
   - 문서 생성
   - 관리자 교육
   - 사용자 세션 개최

3. **시스템 모니터링**
   - Sentry 매일 확인
   - 로그 주간 검토
   - 월간 업데이트

4. **백업 확인**
   - 복원 프로세스 테스트
   - 백업 무결성 확인
   - 절차 문서화

---

## 지원 리소스

### 문서
- [아키텍처 개요](architecture.md)
- [백엔드 설정](backend-setup.md)
- [프론트엔드 설정](frontend-setup.md)

### 커뮤니티
- GitHub Issues
- 문서 사이트
- 커뮤니티 포럼

### 전문 지원
- 호스팅 서비스 지원
- 컨설팅 가능
- 사용자 정의 개발
