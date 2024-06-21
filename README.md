# ⚒️ sql_tuning

## 📋 목차
> ### 📌 01. SQL 처리 과정과 I/O : SQL 기초 내부 동작 및 튜닝 시작
> ### 📌 02. 인덱스 기본 : 인덱스의 구조 및 활용법
> ### 📌 03. 인덱스 튜닝 
> ### 📌 04. 조인 튜닝 
> ### 📌 05. 소트 튜닝 
> ### 📌 06. DML 튜닝 
> ### 📌 07. SQL 옵티마이저 


## 📌 01. SQL 처리 과정과 I/O : SQL 기초 내부 동작 및 튜닝 시작

### 1-1. SQL 파싱과 최적화 

> ### 👉 SQL is degined for a specific purpose : to query data contained in a relational database
> ###    SQL is a set-based, declarative query language, not an imperative language such as C.

- [SQL 작동 원리]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0c1ead91-1ad8-4dbc-8386-130c437a066f" width="500" height="500"/>

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

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/490aa2ab-d942-4e34-8053-b07ad8c499c8" width="500" height="500"/>

- 네비게이터와 같은 원리 


> ### 👉 튜닝의 시작은 실행 계획을 이해하는 것으로부터 시작

- [실행 계획]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/5e555c56-1e82-43db-9f16-100b4345d90e" width="500" height="500"/>

- 실행 계획을 읽고 스스로 수동 개입할 것인지를 판단해야함
- TABLE ACCESS : 테이블 접근, NESTED LOOPS : 중첩 for문, INDEX : 인덱스 활용, COST : 비용
- 트리 구조로 이루어져 있음
- Cost는 예상치임
  - Cost : 쿼리를 수행하는 동안 발생할 것으로 예상하는 I/O 횟수 또는 예상 소요시간을 표현 
- 튜닝 대상
  - (1). 자주 쓰는 것
  - (2). 오래 걸리는 것(비용이 큰 것)

- [힌트(수동 명령)]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/daf3897f-e127-411b-8848-c2d126e4ef3d" width="500" height="500"/>



- 주석을 활용함. 실행 계획에만 영향을 줌(자바의 애너테이션과 유사함)

- [자주 사용하는 힌트 목록]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/116f4d10-d6dc-4a59-9523-508d3754ca8b" width="500" height="500"/>

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/092617d1-a35b-4c96-bf7f-22c6d42832fd" width="500" height="500"/>

<br>
<br>

### 1-2. SQL 공유 및 재사용

> ### 👉 Soft Parsing(소프트 파싱), Hard Parsing(하드 파싱)과 바인드 변수 활용

- [SGA 구성요소]


<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/fb7e2de7-f0ab-4ccb-ae21-55dae03d9752" width="500" height="500"/>

- [소프트 파싱, 하드 파싱 원리]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/52765269-9858-45d2-8b92-5a48c0021ed9" width="500" height="500"/>

- 소프트 파싱 : SQL을 캐시에서 찾아서 곧바로 실행단계로 가는 것
- 하드 파싱 : 캐시에서 SQL 문을 못 찾음, 최적화 및 로우 소스 생성 단계까지 가는 것 

> ### 👉 SQL은 모두 문자열, 따라서 ?(바인드 변수)를 활용해서 캐시 활용도를 높여야함 

- SQL 은 문자열, 또한 SQL은 따로 이름이 없어서 그 자체가 이름 역할을함
- 따라서, 작은 부분이라도 달라도 서로 다른 SQL 문임

- [서로 다른 SQL]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/8c93a6f8-2a5e-4d48-b045-c6bca4c9f9a2" width="500" height="500"/>

- [바인드 변수 활용]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/dcb1bf33-74af-4eeb-b4db-bca9fed9a5bc" width="500" height="500"/>

<br>
<br>

### 1-3. 데이터 저장 구조 및 I/O 메커니즘

> ### 👉 튜닝이란? 디스크 I/O를 최소화하는 것

- [SQL이 느린 이유]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/a186a5a5-4866-40bd-a5a5-a45c7e92c283" width="500" height="500"/>

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b02a5ca7-ab7e-4dda-adea-c8bbdda7a550" width="500" height="500"/>


- 튜닝이란? 어떻게 하면 디스크 I/O를 최소화할수 있을까에 대한 이야기
- 왜냐하면, I/O call이 많을 수록 그 만큼 대기가 많아짐

> ### 👉 데이터베이스 저장 구조 : 테이블스페이스(데이터 저장소) -> 세그먼트(종류별 저장) -> 익스텐드(파일) -> 블럭(I/O 최소 단위) -> 로우

- [논리적 구조]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b354bb75-27e7-4b66-a95f-4bea1026814c" width="500" height="500"/>


- [물리적 구조]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/d1781899-35a7-486c-8f51-6d5c1fb6f06c" width="500" height="500"/>


- 블럭 : 최소 I/O 단위, 익스텐트 : 연속된 블록 집합(확장 단위), 세그먼트 : 데이터 저장 공간이 필요한 오브젝트, 테이블스페이스 : 세그먼트를 담는 콘테이너, 데이터 파일 : 디스크 상의 물리적인 OS 파일
- DBMS가 데이터를 읽고 쓰는 단위는 블록
- 오라클은 기본적으로 8KB 크기의 블록 사용, 즉 한번에 I/O 할때마다 8KB 사용

> ### 👉 시퀀셜(순차) 액세스와 랜덤(이진) 액세스

- [시퀀셜 액세스, 랜덤 액세스]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/07da32de-a2f7-4054-b265-ac3f906ed861" width="500" height="500"/>

- 시퀀셜(순차) 액세스 : O(n) 순차탐색, 대량 액세스일 때 유리, 순서대로 블록을 읽음
- 랜덤(이진) 액세스 : O(logn) 이진탐색, 소량(10~15%)일 때 유리, 한 블록씩 읽음

> ### 👉 논리적 I/O와 물리적 I/O, 메모리에서 읽는 것과 디스크에서 읽는 것

- [SGA]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/07da32de-a2f7-4054-b265-ac3f906ed861" width="500" height="500"/>


- [논리적 I/O와 물리적 I/O]

<img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/1bd88e3c-50cb-46e7-a887-7df0aaa233e7" width="500" height="500"/>


- 논리적 블록 I/O : SQL 처리 과정에서 발생한 총 블록 I/O로써, 메모리 I/O를 의미함
- 물리적 블록 I/O : 디스크에서 발생한 총 블록 I/O를 말함 

<br>
<br>

## 📌 02. 인덱스 기본 : 인덱스의 구조 및 활용법 

### 2-1. 인덱스 구조 및 탐색 

> ### 👉 데이터를 찾는 방법은 2가지 -> (1) full scan, (2) index scan
- (1) full scan : 테이블 전체를 스캔 
- (2) index scan : 인덱스(b * tree) 스캔

> ### 👉 인데스 튜닝의 핵심 -> range(탐색 범위)를 작게 만들기 
- [range가 작은 경우]

- [range가 큰 경우]

- 인덱스 튜닝의 핵심 "인덱스 스캔 효율화. 즉, 스캔 범위를 작게 만들기"
  - 컬럼 순서 : 위의 사진에서 스캔 범위가 차이나는 이유는 컬럼 순서 때문
  - 인덱스를 Range Scan 하기 위해서는 인덱스 선두 컬럼이 조건절에 있어야함 
  - 인덱스 선두 컬럼이 변경되지 않은 상태로 조건절에 명시되어 있으면, 무조건 Range Scan 가능 
  


- [인덱스 스캔과 랜덤 액세스]

- 테이블 액세스 횟수 줄이기, 즉 "랜덤 액세스 최소화 튜닝"


> ### 👉 인덱스의 구조 -> B * tree

- [B * tree]

- 인덱스 : 대용량 테이블에서 소량의 데이터만 빠르게 효율적으로 액세스하기 위해 사용하는 트리
  - 정렬/위치 
- B * Tree 용어
  - ROWID : 테이블 블록 주소 + 로우 번호
  - 데이터 블록 주소 : 데이터 파일 번호 + 블록 번호
  - 블록 번호 : 데이터파일 내에서 부여한 상대적 순번
  - 로우 번호 : 블록 내 순번

> ### 👉 인덱스 탐색 방법 -> (1) 수직 탐색, (2) 수평 탐색
- [인덱스 탐색]


- 인덱스 탐색 과정
  - (1) 수직 탐색 : 인덱스 스캔 시작지점을 찾는 과정, root -> x
    - '조건을 만족하는 첫 번재 레코드'를 찾는 과정 
    
  - (2) 수평 탐색 : 데이터를 찾는 과정, left -> right
    - '찾고자 하는 데이터가 더 안 나타날 때까지 인덱스 리프 블록을 스캔'

> ### 👉 결합 인덱스 구조와 탐색 -> 칼럼이 n개인 인덱스
- [결합 인덱스]


- 두 개 이상의 컬럼을 결합된 인덱스를 지칭
- 주의할 점, where에 2개 이상의 조건을 명시하면 인덱스 탐색과 동시에 조건 전부 확인함

> ### 👉 'Balanced'의 의미 : 트리의 높이 차이가 1이하인 트리 
- [Balanced 아닌 트리]


### 2-2. 인덱스 기본 사용법 

> ### 👉 Range Scan 안되는 경우 -> (1) 변형, (2) like, (3) or, in
- "인덱스를 사용한다" -> "리프에서 스캔 시작점을 찾아서 거기부터 스캔하다 중간에 멈추는 것"
- 특정 상황에서는 인덱스를 활용하지 못함 
  - (1) 변형 : 인덱스 칼럼이 변형 -> 인덱스 스캔 시작점을 찾을 수 없음 
    - where substr(생년월일, 5, 2) = '05', where nvl(주문수량, 0) < 100
  - (2) like : '김%' : o, '%김' : x, '%김%' : x -> '김'을 포함하는 값은 전체 구간에 흩어져 있기 때문
  - (3) or / in : or, in은 n개 값, 어느 한 시작지점을 찾을 수 없음 
    - where (전화번호 = :tel_no OR 고객명 = :cust_nm)
    - or expansion으로 or 문제해결 가능  

- 결론 
  - [인덱스를 쓰지 못하는 경우]

> ### 👉 인덱스를 이용한 소트 연산 생략 -> 인덱스의 특징인 '정렬'을 활용
- 인데스의 특징은 정렬과 위치 정보가 있다는 것
  - 이를 통해 소트 연산 생략 가능 
- 위와 마찬가지로, order by 절에서 칼럼이 변형되면 인덱스 활용 못함 

- [인덱스로 소트]


> ### 👉 SELECT-LIST에서 칼럼 변형 -> 인덱스를 특징을 살려서 성능을 높임 
- [인덱스의 특징]

- [min(), max()]

- 인덱스 특징에 의해 min(), max() 성능이 좋음
- 함수의 순서에 따라 index 활용 여부가 달라짐 


> ### 👉 자동형변환에 의지하지 말라

- 자동형변환은 쿼리 문의 가독성을 떨어트림, 의도를 파악하기 어려움 
- 또한, 개발자가 형변환 함수를 생략해도 옵티마이저가 자동으로 생성


### 2-3. 인덱스 확장기능 사용법 

> ### 👉 인덱스 스캔 방법 총 6가지 -> (1) range scan, (2) full scan, (3) unique scan, (4) skip scan, (5) fast full scan, (6) rage scan descending
- (1) range scan : root -> leaf & left -> right
  - [range scan]
  
- (2) full scan : left -> right
  - [full scan]
  
- (3) unique scan : root -> leaf(=target)
  - [unique scan]
  
- (4) skip scan : range scan + skip
  - [skip scan]
  
- (5) fast full scan : full 
  - [fast full scan]
  
- (6) rage scan descending : root -> leaf & right -> left 
  - [rage scan descending]