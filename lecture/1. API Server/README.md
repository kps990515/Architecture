## API 서버
### 런타임
  - 자주 사용하는 것 : Container+VM, Serverless, K8S
  - 오토스케일링 : 기본적으로 20초 ~ 20분 걸림
    - Serverless의 Appengine(google)은 거의 즉시 가능
  - 콜드스타트 : 인스턴스가 아예 없을때 실행시키는데 오래걸림
  - Container
    - 초기 : Container + VM
    - 후기 : K8S
      - Standard(EKS) : VM 직접 컨트롤 가능(VM단위로 비용, 비쌈)
      - Autopilot(ECS) : 전체(VM + 컨트롤플레인)를 클라우드 밴더가 관리(저렴함)

### 로드 밸런서
  - L4 로드 밸런서(ELB) : IP단위로 관리
  - L7 로드 밸런서(ALB) : HTTP단위로 관리, uri기준으로 분산 가능(/user, /product)
  - SW 로드밸런서 : nginx(L7), haproxy(L4), envoy
  - 웹캐시 : 정적 데이터 + Rest Response
  - API 게이트웨이 : L7 + 알파
  - 네트워크 고려(ADN : Application delivery Netword) 
    - 리전에 따른 네트워크 홉 -> 요즘에는 클라우드 전용망 사용

### WAF
  - 웹 방화벽

### Spot VM을 이용한 비용 절감 방안
  - Standard VM : 고객이 일정한 비용을 지불하고 일정 기간 동안 안정적으로 사용할 수 있는 서버
  - Spot VM : 클라우드 제공 업체가 미사용 상태의 컴퓨팅 리소스를 할인된 가격에 제공하는 것
    - 중간에 자원이 예고 없이 회수될 수 있음
  - 절감방안
    - Standard VM + Spot VM
    - API서버는 stateless이기 때문에 Spot VM이 죽어도 리스타트시 큰 문제 없음

### 멀티스레드 VS 싱글스레드
  - 멀티스레드 서버(Spring) 
    - 쓰레드가 100개면 100개의 API요청 처리만 가능
    - DB 수행 시 Blocking 발생
  - C10K problem 발생 : 하나의 서버에서 동시에 1만(10,000) 개의 클라이언트 연결을 처리할 수 있는가
  - 싱글스레드 서버(node.js)
    - 싱글스레드 이벤트 루프를 통해 비동기 API 처리
    - DB 수행 시 Non blocking으로 처리
    - 문제 : 싱글스레드이기 때문에 무거운 코드가 실행되면 다른 처리가 중단

## 캐시 서버
### 종류
  - Memcached : 단순 Key, Value 스토어
  - Redis
    - 다양한 데이터 타입, 메시지 큐 모델 지원
    - 클러스터링 지원

### 캐싱 기법
  - TTL : 일정시간 지나면 캐시에서 데이터 삭제
    - 장점 : 실시간성이 덜 중요한 데이터에 적합, 캐시 오버플로우 방지
    - 단점 : 데이터 업데이트 발생 시 데이터 불일치 발생
  - Lazy Loading : 데이터가 캐시에 없는 경우 DB에서 캐시로 데이터 로딩
    - 장점 : 자주 사용되는 데이터만 캐시에 담김
    - 단점 : 처음 요청은 DB에 접근해야하기 때문에 캐시 미스율 높음
  - Write Through caching : 데이터 변경시 캐시 먼저 반영, 후에 DB 반영
    - 장점 : 캐시, DB 데이터 일관성
    - 단점 : 데이터 변경이 많을 경우 캐시 부하 발생
  - Write back : 캐시에 먼저 기록하고, 일정시간 후에 DB에 저장
    - 장점 : 쓰기작업이 빠름
    - 단점 : 데이터 불일치 발생, 장애 시 캐시 데이터 유실 가능