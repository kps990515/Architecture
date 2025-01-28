## DB
### RDBMS VS NoSQL
  - RDBMS
    - 테이블간의 관계가 있을 때 유용
    - 일관성 보장(중요한 트랜잭션에 유용)
  - NoSQL
    - 단순, 대용량 데이터에 유리
    - 일관성 보장 못함
    - 비정형 데이터 지원

### 설치형 VS 매니지드
  - 설치형
    - 관리가 까다로움
    - On-premise에 사용
    - 커스터마이징 가능
    - 다양한 인프라를 활용해 성능 극대화 가능
  - 매니지드
    - 관리 필요없음, 분산환경/HA등 복잡한 환경에 유리
    - 공급자 마다 내부 아키를 변경하여 성능, 용량을 추가 제공하는 경우 있음
    - 가격 상대적으로 비쌈

### 오픈소스 VS 유료

### RDBMS
 - OLTP(Online Transaction Processing) : 일반적인 트랜잭션 CRUD에 최적화된 설정
 - OLAP(Online Analytical Processing) : 분석(READ)쿼리에 최적화된 설정
 - 오라클 
   - 가격은 높으나 높은 성능, 안정성, 확장성 제공
   - RAC 구성을 통한 Active-Active 구성 가능
   - 엑사데이터(하드웨어와 소프트웨어를 통합한 솔루션) 통한 높은 성능 제공
 - MS SQL
   - 중간 가격대로 ,가성비
   - 게이밍, 중소 규모
 - Postgres
   - 엔터프라이즈 수준의 기능을 지원(2PC : Two-Phase Commit)
   - Vector(RAG), GIS등 다양한 기능 지원
 - MySql
   - OLTP에 최적화, 높은성능
   - 사용이 편리

### 오픈소스 RDBMS
  - Postgres, Mysql 지원
  - 클라우드 서비스 형태
    - 커스터마이즈드 서비스
      - 내부엔진을 재설계해 더 높은 성능과 확장성 제공, 더비쌈
      - Google AllovDB, AWS Aurora DB
    - 오픈소스 매니지드 서비스
      - 단순 오픈소스 DB를 클라우드에서 제공
      - Google CloudSQL, AWS RDS

### 분산 트랜잭션
  - 결제 DB, 주문 DB가 분리되었을 때 결재는 성공, 주문 실패한다면?
  - 다른 트랜잭션이기에 결재는 성공으로 저장, 주문은 미생성
  - 해결법 : 2개의 트랜잭션을 묶어서 같이 성공처리하거나 같이 롤백 처리
    - Application단에서 XA(eXtended Architecture) 트랜잭션 서비스 사용 필요
    - XA-2PC
    
  - XA-2PC
    - 두개 이상의 리소스에(DB, 메시지큐) 대해 트랜잭션 처리
    - 트랜잭션 관리 시스템 필요(자바: JTS/JTA지원하는 Jeus, weblogic, JBoss 등)
    - 동작 과정
      - Prepare Phase (준비 단계)
        - 트랜잭션 관리자(WAS 등)가 트랜잭션에 참여하는 모든 노드에게 작업 준비 상태 확인
        - 각 노드는 준비 상태 응답
      - Commit Phase (커밋 단계)
        - 모든 노드가 YES를 응답한 경우 Commit
        - 한 노드라도 NO를 응답한 경우 Rollback

### RDBMS 고가용성 및 스케일링 구조
  - Active-StandBy 구조(Master)
    - HA를 지원하기 위한 구조
    - Active가 다운되었을때 StandBy노드가 Master노드로 격상되어 정상 서비스 제공
    - 사용하지 않는 StandBy 1개 이상 유지 필요
  - Master Slave 구조
    - 위 Active-StandBy에서 Active가 Master
    - Master노드에는 CUD
    - Slave는 읽기만
  - Master-Slave VS CQRS
    - Master-Slave는 데이터베이스 수준에서 읽기와 쓰기를 물리적으로 분리하고, 복제를 통해 성능을 최적화하는 구조
    - CQRS는 **데이터 모델 수준(테이블 또는 데이터베이스)**에서 읽기와 쓰기의 책임을 논리적으로 분리하여 최적화.
    - 
      | **구분**           | **Master-Slave**                                      | **CQRS**                                       |
      |--------------------|-------------------------------------------------------|-----------------------------------------------|
      | **적용 범위**      | 데이터베이스 레벨: Master와 Slave라는 독립된 DB 인스턴스. | 테이블/데이터 모델 레벨: 읽기와 쓰기를 분리.      |
      | **작업 분리**      | Master는 쓰기, Slave는 읽기를 처리.                   | 쓰기 모델과 읽기 모델을 별도로 설계.            |
      | **복제 사용 여부** | Master에서 Slave로 데이터 복제를 통해 동기화.          | 데이터 동기화는 이벤트, 메시징, 별도 설계로 처리. |
      | **데이터 일관성**  | 주로 실시간 복제 또는 일정한 지연이 발생.              | 최종 일관성(Eventual Consistency)을 수용.       |
      | **데이터베이스 구성** | 하나의 Master DB와 여러 Slave DB를 사용.              | 쓰기와 읽기용 DB를 완전히 분리하거나 하나의 DB 내부에서 처리. |

  <br>    
  
  - Active Active 구조
    - HA와 확장성을 동시에 구현하는 구조로, 여러 노드에 동시 CRUD 가능
    - 노드간 트랜잭션을 일관성있게 복제해야하기에 구현 어려움
    - Oracle RAC, MySQL Galera Cluster 솔루션 필요

### DB 샤딩
  - 데이터양이 많아 단일 DB에 저장이 어려운경우, 분리된 DB에 데이터를 저장
  - 과거 : 동일 DB(Mysql)여러개에 샤딩
  - 현재 : 다른 DB(메타 : MySql, 대규모데이터 : NoSql)에 샤딩
  - Vertical sharding(잘 안씀) : 범위 단위 샤딩
  - Horizontal sharding : 도메인 단위로 샤딩

### NoSQL(Not Only SQL)
  - RDBMS와 다르게 SQL을 사용하지 않는 DBMS의 총칭
  - 특징
    - 스키마리스
    - 수평성 확장성(Vertical sharding과 유사, 데이터가 늘어나면 확장 가능)
    - 다양한 데이터 모델(문서, KeyValue, Graph)
  - 대규모 데이터와 빠른 읽기 쓰기

### NoSQL 분류
  - Key-Value 스토어
    - Sorting된 Key를 사용하는 경우가 많음
    - Put/Get, 커서 이동정도의 지원(Join, sorting, where 등 지원안함)
    - 제품 : HBase, Cassandra, Google Cloud BigTable, AWS Dynamo
  - Document DB
    - Json 문서를 통으로 저장가능
    - Where, join 등 복잡한 쿼리 일부 지원
    - 제품 : MongoDB, Google FireStore
  - Graph DB
    - 그래프 구조의 DB
    - 노드와 엣지를 통해 관계를 표현(노드 : 배우, 엣지 : 출연함)
    - 제품 : Neo4J

### CAP 이론
  - 분산 데이터 저장소가 다음 3가지 보장 중 2가지만 제공할 수 있다는 이론 
  1. 일관성(Consistency) : 시스템 내 모든 노드가 동일한 데이터 반환 가능
  2. 가용성(Availability) : 일부 노드가 실패하더라도 서비스가 계속 제공될 수 있도록 보장
  3. 파티션 허용(Partition-Tolerance) : 노드간 네트워크가 실패하더라도 서비스 제공 보장

### NOSQL 아키텍쳐
  - 일반적으로 분산형 구조
  - Vertical sharding 사용해서 각 노드마다 한 테이블의 데이터를 분리된 구간별로 저장, 관리
  - Hot Key : 특정 키 영역이 자주 액세스 되는 경우 -> 성능 저하 발생

