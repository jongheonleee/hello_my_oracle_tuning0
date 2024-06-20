# ⚒️ sql_tuning

## 📋 목차
> ### 📌 01. SQL 처리 과정과 I/O
> ### 📌 02. 인덱스 기본 
> ### 📌 03. 인덱스 튜닝 
> ### 📌 04. 조인 튜닝 
> ### 📌 05. 소트 튜닝 
> ### 📌 06. DML 튜닝 
> ### 📌 07. SQL 옵티마이저 


## 📌 01. SQL 처리 과정과 I/O : 

### 1-1. SQL 파싱과 최적화 

> ### 👉 SQL is degined for a specific purpose : to query data contained in a relational database
> ###    SQL is a set-based, declarative query language, not an imperative language such as C.

- [SQL 작동 원리]


- 핵심은 "Structured Query Language". 즉, 구조적 질의 언어
- 더욱 자세히 말하면, 구조적, 집합적, 선언적 질의 언어


> ### 👉 SQL의 최적화 과정 : SQL 파싱 -> SQL 최적화 -> 로우 소스 생성(기계어)

- (1) SQL 파싱 
  - 파싱 트리 생성 : 각 문장을 파싱해서 구조 파악
  - Syntax 체크 : 문법적 오류 확인
  - Semantic 체크 : 의미상 오류 확인

- (2) SQL 최적화 
  - 옵티마이저가 Cost Base(통계 비용)기반으로 최적화 진행

- (3) 로우 소스 생성(기계어)
  - 실행 가능한 프로시저 형태로 포맷팅


> ### 👉 SQL 옵티마이저, 최적의 데이터 액세스 경로를 선택해주는 DBMS 엔진

- [SQL 옵티마이저 최적 경로 선택]

- 네비게이터와 같은 원리 


> ### 👉 튜닝의 시작은 실행 계획을 이해하는 것으로부터 시작

- [실행 계획]

- 실행 계획을 읽고 스스로 수동 개입할 것인지를 판단해야함
- TABLE ACCESS : 테이블 접근, NESTED LOOPS : 중첩 for문, INDEX : 인덱스 활용, COST : 비용
- 트리 구조로 이루어져 있음
- Cost는 예상치임
  - Cost : 쿼리를 수행하는 동안 발생할 것으로 예상하는 I/O 횟수 또는 예상 소요시간을 표현 
- 튜닝 대상
  - (1). 자주 쓰는 것
  - (2). 오래 걸리는 것(비용이 큰 것)

- [힌트(수동 명령)]


- 주석을 활용함. 실행 계획에만 영향을 줌(자바의 애너테이션과 유사함)

- [자주 사용하는 힌트 목록]

