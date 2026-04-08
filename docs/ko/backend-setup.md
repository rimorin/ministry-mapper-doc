# 백엔드 설정 가이드

!!! warning "셀프 호스팅 권장하지 않음"
    이 가이드는 Ministry Mapper를 셀프 호스팅하려는 고급 사용자를 위한 것입니다. 기술적 복잡성, 유지 관리 부담 및 보안 책임으로 인해 셀프 호스팅을 권장하지 않습니다.
    
    **대부분의 사용자는**: **[ministry-mapper.com](https://ministry-mapper.com)**에서 저희 호스팅 서비스를 사용하세요.

!!! info "사용자 문서를 찾고 계십니까?"
    Ministry Mapper 사용 방법을 배우려는 일반 사용자라면 대신 [시작하기](getting-started.md) 또는 [사용자 가이드](user-guide.md)를 참조하세요.

## 개요

Ministry Mapper 백엔드(ministry-mapper-be)는 Go와 PocketBase로 구축되었습니다. 다음을 제공합니다:

- 데이터 저장 및 관리 (SQLite 데이터베이스)
- 사용자 인증 및 권한 부여
- 실시간 데이터 동기화
- 구역 관리를 위한 커스텀 API 엔드포인트
- 자동화된 작업을 위한 백그라운드 작업
- SMTP를 통한 이메일 알림

**이 가이드는 귀하가 다음을 갖추었다고 가정합니다**:
- 서버 관리 경험
- Docker 및 컨테이너화 이해
- 환경 변수에 대한 친숙함
- 보안 모범 사례 지식

완전한 셀프 호스팅 지침은 [셀프 호스팅 가이드](self-hosting.md)를 참조하세요.

## 빠른 참조

이 페이지는 백엔드 구성에 대한 빠른 참조를 제공합니다. **완전한 설정 지침은 [셀프 호스팅 가이드](self-hosting.md)를 참조하세요.**

### 기술 스택

- **Go**: 빠르고 효율적인 프로그래밍 언어
- **PocketBase**: 내장 관리 패널이 있는 서비스로서의 백엔드
- **SQLite**: `pb_data` 폴더의 경량 데이터베이스
- **Docker**: Alpine Linux 기반 컨테이너

### 주요 기능

- 데이터 저장 및 관리
- 사용자 인증
- 실시간 구독 (서버 전송 이벤트)
- 커스텀 API 엔드포인트
- 예약된 백그라운드 작업
- 이메일 알림 (SMTP)
- 오류 추적 (Sentry)
- 기능 플래그 (LaunchDarkly)

---

!!! note "완전한 설정 지침"
    아래 섹션은 백엔드 구성에 대한 기술적 참조를 제공합니다. 컨텍스트 및 설명이 포함된 단계별 설정 지침은 **[셀프 호스팅 가이드](self-hosting.md)**를 참조하세요.

---

## 환경 변수 설명

### 서비스 통합 키 (선택)

```bash
# MailerSend API 키 - MailerSend 서비스를 통해 이메일 전송
# 대신 SMTP를 사용하는 경우 비워 두세요
MAILERSEND_API_KEY=

# MailerSend 발신자를 위한 이메일 주소
MAILERSEND_FROM_EMAIL=

# LaunchDarkly SDK 키 - 백그라운드 작업 제어를 위한 기능 플래그
# 구성되지 않은 경우 기본적으로 작업 실행
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# OpenAI API 키 - 월간 보고서 및 메시지 다이제스트에서 AI 생성 요약
# 선택: 이것 없이도 보고서 및 다이제스트가 잘 작동; AI 섹션이 단순히 생략됨
OPENAI_API_KEY=
```

### 애플리케이션 설정

```bash
# 이메일 및 관리 패널에 표시되는 이름
PB_APP_NAME=Ministry Mapper

# 프론트엔드가 호스팅되는 URL
PB_APP_URL=http://localhost:3000
```

### 기본 관리자 계정

```bash
# 최초 설정 중 생성되는 초기 관리자 계정
# 첫 로그인 후 즉시 비밀번호 변경!
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### 이메일 구성 (SMTP)

```bash
# SMTP 서버 주소 (예: smtp.gmail.com, smtp.sendgrid.net)
PB_SMTP_HOST=

# SMTP 비밀번호 (Gmail의 경우 일반 비밀번호가 아닌 앱 비밀번호 사용)
PB_SMTP_PASSWORD=

# SMTP 사용자 이름 (보통 이메일 주소)
PB_SMTP_USERNAME=

# SMTP 포트 (TLS의 경우 587, SSL의 경우 465)
PB_SMTP_PORT=587

# "보내는 사람" 필드에 표시되는 이메일 주소
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# "보내는 사람" 필드에 표시되는 이름
PB_SMTP_SENDER_NAME=MM Support
```

### 보안 및 접근 제어

```bash
# CORS에 허용된 도메인 목록 (쉼표로 구분)
# 개발에는 * 사용, 프로덕션에는 특정 도메인 사용
PB_ALLOW_ORIGINS=*

# 공개 접근으로부터 관리 UI 숨기기 (true/false)
# 보안을 위해 프로덕션에서는 true로 설정
PB_HIDE_CONTROLS=false
```

### 인증 설정

```bash
# 일회용 비밀번호 로그인 활성화 (true/false)
PB_OTP_ENABLED=false

# 다중 인증 활성화 (true/false)
PB_MFA_ENABLED=false
```

### 속도 제한

```bash
# API 남용 방지를 위한 속도 제한 활성화 (true/false)
PB_ENABLE_RATE_LIMITING=false
```

### 오류 추적

```bash
# 오류 모니터링 및 추적을 위한 Sentry DSN
SENTRY_DSN=

# Sentry를 위한 환경 이름 (development, staging, production)
SENTRY_ENV=production

# Git 커밋 해시 - Coolify 같은 호스팅 플랫폼에서 자동으로 설정
SOURCE_COMMIT=
```

## 설치 단계

### 옵션 1: Docker 배포 (권장)

Docker는 플랫폼 전반에서 배포를 간단하고 일관되게 만듭니다.

#### 1. Docker 이미지 빌드

```bash
docker build -t ministry-mapper-backend .
```

#### 2. 컨테이너 실행

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**중요 사항:**

- 컨테이너는 포트 **8080**에서 실행됩니다 (8090이 아님)
- `-p 8080:8080`은 컨테이너의 포트 8080을 호스트 포트 8080에 매핑
- `-v /path/to/pb_data:/app/pb_data`는 데이터베이스를 영구적으로 저장 (실제 시스템 경로로 교체)
- `--env-file .env`는 `.env` 파일에서 환경 변수 로드
- `-d`는 백그라운드에서 컨테이너 실행 (분리 모드)

### 옵션 2: 로컬 개발

컴퓨터에서 개발 및 테스트하는 경우:

#### 1. Go 종속성 설치

```bash
./scripts/install.sh
```

#### 2. 환경 변수 설정

```bash
cp .env.sample .env
# 설정으로 .env 편집
```

#### 3. 애플리케이션 실행

```bash
./scripts/start.sh
```

백엔드는 기본적으로 **http://localhost:8090**에서 시작됩니다 (PocketBase 기본 포트).

## 관리 패널 접근

백엔드가 실행되면:

1. `http://your-backend-url/_/`로 이동합니다 (밑줄과 후행 슬래시 참고)
2. `PB_ADMIN_EMAIL` 및 `PB_ADMIN_PASSWORD`의 관리자 자격 증명으로 로그인
3. PocketBase 관리 대시보드가 표시됩니다

**보안 참고**: 관리 UI를 공개 접근으로부터 숨기려면 프로덕션에서 `PB_HIDE_CONTROLS=true`를 설정하세요.

## 데이터베이스 관리

### 데이터 백업

데이터는 `pb_data` 폴더에 있습니다. 정기적인 백업이 중요합니다.

#### 수동 백업

```bash
# 전체 pb_data 폴더의 백업 생성
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/
```

#### 자동 일일 백업

```bash
# crontab에 추가 (실행: crontab -e)
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

#### 백업에서 복원

```bash
# 먼저 애플리케이션 중지
# 그런 다음 백업 압축 해제
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /path/to/
```

## 보안 체크리스트

프로덕션으로 전환하기 전:

- [ ] 첫 로그인 후 즉시 기본 관리자 비밀번호 변경
- [ ] `PB_ALLOW_ORIGINS`를 실제 도메인으로 설정 (`*`이 아님)
- [ ] HTTPS 활성화 (프로덕션에서 HTTP 사용 금지)
- [ ] 관리자 UI 숨기기 위해 `PB_HIDE_CONTROLS=true` 설정
- [ ] `PB_ENABLE_RATE_LIMITING=true`로 속도 제한 활성화 고려
- [ ] 오류 모니터링을 위해 Sentry 구성
- [ ] 자동화된 일일 백업 설정
- [ ] 모든 계정에 강력하고 고유한 비밀번호 사용
- [ ] Gmail SMTP의 경우 앱 비밀번호 사용
- [ ] Go 종속성 정기적으로 업데이트

## 백엔드 업데이트

### Docker 업데이트

```bash
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

### 로컬 개발 업데이트

```bash
git pull origin main
./scripts/update.sh
./scripts/start.sh
```

## 모니터링 및 로그

### Docker 로그

```bash
docker logs ministry-mapper
docker logs -f ministry-mapper
docker logs --tail 100 ministry-mapper
```

## 문제 해결

### 포트가 이미 사용 중

```bash
lsof -i :8080
lsof -i :8090
# 포트를 사용하는 프로세스 PID를 확인한 후 종료하세요
```

### 데이터베이스 잠금

- 데이터베이스에 접근하는 모든 컨테이너/프로세스 중지
- 앱의 인스턴스가 하나만 실행 중인지 확인
- SQLite는 여러 프로세스의 동시 쓰기를 지원하지 않음
- 애플리케이션 재시작

### 이메일이 전송되지 않음

1. `.env`의 모든 `PB_SMTP_*` 변수 확인
2. **Gmail 사용자**: 앱 비밀번호 사용
3. **스팸 폴더 확인**
4. **로그 검토**: `pb_data/logs/` 또는 Docker 로그

### 관리 패널에 접근할 수 없음

- URL 형식 확인: `http://your-url/_/` (밑줄과 후행 슬래시 포함)
- `PB_HIDE_CONTROLS=true`인지 확인
- 브라우저 캐시 지우기

## API 엔드포인트

### 지도 관리
- `POST /map/codes` - 특정 지도의 모든 지도 코드 가져오기
- `POST /map/code/add` - 지도에 새 주소 코드 추가
- `POST /map/codes/update` - 지도 코드의 순서 업데이트
- `POST /map/code/delete` - 지도에서 주소 코드 삭제
- `POST /map/add` - 새 지도 생성
- `POST /map/territory/update` - 지도가 속한 구역 업데이트
- `POST /map/reset` - 지도 재설정

### 층 관리
- `POST /map/floor/add` - 지도에 층 추가
- `POST /map/floor/remove` - 지도에서 층 제거

### 구역 관리
- `POST /territory/reset` - 구역 재설정
- `POST /territory/link` - 구역에 대한 빠른 접근 링크 생성

### 구성
- `POST /options/update` - 회중 옵션/설정 업데이트

## 백그라운드 작업 (Cron 스케줄러)

1. **구역 집계** (`*/10 * * * *`) - 기능 플래그: `enable-territory-aggregations`
2. **배정 정리** (`*/5 * * * *`) - 기능 플래그: `enable-assignments-cleanup`
3. **메시지 처리** (`*/30 * * * *`) - 기능 플래그: `enable-message-processing`
4. **지시사항 처리** (`*/30 * * * *`) - 기능 플래그: `enable-instruction-processing`
5. **메모 처리** (`0 * * * *`) - 기능 플래그: `enable-note-processing`
6. **월간 보고서** (`0 0 1 * *`) - 기능 플래그: `enable-monthly-report`
7. **미프로비저닝 사용자 처리** (`0 1 * * *`) - 기능 플래그: `enable-unprovisioned-user-processing`
8. **비활성 사용자 처리** (`30 1 * * *`) - 기능 플래그: `enable-inactive-user-processing`

**참고**: LaunchDarkly가 구성되지 않은 경우, 모든 작업은 기본적으로 실행됩니다.

## 데이터베이스 훅

- **주소 업데이트**: 메모가 변경될 때 타임스탬프 및 사용자 정보 기록
- **주소 업데이트**: 지도 집계 재계산 트리거
- **사용자 인증**: 마지막 로그인 타임스탬프 업데이트
- **사용자 생성**: 이메일 주소 정규화 및 소문자 변환

## 다음 단계

- [프론트엔드 애플리케이션](frontend-setup.md) 설정
- [사용자 가이드](user-guide.md) 알아보기
