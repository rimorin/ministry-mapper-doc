# 자주 묻는 질문 (FAQ)

## 일반 질문

### Ministry Mapper란 무엇입니까?

Ministry Mapper는 회중이 전도 구역을 디지털 방식으로 관리하도록 돕는 현대적인 웹 애플리케이션입니다. 종이 구역 쪽지 대신 인터넷에 연결된 모든 기기에서 디지털로 모든 것을 구성하고 추적할 수 있습니다. 프론트엔드에 React, 백엔드에 PocketBase, 인터랙티브 지도에 Leaflet을 사용합니다.

### Ministry Mapper에 어떻게 접근합니까?

**권장**: 저희 호스팅 서비스 **[ministry-mapper.com](https://ministry-mapper.com)**을 사용하세요.

간단히:
1. [ministry-mapper.com](https://ministry-mapper.com) 방문
2. 가입 페이지를 사용하여 계정 만들기
3. 이메일 주소 인증
4. 적절한 권한을 위해 회중 관리자에게 연락

**대안**: 셀프 호스팅(권장하지 않음) - 자세한 내용은 [셀프 호스팅 가이드](self-hosting.md) 참조. 셀프 호스팅에는 상당한 기술적 전문 지식과 지속적인 유지 관리가 필요합니다.

### Ministry Mapper는 무료입니까?

**호스팅 서비스**: 요금 및 요금제는 [ministry-mapper.com](https://ministry-mapper.com)을 방문하세요.

**셀프 호스팅**: 소스 코드는 무료 오픈 소스이지만, 다음을 직접 제공해야 합니다:
- 프론트엔드 호스팅 (예: Vercel, Netlify, AWS)
- PocketBase 백엔드 배포
- 선택: 오류 모니터링을 위한 Sentry 계정

참고: 셀프 호스팅 비용(서버, API 요금, 유지 관리 시간)이 종종 호스팅 서비스 요금을 초과합니다.

### Ministry Mapper는 어떤 언어를 지원합니까?

현재 지원되는 언어:

- 영어 (English)
- 일본어 (日本語)
- 한국어
- 중국어 (中文)
- 인도네시아어 (Bahasa Indonesia)
- 말레이어 (Bahasa Melayu)

인터페이스가 브라우저 언어를 자동으로 감지합니다. 앱에서 수동으로 언어를 변경할 수도 있습니다.

### Ministry Mapper를 사용하려면 기술적 지식이 필요합니까?

**전도인/사용자**: 기술적 지식이 필요 없습니다! 스마트폰이나 컴퓨터를 사용할 수 있다면 [ministry-mapper.com](https://ministry-mapper.com)의 호스팅 서비스를 통해 Ministry Mapper를 사용할 수 있습니다.

**셀프 호스팅 관리자**: 네, 상당한 기술적 지식이 필요합니다:

- Linux 서버 관리 경험
- Docker 및 컨테이너화 이해
- 환경 변수 및 구성에 대한 지식
- SSL/TLS 인증서에 대한 친숙함
- 웹 서버 구성(Nginx/Apache)
- DNS 및 도메인 구성

**셀프 호스팅 대신 호스팅 서비스 사용을 강력히 권장합니다.** 여전히 셀프 호스팅을 원한다면 [셀프 호스팅 가이드](self-hosting.md)를 참조하세요.

### Ministry Mapper는 오프라인으로 작동합니까?

아니요, 앱이 제대로 기능하려면 인터넷 연결이 필요합니다. 이유:

- PocketBase 백엔드에 연결해야 합니다
- 사용자 간 실시간 업데이트에는 연결이 필요합니다

WiFi를 사용할 수 없는 경우에도 앱은 모바일 데이터에서 잘 작동합니다.

## 개인정보 및 법적 사항

### 어떤 개인정보 보호법을 고려해야 합니까?

**중요**: Ministry Mapper는 주거 주소를 추적하므로 데이터 개인정보 보호법의 적용을 받을 수 있습니다. 이러한 법률은 국가와 지역에 따라 크게 다릅니다:

- **유럽**: GDPR (일반 데이터 보호 규정)
- **캘리포니아**: CCPA (캘리포니아 소비자 개인정보 보호법)
- **브라질**: LGPD (Lei Geral de Proteção de Dados)
- **기타 여러 나라**: 다양한 지역 규정

**Ministry Mapper를 사용하기 전에 지역 규정을 철저히 검토하고 준수를 확인하십시오.** 귀하의 지역에 맞는 법적 조언을 위해 법률 전문가와 상담하십시오.

**호스팅 서비스**: [ministry-mapper.com](https://ministry-mapper.com)의 호스팅 서비스가 규정 준수 지원을 제공할 수 있지만, 귀하는 여전히 현지 법률을 준수할 책임이 있습니다.

**셀프 호스팅**: 모든 개인정보 보호법 준수에 대한 전적인 책임이 귀하에게 있습니다.

### Ministry Mapper는 어떤 데이터를 저장합니까?

**사용자 데이터 (PocketBase로 관리):**

- 이메일 주소
- 이름
- 비밀번호 (암호화됨)
- 사용자 역할 및 권한

**구역 데이터:**

- 구역 경계 및 이름
- 주소
- 상태 정보
- 세대에 대한 메모
- 배정 이력
- 업데이트 타임스탬프

**기술 데이터 (Sentry가 활성화된 경우):**

- 오류 로그
- 성능 지표

모든 데이터 저장은 보안 모범 사례를 준수합니다. 호스팅 서비스 데이터 정책은 [ministry-mapper.com](https://ministry-mapper.com)을 확인하십시오.

## 기술 설정 (셀프 호스팅)

!!! warning "셀프 호스팅 권장하지 않음"
    다음 섹션은 Ministry Mapper를 셀프 호스팅하려는 고급 사용자를 위한 것입니다. **대신 [ministry-mapper.com](https://ministry-mapper.com)의 호스팅 서비스를 사용할 것을 강력히 권장합니다.** 셀프 호스팅에는 상당한 기술적 전문 지식, 지속적인 유지 관리 및 보안 책임이 필요합니다.
    
    완전한 셀프 호스팅 지침은 [셀프 호스팅 가이드](self-hosting.md)를 참조하십시오.

### 시스템 요건은 무엇입니까?

**호스팅 서비스 사용 시:**
- 최신 웹 브라우저 (Chrome, Firefox, Safari, Edge)
- 인터넷 연결
- 이메일 주소
- 모바일 기기 또는 컴퓨터

**셀프 호스팅 개발 환경:**

- Node.js >= 22.0.0
- npm 패키지 관리자
- Git
- Docker (백엔드용)

**셀프 호스팅 프로덕션 환경:**

- 클라우드 호스팅 서비스 (Railway, Render, DigitalOcean, AWS 등)
- 도메인 이름
- SSL/TLS 인증서
- PocketBase 백엔드 (별도 배포)
- (선택) 모니터링을 위한 Sentry 계정
- (선택) 알림을 위한 이메일 서비스

### 필요한 환경 변수는 무엇입니까?

**셀프 호스팅 전용** - 이 섹션은 셀프 호스팅을 하는 경우에만 해당됩니다. 호스팅 서비스는 모든 구성을 자동으로 처리합니다.

**프론트엔드 (.env 파일):**

```
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=your_pocketbase_backend_url
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
VITE_SENTRY_DSN=your_sentry_dsn (선택)
VITE_PRIVACY_URL=your_privacy_policy_url
VITE_TERMS_URL=your_terms_url
VITE_ABOUT_URL=your_about_page_url
```

**프로덕션 환경:** 위와 동일하지만 `VITE_SYSTEM_ENVIRONMENT=production` 사용

자세한 내용은 [프론트엔드 설정 가이드](frontend-setup.md)를 참조하십시오.

### 프론트엔드를 어떻게 배포합니까?

**셀프 호스팅 전용** - 호스팅 서비스를 사용하는 경우 건너뛰세요.

1. **애플리케이션 빌드:**

   ```bash
   npm run build
   ```

2. 위에 지정된 대로 **환경 변수 구성**

3. **`build/` 폴더를 호스팅 제공업체에 배포:**

   - Vercel: GitHub 저장소 연결 및 환경 변수 구성
   - Netlify: 빌드 폴더 끌어다 놓기 또는 Git으로 연결
   - AWS S3: 빌드 폴더 업로드 및 정적 웹사이트 호스팅 구성

4. **PocketBase 백엔드가 배포되었고** `VITE_POCKETBASE_URL`에 지정된 URL에서 접근 가능한지 확인

자세한 지침은 [프론트엔드 설정 가이드](frontend-setup.md) 및 [셀프 호스팅 가이드](self-hosting.md)를 참조하십시오.

### PocketBase 백엔드를 어떻게 설정합니까?

**셀프 호스팅 전용** - 호스팅 서비스를 사용하는 경우 건너뛰세요.

백엔드는 별도의 저장소에서 관리됩니다: [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)

**빠른 개요:**

1. 백엔드 저장소 클론
2. 환경 변수 구성 ([백엔드 설정 가이드](backend-setup.md) 참조)
3. Docker 또는 Railway/Render를 사용하여 배포
4. 스키마로 데이터베이스 초기화
5. 이메일 알림을 위한 SMTP 구성 (선택)

**자세한 지침:** [백엔드 설정 가이드](backend-setup.md) 및 [셀프 호스팅 가이드](self-hosting.md) 참조.

**중요**: 백엔드가 프론트엔드를 배포하기 전에 배포되고 접근 가능해야 합니다.

### Sentry 모니터링을 어떻게 설정합니까?

**셀프 호스팅 전용** - 호스팅 서비스를 사용하는 경우 건너뛰세요.

1. [Sentry](https://sentry.io/) 계정 만들기
2. React 프로젝트 만들기
3. 설정으로 이동하여 DSN 키 가져오기
4. 다음 환경 변수 구성:
   - `VITE_SENTRY_DSN`: Sentry 프로젝트 DSN
   - `VITE_SYSTEM_ENVIRONMENT`: 프로덕션의 경우 "production"으로 설정 (추적 샘플 속도에 영향)
   - `VITE_VERSION`: Sentry의 릴리스 추적에 사용

참고: Sentry는 선택 사항이지만 프로덕션 배포에 권장됩니다.

## 개발

### 앱을 로컬에서 어떻게 실행합니까?

**개발자 전용** - 이 섹션은 Ministry Mapper에 기여하는 개발자를 위한 것입니다.

1. **저장소 클론:**

   ```bash
   git clone https://github.com/rimorin/ministry-mapper-v2.git
   cd ministry-mapper-v2
   ```

2. **종속성 설치:**

   ```bash
   npm install
   ```

3. 필요한 환경 변수로 **.env 파일 구성** (`.env.example` 참조)

4. **개발 서버 시작:**
   ```bash
   npm start
   ```

앱은 `http://localhost:3000`에서 실행됩니다.

참고: 앱이 기능하려면 실행 중인 PocketBase 백엔드가 필요합니다. [백엔드 설정 가이드](backend-setup.md)를 참조하세요.

### 테스트를 어떻게 실행합니까?

```bash
npm test
```

테스트는 Vitest와 Testing Library를 사용하여 작성되었습니다. 테스트 파일은 `.test.ts` 확장자로 소스 파일 옆에 위치합니다.

### 코드를 어떻게 포맷합니까?

**포맷 확인:**

```bash
npm run prettier
```

**포맷 수정 적용:**

```bash
npm run prettier:fix
```

프로젝트는 코드 포맷팅에 Prettier를 사용하며 Husky를 통한 사전 커밋 훅이 구성되어 있습니다.

### 기술 스택은 무엇입니까?

- **프론트엔드 프레임워크**: React 19 with TypeScript
- **빌드 도구**: Vite
- **UI 라이브러리**: React Bootstrap (Bootstrap 5)
- **지도**: Leaflet with OpenStreetMap
- **상태 관리**: React Context + 커스텀 훅
- **라우팅**: Wouter
- **스타일링**: SCSS
- **백엔드**: PocketBase (별도 저장소)
- **모니터링**: Sentry
- **테스팅**: Vitest + Testing Library

자세한 아키텍처 정보는 CLAUDE.md를 참조하세요.

## 문제 해결

### 앱에서 "백엔드에 연결할 수 없습니다" 오류가 표시됩니다

**호스팅 서비스 사용자**: 이 오류가 표시되면 서비스에 문제가 있을 수 있습니다. 상태 페이지를 확인하거나 지원팀에 문의하세요.

**셀프 호스팅 사용자**:

**가능한 원인:**

1. PocketBase 백엔드가 실행되지 않거나 접근 불가
2. `VITE_POCKETBASE_URL` 환경 변수가 잘못됨
3. CORS 문제 (백엔드가 프론트엔드 도메인을 허용하지 않음)

**해결책:**

- 백엔드가 실행 중이고 접근 가능한지 확인
- 환경 변수의 URL 확인
- PocketBase에서 CORS 설정을 구성하여 프론트엔드 도메인 허용

### 재빌드 후 변경 사항이 반영되지 않습니다

**셀프 호스팅 전용**:

**가능한 원인:**

1. 브라우저 캐시
2. CDN 캐시 (사용하는 경우)
3. 빌드 아티팩트가 올바르게 배포되지 않음

**해결책:**

- 브라우저 캐시를 지우거나 시크릿 모드 사용
- 해당되는 경우 CDN 캐시 무효화
- 빌드 폴더가 호스팅 서비스에서 완전히 교체되었는지 확인
- 오류를 위해 브라우저 콘솔 확인

### 사용자 인증이 작동하지 않습니다

**호스팅 서비스 사용자**: 인증 문제가 있는 경우 지원팀에 문의하세요.

**셀프 호스팅 사용자**:

**가능한 원인:**

1. PocketBase 백엔드 사용자 관리가 구성되지 않음
2. 네트워크 연결 문제
3. 잘못된 PocketBase URL
4. CORS 구성 문제

**해결책:**

- PocketBase 백엔드가 올바르게 구성되었는지 확인
- 오류 메시지를 위해 브라우저 콘솔 확인
- 백엔드 API 엔드포인트 직접 테스트
- PocketBase에서 CORS 설정 확인

### 앱이 느리거나 응답이 없습니다

**가능한 원인:**

1. 대용량 구역 데이터셋
2. 네트워크 대기 시간
3. 서버 리소스 부족 (셀프 호스팅)
4. 브라우저 성능 문제

**해결책:**

- 인터넷 연결 확인
- 다른 브라우저 사용해보기
- 셀프 호스팅: 구역 데이터 최적화
- 셀프 호스팅: 더 가까운 호스팅 지역 사용
- 셀프 호스팅: 필요한 경우 서버 리소스 업그레이드
- 브라우저 캐시 및 데이터 지우기
- 성능 경고를 위해 브라우저 콘솔 확인

## 기여

### 버그를 어떻게 보고합니까?

1. [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)에서 이미 보고된 버그인지 확인
2. 없다면 다음을 포함하여 새 이슈 생성:
   - 문제에 대한 명확한 설명
   - 재현 단계
   - 예상 vs 실제 동작
   - 브라우저 및 기기 정보
   - 스크린샷 또는 오류 메시지
   - 환경 구성 (민감한 데이터 제외)

### 기능을 요청할 수 있습니까?

네! GitHub에 다음을 포함하여 기능 요청을 생성하세요:

- 기능에 대한 명확한 설명
- 사용 사례 및 이점
- 작동 방식
- 혜택을 받을 사람

참고: 이것은 자원봉사자가 유지 관리하는 프로젝트이므로, 구현은 가용한 시간과 리소스에 따라 달라집니다.

### 코드를 기여할 수 있습니까?

물론입니다! 기여를 환영합니다:

**기여 방법:**

1. 저장소 포크
2. 기능 브랜치 생성
3. 기존 코드 스타일을 따르면서 변경
4. 새 기능에 대한 테스트 작성
5. `npm test` 및 `npm run prettier`를 실행하여 품질 확인
6. 명확한 설명과 함께 풀 리퀘스트 제출

**기여 지침:**

- TypeScript 및 React 모범 사례 따르기
- 코드베이스의 기존 패턴 사용
- 필요한 경우 문서 업데이트
- 변경 사항을 집중적이고 최소화하기
- 제출 전에 철저히 테스트

아키텍처 세부 정보 및 코딩 규칙은 CLAUDE.md를 참조하세요.

### 앱을 새 언어로 번역할 수 있습니까?

네! 앱은 번역에 국제화(i18n)를 사용합니다:

1. 기존 번역은 [프론트엔드 저장소](https://github.com/rimorin/ministry-mapper-v2)의 `src/i18n/` 디렉토리 확인
2. 기존 구조를 따르는 새 언어 파일 생성
3. 번역 추가
4. 언어 선택기 컴포넌트 업데이트
5. 풀 리퀘스트 제출

### 새 버전으로 어떻게 업그레이드합니까?

**호스팅 서비스 사용자**: 업데이트가 자동으로 이루어집니다. 아무것도 할 필요가 없습니다!

**셀프 호스팅 사용자**:

**프론트엔드:**

```bash
git pull origin main
npm install
npm run build
# 새 빌드를 호스팅 서비스에 배포
```

**백엔드:**
백엔드 업그레이드 지침은 [Ministry Mapper BE 저장소](https://github.com/rimorin/ministry-mapper-be)를 참조하세요.

**중요:**

- 항상 먼저 데이터 백업
- 주요 변경 사항을 위해 CHANGELOG.md 읽기
- 가능하면 스테이징 환경에서 테스트
- 필요한 경우 환경 변수 업데이트

## 지원

### 어디서 도움을 받을 수 있습니까?

**호스팅 서비스 사용자**: [ministry-mapper.com](https://ministry-mapper.com) 웹사이트를 통해 지원팀에 연락하세요.

**모든 사용자**:

1. **문서**: 이 문서 사이트 확인, [시작하기](getting-started.md), [사용자 가이드](user-guide.md) 및 기타 가이드 포함
2. **GitHub Issues**: [ministry-mapper-v2 저장소](https://github.com/rimorin/ministry-mapper-v2/issues)에서 기존 이슈 검색하거나 새 이슈 생성
3. **GitHub Discussions**: 질문하고 아이디어 공유
4. **코드 검토**: 구현 세부 사항을 위해 코드와 주석 읽기 (개발자)

### 상업적 지원이 있습니까?

**호스팅 서비스**: [ministry-mapper.com](https://ministry-mapper.com)을 통해 전문 지원이 제공됩니다.

**셀프 호스팅**: 셀프 호스팅 인스턴스에 대한 공식 상업적 지원이 없습니다. 이것은 자원봉사자가 유지 관리하는 오픈 소스 프로젝트입니다. 그러나:

- GitHub를 통한 커뮤니티 지원
- 포괄적인 문서
- 커스터마이징을 위해 독립 개발자를 고용할 수 있습니다

### 앱이 얼마나 자주 업데이트됩니까?

**호스팅 서비스**: 업데이트가 제공되면 자동으로 적용됩니다.

**셀프 호스팅**: 업데이트는 자원봉사자 가용성에 따라 달라집니다. 확인:

- **CHANGELOG.md** - 버전 이력
- **GitHub Releases** - 릴리스 노트
- **커밋 이력** - 최근 변경 사항

### 내 필요에 맞게 Ministry Mapper를 커스터마이징할 수 있습니까?

네! 오픈 소스이므로 다음이 가능합니다:

- UI 수정 (색상, 레이아웃, 브랜딩)
- 커스텀 기능 추가
- 워크플로우 변경
- 다른 시스템과 통합

**요건:**

- React 19, TypeScript 및 PocketBase 지식
- 코드베이스 이해 (저장소의 CLAUDE.md 참조)
- 변경 사항의 적절한 테스트
- 프로젝트에 개선 사항 기여 고려

**참고**: 커스터마이징에는 셀프 호스팅이 필요합니다. 호스팅 서비스는 표준 버전을 사용합니다.

---

## 아직 질문이 있으신가요?

1. **문서 읽기**: 
   - [시작하기 가이드](getting-started.md)
   - [사용자 가이드](user-guide.md)
   - [셀프 호스팅 가이드](self-hosting.md) (셀프 호스팅의 경우)
   - [백엔드 설정 가이드](backend-setup.md) (셀프 호스팅의 경우)
   - [프론트엔드 설정 가이드](frontend-setup.md) (셀프 호스팅의 경우)
2. [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues) **검색**
3. GitHub Discussions에서 **질문**
4. **지원팀에 연락** (호스팅 서비스 사용자)
5. 질문으로 **새 이슈 열기**

기억하세요: Ministry Mapper는 오픈 소스이며 자원봉사자가 유지 관리합니다. 도움을 요청할 때 인내심을 가지고 존중하는 태도를 보여주세요!
