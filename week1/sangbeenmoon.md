## 핵심 내용 요약
- 웹 계층은 무상태 계층으로
- 모든 계층에 다중화 도입
- 가능한 많은 데이터를 캐싱
- 여러 데이터 센터 지원
- 정적 콘텐츠는 CDB 으로 서비스
- 데이터 계층은 샤딩을 통해 규모 확장
- 각 계층은 독립적 서비스로 분할
- 시스템을 지속적으로 모니터링하고, 자동화 도구 활용



## 인상 깊은 내용
> 특히 기억에 남거나 공감된 부분
- 캐시 사용 시 유의할 점.
  - 캐시는 언제 사용하는게 좋나? A. 데이터 갱신은 자주 일어나지 않지만 참조는 빈번하게 발생하는 경우.
  - 만료 정책?
  - 원본과 일관성 유지 방안?
  - eviction 정책?
  - 캐시 메모리 크기?


### 데이터베이스의 규모 확장
#### 수직적 확장
- CPU, RAM, 디스크 증설

#### 수평적 확장
- a.k.a 샤딩
- 대규모 데이터베이스를 shard 라고 부르는 작은 단위로 분할.
  - 샤드에 보관되는 데이터 사이에는 중복이 없다. e.g. user_id % 4
- 샤딩 도입시 고려해야 하는 문제
  - 재샤딩
  - celebrity 문제 (hotspot key)
  - join & 비정규화


## 이해가 어려웠던 내용
> 설명이 어렵거나 개념이 잘 이해되지 않았던 부분

### RDB vs NoSQL
#### RDB 가 유리한 케이스
- 트랜잭션 e.g. 금융/결제 시스템, 주문/재고
- 데이터 정합성
- 복잡한 조회(JOIN) e.g. 복잡한 통계 조회
- 관계형 데이터 모델링 e.g. 회원/권한

#### NoSQL 이 유리한 케이스
- 수평 확장 e.g. SNS 피드 (관계형으로 하면 JOIN 이 폭발적으로 증가)
- 대용량 처리 e.g. SNS 피드
- 스키마 유연성 e.g. 로그 수집
- 높은 쓰기 성능 e.g. IoT 데이터

실무적으로는 "시스템의 Source of Truth는 PostgreSQL/MySQL에 두고, 성능 병목이 발생하는 부분을 Redis·MongoDB·DynamoDB 같은 NoSQL로 분리" 하는 아키텍처가 가장 흔하다.


### 실무에서 primary DB 장애시 데이터 유실 확률 낮추는 방법 (9p)
```
Primary
  ↓
Replica 1
Replica 2

+ Semi-sync (Secondary가 최소 1대는 로그를 받았다는 확인 후 Primary가 Commit 성공을 반환)
+ Failover 시 replication lag 확인 후 최신 Replica 만 승격
+ Binlog 보관 , PITR(Point In Time Recovery)
```

### 캐싱 전략
- read-through : 애플리케이션은 캐시만 조회
- Cache-Aside (Lazy Loading) : 애플리케이션이 캐시 로직을 직접 관리
```
Application
    ↓
  Cache
    ↓ miss
    DB
    ↓
  Cache 저장
```
- write-back (write-behind) : 먼저 캐시에 기록하고 DB 반영은 나중에 수행

```
Application
    ↓
 Cache
    ↓ (비동기)
    DB
```



### LRU cache 가 유리한 케이스
- 최근에 사용된 데이터가 다시 사용될 가능성이 높은 워크로드(Temporal Locality)가 있는 경우 LRU가 가장 유리하다. 반면 순차 스캔이나 캐시보다 큰 순환 접근 패턴에서는 성능이 크게 떨어질 수 있다.


## 의문이 드는 내용 / 동의하지 않는 내용
> 책의 주장에 대한 비판적인 관점

## 현업 경험 공유
> 실제 업무에서 겪었던 경험이나 사례
- Cache-Aside (Lazy Loading) 캐시 사용. e.g. 주소록 조회 API 호출 횟수 경감 목적.
- 무상태 아키텍처 - 로그인 인증 정보 관리 방법.
- RDB - NoSQL 조합