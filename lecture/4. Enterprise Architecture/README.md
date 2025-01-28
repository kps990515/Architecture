## Enterprise Architecture

### 계정 관리 시스템 (IDM)
  - Identity management system
  - 사용자의 계정을 관리, 인증(Authentication), 인가(Authorization) 수행
  - Idp (Identity provider): 사용자 정보를 저장하고, 이에 대한 인증을 수행(카카오, 네이버인증)
  - Sp(Service provider): 인증이 된후, 서비스를 제공하는 시스템
  - 분류 
    - 사내 인증 시스템 : 다양한 시스템에 대한 지원, 보통 이메일 시스템을 Idp로 사용
    - 대외 인증 시스템 : 대용량 사용자를 지원할 수 있어야 함, RDBMS로 보통 구현 

### 계정 관리 시스템 디자인시 주의 사항
  - 보안 : 최소권한, 다단계인증(MFA), RBAC(Role based authorization), 계정 생명주기
  - 확장성, 유연성 : 사용자수, 어플리케이션 증가에 따른 확장, 다양한 프로토콜 지원, SSO지원, 모바일 친화
  - 규제준수

### IDM 솔루션
  - 오픈소스
    - WSO2 Identity Server : 공부하기 좋음
    - KeyCloak : 클라우드, 온프렘지원
    - Apache syncope
  - 상용 솔루션
    - Microsoft AD : MS 생태계에 최적화됨, 온프렘 지원
    - Okta : 클라우드 기반
    - OneLogin : 클라우드 기반, 중소기업 레벨

### 모바일 페더레이션
  - 구글이 제공하는 사용자 인증 SDK
    - Facebook, Twitter, Google 등 SNS 인증 제공 (카카오, 네이버 플러그인)
    - IOS, Android,Web, Unity 등 지원
    - JWT 토큰 발급 지원
    - 빠르게 모바일, 웹 인증 구현에 유리함

### EAI 
  - 서로 다른 시스템간의 연계가 필요한 경우
  - 마이크로 서비스는 REST 기반 연계이기 때문에 별도의 EAI가 필요 없음
  - 레거시 시스템과의 연동에 많이 사용
  - vs ETL : ETL은 데이터베이스간 연결, 조금 더 실시간성

### CDN
  - 정적 컨텐츠 등을 사용자와 가까운 곳에 복사해놓고, 가까운 곳에서 서비스를 함(지연 감소)
  - Akami가 선두, 비용이 비쌈
  - 멀티 CDN으로 장애 대비 필요
  - 콘텐츠 압축 (Jpeg >> WebP 30%) 

### 글로벌 스케일 시스템 아키텍처
  - 구성방식
    - 비대칭 : Master(HQ) / Regional 센터
      - Master : 모든 인프라 + 운영 + 분석 + 머신러닝 학습
      - Regional : 운영 (서빙 API), 데이터 분석 시스템 (국지적)
    - 대칭 : : Master / Master 방식
  - 서비스 Look up
    - 주로 가까운 데이터에 접속하도록 함. (글로벌 로드 밸런서)
    - 사용자 프로필 (국적)에 따라 리전 지정 
  - 글로벌 시스템간 라우팅 : 공용 서비스 이외에, 대부분의 경우 Region간에 라우팅이 불필요

