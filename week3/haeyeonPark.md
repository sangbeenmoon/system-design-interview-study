## 핵심 내용 요약
- Rate limiter 구현시 고려요소
    - 구현 위치: 서버 vs 클라이언트 vs middle ward (API Gateway)
    - 처리율 제한 알고리즘
        - 토큰 버킷: bucket size 가 있고, token 공급률에 따라 토큰 공급됨.
        - 누출 버킷(leaky bucket): bucket size가 있고, 규칙적인 outflow rate가 있음.
        - 고정 윈도 카운터(fixed window counter): 고정시간마다 요청의 카운터를 세고 limit 넘으면 버린다.
        - 이동 윈도 로그(sliding window log): 
        - 이동 윈도 카운터(sliding window counter)
    - 카운터 저장위치: 레디스 자주 사용됨.(메모리 기반 저장.  fast)
        - race condition: lock --> 성능 저하 우려시 lua script, sorted set 레디스 자료구조 사용
- 안정 해시 설계
    - consisten hash ? 해시테이블 크기가 조정될때 (키의 개수 / 슬롯의 개수)의 키만 재배치하는 해시 기술
    - 가상노드 없이 일반적인 접근법으로 생각하면 키도, 파티션도 해시공간에서 균등함을 보장할수 없다는 근본적 문제점에 도달 
    - sol. 가상노드 배치. 
    - 아마존 dynamo, apache cassandra같은 분산 저장 시스템에서 실제로 널리 쓰이는 기술이다.

## 인상 깊은 내용
> 특히 기억에 남거나 공감된 부분

## 이해가 어려웠던 내용
> 설명이 어렵거나 개념이 잘 이해되지 않았던 부분

## 의문이 드는 내용 / 동의하지 않는 내용
> 책의 주장에 대한 비판적인 관점

## 현업 경험 공유
> 실제 업무에서 겪었던 경험이나 사례
- 

## 스터디원에게 질문 하나
> 간단한 가상 면접 질문을 하나 던진다면?
- 클라이언트 vs 서버 사이드 rate limiter 설계의 장단점은 무엇일까요? 언제 무엇을 사용해야할까요?