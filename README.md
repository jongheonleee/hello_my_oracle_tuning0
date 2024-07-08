# ⚒️ sql_tuning

## 📋 목차
> ### 📌 01. SQL 처리 과정과 I/O : SQL 기초 내부 동작 및 튜닝 시작
> ### 📌 02. 인덱스 기본 : 인덱스의 구조 및 활용법
> ### 📌 03. 인덱스 튜닝 : range, table access 어떻게 줄여나갈까?
> ### 📌 04. 조인 튜닝 : 어떤 상황에 어떤 조인을 써야 효율적일까?
> ### 📌 05. 소트 튜닝 : 어떻게하면 소트 처리를 효율적으로할까?
> ### 📌 06. DML 튜닝 
> ### 📌 07. SQL 옵티마이저 

<br>
<br>

## 📌 01. SQL 처리 과정과 I/O : SQL 기초 내부 동작 및 튜닝 시작

<br>

### 1-1. SQL 파싱과 최적화 

> ### 👉 SQL is degined for a specific purpose : to query data contained in a relational database
> ###    SQL is a set-based, declarative query language, not an imperative language such as C.

- [SQL 작동 원리]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0c1ead91-1ad8-4dbc-8386-130c437a066f" width="500" height="500"/>

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
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/490aa2ab-d942-4e34-8053-b07ad8c499c8" width="500" height="500"/>
- 네비게이터와 같은 원리 


> ### 👉 튜닝의 시작은 실행 계획을 이해하는 것으로부터 시작

- [실행 계획]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/5e555c56-1e82-43db-9f16-100b4345d90e" width="500" height="500"/>
- 실행 계획을 읽고 스스로 수동 개입할 것인지를 판단해야함
- TABLE ACCESS : 테이블 접근, NESTED LOOPS : 중첩 for문, INDEX : 인덱스 활용, COST : 비용
- 트리 구조로 이루어져 있음
- Cost는 예상치임
  - Cost : 쿼리를 수행하는 동안 발생할 것으로 예상하는 I/O 횟수 또는 예상 소요시간을 표현 
- 튜닝 대상
  - (1). 자주 쓰는 것
  - (2). 오래 걸리는 것(비용이 큰 것)

- [힌트(수동 명령)]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/daf3897f-e127-411b-8848-c2d126e4ef3d" width="500" height="500"/>
- 주석을 활용함. 실행 계획에만 영향을 줌(자바의 애너테이션과 유사함)

<br>

- [자주 사용하는 힌트 목록]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/116f4d10-d6dc-4a59-9523-508d3754ca8b" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/092617d1-a35b-4c96-bf7f-22c6d42832fd" width="500" height="500"/>

<br>

### 1-2. SQL 공유 및 재사용

> ### 👉 Soft Parsing(소프트 파싱), Hard Parsing(하드 파싱)과 바인드 변수 활용

- [SGA 구성요소]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/fb7e2de7-f0ab-4ccb-ae21-55dae03d9752" width="500" height="500"/>

- [소프트 파싱, 하드 파싱 원리]

- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/52765269-9858-45d2-8b92-5a48c0021ed9" width="500" height="500"/>

- 소프트 파싱 : SQL을 캐시에서 찾아서 곧바로 실행단계로 가는 것
- 하드 파싱 : 캐시에서 SQL 문을 못 찾음, 최적화 및 로우 소스 생성 단계까지 가는 것 

<br>

> ### 👉 SQL은 모두 문자열, 따라서 ?(바인드 변수)를 활용해서 캐시 활용도를 높여야함 

- SQL 은 문자열, 또한 SQL은 따로 이름이 없어서 그 자체가 이름 역할을함
- 따라서, 작은 부분이라도 달라도 서로 다른 SQL 문임

- [서로 다른 SQL]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/8c93a6f8-2a5e-4d48-b045-c6bca4c9f9a2" width="500" height="500"/>

- [바인드 변수 활용]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/dcb1bf33-74af-4eeb-b4db-bca9fed9a5bc" width="500" height="500"/>

<br>

### 1-3. 데이터 저장 구조 및 I/O 메커니즘

> ### 👉 튜닝이란? 디스크 I/O를 최소화하는 것

- [SQL이 느린 이유]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/a186a5a5-4866-40bd-a5a5-a45c7e92c283" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b02a5ca7-ab7e-4dda-adea-c8bbdda7a550" width="500" height="500"/>
- 튜닝이란? 어떻게 하면 디스크 I/O를 최소화할수 있을까에 대한 이야기
- 왜냐하면, I/O call이 많을 수록 그 만큼 대기가 많아짐

<br>

> ### 👉 데이터베이스 저장 구조 : 테이블스페이스(데이터 저장소) -> 세그먼트(종류별 저장) -> 익스텐드(파일) -> 블럭(I/O 최소 단위) -> 로우

- [논리적 구조]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b354bb75-27e7-4b66-a95f-4bea1026814c" width="500" height="500"/>

- [물리적 구조]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/d1781899-35a7-486c-8f51-6d5c1fb6f06c" width="500" height="500"/>


- 블럭 : 최소 I/O 단위, 익스텐트 : 연속된 블록 집합(확장 단위), 세그먼트 : 데이터 저장 공간이 필요한 오브젝트, 테이블스페이스 : 세그먼트를 담는 콘테이너, 데이터 파일 : 디스크 상의 물리적인 OS 파일
- DBMS가 데이터를 읽고 쓰는 단위는 블록
- 오라클은 기본적으로 8KB 크기의 블록 사용, 즉 한번에 I/O 할때마다 8KB 사용

<br>

> ### 👉 시퀀셜(순차) 액세스와 랜덤(이진) 액세스

- [시퀀셜 액세스, 랜덤 액세스]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/07da32de-a2f7-4054-b265-ac3f906ed861" width="500" height="500"/>
- 시퀀셜(순차) 액세스 : O(n) 순차탐색, 대량 액세스일 때 유리, 순서대로 블록을 읽음
- 랜덤(이진) 액세스 : O(logn) 이진탐색, 소량(10~15%)일 때 유리, 한 블록씩 읽음

<br>

> ### 👉 논리적 I/O와 물리적 I/O, 메모리에서 읽는 것과 디스크에서 읽는 것

- [SGA]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/07da32de-a2f7-4054-b265-ac3f906ed861" width="500" height="500"/>
- [논리적 I/O와 물리적 I/O]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/1bd88e3c-50cb-46e7-a887-7df0aaa233e7" width="500" height="500"/>
- 논리적 블록 I/O : SQL 처리 과정에서 발생한 총 블록 I/O로써, 메모리 I/O를 의미함
- 물리적 블록 I/O : 디스크에서 발생한 총 블록 I/O를 말함 

<br>
<br>

## 📌 02. 인덱스 기본 : 인덱스의 구조 및 활용법 

<br>

### 2-1. 인덱스 구조 및 탐색 

> ### 👉 데이터를 찾는 방법은 2가지 -> (1) full scan, (2) index scan
- (1) full scan : 테이블 전체를 스캔 
- (2) index scan : 인덱스(b * tree) 스캔

> ### 👉 인데스 튜닝의 핵심 -> range(탐색 범위)를 작게 만들기 
- [range가 작은 경우]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/125e7311-eb26-444a-aa02-ef2fd4c66196" width="500" height="500"/>

- [range가 큰 경우]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/9f2f3d61-3619-43a2-acf4-e2bbf410bd5f" width="500" height="500"/>

- 인덱스 튜닝의 핵심 "인덱스 스캔 효율화. 즉, 스캔 범위를 작게 만들기"
  - 컬럼 순서 : 위의 사진에서 스캔 범위가 차이나는 이유는 컬럼 순서 때문
  - 인덱스를 Range Scan 하기 위해서는 인덱스 선두 컬럼이 조건절에 있어야함 
  - 인덱스 선두 컬럼이 변경되지 않은 상태로 조건절에 명시되어 있으면, 무조건 Range Scan 가능 
  


- [인덱스 스캔과 랜덤 액세스]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/9eb4acd8-fecf-4a68-93d5-f40b73fed7e2" width="500" height="500"/>
- 테이블 액세스 횟수 줄이기, 즉 "랜덤 액세스 최소화 튜닝"

<br>

> ### 👉 인덱스의 구조 -> B * tree

- [B * tree]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/181936e4-ba2e-435f-9e48-bd9d59170dd6" width="500" height="500"/>

- 인덱스 : 대용량 테이블에서 소량의 데이터만 빠르게 효율적으로 액세스하기 위해 사용하는 트리
  - 정렬/위치 
- B * Tree 용어
  - ROWID : 테이블 블록 주소 + 로우 번호
  - 데이터 블록 주소 : 데이터 파일 번호 + 블록 번호
  - 블록 번호 : 데이터파일 내에서 부여한 상대적 순번
  - 로우 번호 : 블록 내 순번

<br>

> ### 👉 인덱스 탐색 방법 -> (1) 수직 탐색, (2) 수평 탐색
- [인덱스 탐색]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/cb30243a-9599-413f-9c7d-e3c7d2f1434f" width="500" height="500"/>

- 인덱스 탐색 과정
  - (1) 수직 탐색 : 인덱스 스캔 시작지점을 찾는 과정, root -> x
    - '조건을 만족하는 첫 번재 레코드'를 찾는 과정 
    
  - (2) 수평 탐색 : 데이터를 찾는 과정, left -> right
    - '찾고자 하는 데이터가 더 안 나타날 때까지 인덱스 리프 블록을 스캔'

<br>

> ### 👉 결합 인덱스 구조와 탐색 -> 칼럼이 n개인 인덱스
- [결합 인덱스]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/464ddfc2-35ca-401b-84a4-d13eb40b6f02" width="500" height="500"/>

- 두 개 이상의 컬럼을 결합된 인덱스를 지칭
- 주의할 점, where에 2개 이상의 조건을 명시하면 인덱스 탐색과 동시에 조건 전부 확인함

<br>

> ### 👉 'Balanced'의 의미 : 트리의 높이 차이가 1이하인 트리 
- [Balanced 아닌 트리]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/a73dd7ed-568d-4296-bb62-e17b2cde361e" width="500" height="500"/>

<br>

### 2-2. 인덱스 기본 사용법 

<br>

> ### 👉 Range Scan 안되는 경우 -> (1) 변형, (2) like, (3) or, in
- "인덱스를 사용한다" -> "리프에서 스캔 시작점을 찾아서 거기부터 스캔하다 중간에 멈추는 것"
- 특정 상황에서는 인덱스를 활용하지 못함 
  - (1) 변형 : 인덱스 칼럼이 변형 -> 인덱스 스캔 시작점을 찾을 수 없음 
    - where substr(생년월일, 5, 2) = '05', where nvl(주문수량, 0) < 100
  - (2) like : '김%' : o, '%김' : x, '%김%' : x -> '김'을 포함하는 값은 전체 구간에 흩어져 있기 때문
  - (3) or / in : or, in은 n개 값, 어느 한 시작지점을 찾을 수 없음 
    - where (전화번호 = :tel_no OR 고객명 = :cust_nm)
    - or expansion으로 or 문제해결 가능
  - (4) 부정 표현식
  - (5) null 

- 결론 
  - [인덱스를 쓰지 못하는 경우]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/5eb28462-af4f-40d2-8f70-7cdeba0bf572" width="500" height="500"/>

<br>

> ### 👉 인덱스를 이용한 소트 연산 생략 -> 인덱스의 특징인 '정렬'을 활용
- 인데스의 특징은 정렬과 위치 정보가 있다는 것
  - 이를 통해 소트 연산 생략 가능 
- 위와 마찬가지로, order by 절에서 칼럼이 변형되면 인덱스 활용 못함 

- [인덱스로 소트]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/6c9f84f0-f023-45fd-81fc-21b6a1b28a7a" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/870f6f64-8622-44aa-8e2d-eae51b239119" width="500" height="500"/>

<br>

> ### 👉 SELECT-LIST에서 칼럼 변형 -> 인덱스를 특징을 살려서 성능을 높임 
- [인덱스의 특징]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/aa7ab6ba-b0f1-4c66-b7e4-057ea6792500" width="500" height="500"/>

- [min(), max()]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/df86d1d1-df04-4e54-81e6-4b3520bfa170" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/d53dc4c0-c354-4c13-becb-52e340667d46" width="500" height="500"/>

- 인덱스 특징에 의해 min(), max() 성능이 좋음
- 함수의 순서에 따라 index 활용 여부가 달라짐 

<br>

> ### 👉 자동형변환에 의지하지 말라

- 자동형변환은 쿼리 문의 가독성을 떨어트림, 의도를 파악하기 어려움 
- 또한, 개발자가 형변환 함수를 생략해도 옵티마이저가 자동으로 생성

<br>

### 2-3. 인덱스 확장기능 사용법 

<br>

> ### 👉 인덱스 스캔 방법 총 6가지 -> (1) range scan, (2) full scan, (3) unique scan, (4) skip scan, (5) fast full scan, (6) rage scan descending
- (1) range scan : root -> leaf & left -> right
  - [range scan]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/7919b17f-1aa9-408c-ba24-a6bbeb677b61" width="500" height="500"/>
  
  
- (2) full scan : left -> right
  - [full scan]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/87b6ae48-8821-4a1c-9120-5e4aa1f754c3" width="500" height="500"/>
  
- (3) unique scan : root -> leaf(=target)
  - [unique scan]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/39ddc746-7415-4286-8bfb-4f7f4cd64592" width="500" height="500"/>
  
- (4) skip scan : range scan + skip
  - [skip scan]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/d9e69c80-a0de-42bc-80b1-2b9019a9cfd3" width="500" height="500"/>
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/3a947a19-f25f-425e-b313-9ba6497bc443" width="500" height="500"/>
  
- (5) fast full scan : full 
  - [fast full scan]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/65b916db-3e83-4c99-90dc-fa9e418c49f8" width="500" height="500"/>
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/cf683320-0534-4229-a928-3b7a0551d3c6" width="500" height="500"/>
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/cb7e8df7-52bd-4645-b895-7cd9597feb9d" width="500" height="500"/>

  
- (6) rage scan descending : root -> leaf & right -> left 
  - [rage scan descending]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/cba3ff26-cee7-4d73-b96a-b9aa779de8cd" width="500" height="500"/>

<br>
<br>

## 📌 03. 인덱스 튜닝 : range, table access 어떻게 줄여나갈까?

### 3-1. 테이블 액세스 최소화

<br>

> ### 👉 튜닝 대상 2가지 - range, table access 최소화 
- [그림 1]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/36a84009-8e7c-4024-b45e-e0efe72851cc" width="500" height="500"/>
- [그림 2]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/d469ca4e-10f5-493b-b6c6-9317d48f6224" width="500" height="500"/>
- [그림 3]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/89ec5e14-cfc6-4016-8dec-10f6b9a73fcb" width="500" height="500"/>

- 인덱스를 이용하는 이유, 빠르게 ROWID를 얻기 위함 
  - ROWID : 데이터파일 번호 + 오브젝트 번호 + 블록 번호
  - ROWID는 논리적 주소 

<br>

> ### 👉 데이터의 군집도를 나타내는 '인덱스 클러스터링 팩터'

- [클러스터링 팩터가 좋은 경우]  
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0aa5a42a-f664-4261-a5a3-f95c7a3033a6" width="500" height="500"/>

- [클러스터링 팩터가 안좋은 경우]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/f077492b-bef9-4326-a30d-a0d77f19d8f6" width="500" height="500"/>

- 인덱스 클러스터링 팩터 : 특정 칼럼 기준으로 같은 값을 갖는 데이터의 군집도
- 클러스터링 팩터가 좋은 경우, IO 횟수가 줄어듬 - 여러번 읽지 않고 적은 횟수로 많이 읽음 

<br>

> ### 👉 인덱스는 소량의 데이터를 조회할 때 사용(10~15%)

- [인덱스 손익 분기점]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/e3c07299-7053-43d2-b3e9-37910e8b870b" width="500" height="500"/>

- table full scan : 시퀀셜 엑세스, 순차 탐색 -> Multiblock I/O
- index scan : 인덱스의 rowid를 이용한 테이블 액세스  -> Single Block I/O

<br>

> ### 👉 인덱스 컬럼 추가를 통해 테이블 엑세스를 최소화한다 

- [테이블 엑세스 많은 경우]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/56283ec3-2a5d-4b1d-9c21-870e80f6118f" width="500" height="500"/>

- [테이블 엑세스 적은 경우]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b0bd5c8d-4223-4076-b1f7-b10aa879757a" width="500" height="500"/>

- 테이블 엑세스가 일어나는 이유, 인덱스에 칼럼이 부족하기 때문
  - 따라서 인덱스에 컬럼을 추가하여 테이블 엑세스 최소화할 수 있음

<br>

> ### 👉 인덱스 구조 테이블 = 인덱스 + 테이블 

- [인덱스 구조 테이블]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/c9ee8a6d-3bf1-414b-a6bd-0523005213d0" width="500" height="500"/>

- 인덱스 구조 테이블 = IOT 라고함(오라클)
- 인덱스 리프 블록에 데이터 블록을 모두 저장하는 방식 
- 위와 같은 구조는 유지 비용이 많이 듬

<br>

> ### 👉 클러스터 테이블 = 테이블을 특정 기준으로 쪼갬

- [인덱스 클러스터 테이블]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/14e6cd76-e9a5-4ab9-b016-a5b50e0344a9" width="500" height="500"/>

- [해시 클러스터 테이블]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0ce129f4-9672-49a5-a774-c1b8a0cbbed0" width="500" height="500"/>

- 클러스터 테이블에는 크게 2가지 종류가 있음
  - 인덱스 클러스터 테이블 : 클러스터 키 값이 같은 레코드를 한 블록에 모아 놓은 구조
  - 해시 클러스터 테이블 : 위 테이블과 원리는 같지만, 해시 함수를 사용해 클러스터를 조회함 


<br>

### 3-2. 부분범위 처리 활용 

> ### 👉 부분범위 처리 = 필요할 때만 조금씩 일정량만 나눠서 분배 
- [부분범위 처리 비유]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/2c6b6e48-1863-4517-993a-771c9c247d4d" width="500" height="500"/>


- 위의 사진을 보면, 운반공은 미장공이 벽돌을 더 가져오라는 요청(fetch-call)이 있을 때만 벽돌을 실어 나름, 추가 요청 없으면 운반 작업 멈춤 
- DBMS가 클라에게 데이터 전송할 때도 일정량씩 나눠서 전송, 전체 결과집합 중 아직 정송하지 않은 분량이 있어도 서버 프로세스는 클라로부터 추가 Fetch Call을 받기까지 멈춤
- OLTP : Online Transaction Processing, 온라인 트랜잭션을 처리하는 시스템 
- OLAP : Online Analytical Processing, 사용자가 동일한 데이터를 여러 기준을 이용하는 다양한 방식으로 바라보면서 다차원 데이터 분석을 할 수 있도록 도움 
- 인덱스와 부분범위 처리 원리를 잘 활용하면 OLTP에서 극적인 성능개선 효과를 얻을 수 있음


<br>

### 3-3. 인덱스 스캔 효율화 

> ### 👉 컬럼의 순서와 where 절 작성을 통해 인덱스 스캔 효율화 도모 

- [인덱스 탐색 과정]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/a172b6f6-9e6f-4387-b1d7-94640006152d" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0967a56f-193a-41ff-be13-29fde525dc41" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b33baa3a-6b85-4f0b-b687-de33a4c09705" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/1e29ea76-ead9-4ad4-84c1-467fb1db23ac" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/ca724b67-d468-48f0-b588-61a1d5a93c98" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/c35c0df9-8a00-4c9f-ac83-58e106d91415" width="500" height="500"/>

<br>

> ### 👉 연속된 컬럼으로 조회해야 range 범위가 짧음 

- [연속적인/비연속적인 경우]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/5c559ee2-514d-417b-814f-c1f53d357ea5" width="500" height="500"/>


<br>
<br>

## 📌 04. 조인 튜닝 : 어떤 상황에 어떤 조인을 써야 효율적일까? 

<br>

### 4-1. NL 조인 : 인덱스를 이용한 조인(이중 for 문) -> 소용량

> ### 👉 Nested Loop 조인 -> O(n * logn), 앞에 작은 테이블 & 뒤에 큰 테이블이 유리함 

- [PL/SQL 코드]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/142e32e3-abac-4940-98cb-e7ca5b408cc7" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/8254bd29-a0b4-4f42-868b-d54414dadaa4" width="500" height="500"/>
- NL 조인은 Outer 와 Inner 양쪽 테이블 모두 인덱스를 이용. 물론 Outer 에서 Full Scan 하는 경우도 있음
- NL 조인 제어 -> 'use_nl(테이블)' 힌트 활용 
- NL 조인의 2 가지 특징 
  - (1) 랜덤 엑세스 위주의 조인 방식, 즉 인덱스 사용
  - (2) 한 레코드씩 순차적으로 진행
    
<br>

- [내부 동작 그림 1]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/1bb542b6-40f1-42fc-80a8-660cc44ba06f" width="500" height="500"/>

<br>

- [내부 동작 그림 2] 
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/7e93077c-4162-4360-88d8-8d4e93554968" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/64bc2eaf-9255-4b32-b5a3-2dbda3669e45" width="500" height="500"/>
- 2 -> 3 -> 1 -> 4 순대로 진행 

<br>

### 4-2. 소트 머지 조인 : 머지 소트를 활용한 조인 -> 대용량 & 비등가(non-equal)

> ### 👉 SGA 와 PGA
- [SGA / PGA]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0721756c-3ca2-4baa-81d1-d3089a4403e4" width="500" height="500"/>
- SGA : 공유 메모리 영역 
- PGA : 각 오라클 서버 프로세스에 할당된 메모리 영역, 개별 메모리 영역 
- PGA는 독립적인 메모리 공간, 래치 메커니즘이 불필요함. 따라서, 같은 양의 데이터를 읽더라고 SGA 버퍼캐시에서 읽을 때보다 빠름
  - 오라클에서는 동기화 매커니즘이 2가지가 있음
    - (1) 락 : 
    - (2) 래치 : 

<br>

> ### 👉 소트 머지 조인 -> 양쪽 컬럼 정렬
- [소트 머지 조인]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/f7e2761e-e9b5-472b-b72e-788087a8c356" width="500" height="500"/>
- use_merge(테이블 이름) 힌트 활용
- 소트 머지 조인의 특징 
  - (1) PGA 영역에 저장한 데이터를 이용, 해당 범위 내에서는 성능이 빠름
  - (2) 컬럼에 대한 인덱스 유무를 영향 안받음(NL은 영향 받음)
  - (3) 스캔 위주의 엑세스 방식을 사용함 

<br>

> ### 👉 소트 머지 조인이 빠른 이유?
- 소트 머지 조인은 Sort Area에 미리 정렬한 자료구조를 이용한다는 점만 다를 뿐 NL 조인과 같음
- 하지만, NL 조인은 '소량'에 유리, 소트 머지 조인은 '대량&비등가'에 유리
- 소트 머지 조인은 양쪽 테이블로 부터 조인 대상 집합을 '일괄적으로' 읽고 PGA에 저장한 후 조인함
  - 조인 대상 집합을 읽을 때는 DB 버퍼캐시를 경유함
- PGA는 프로세스만을 위한 독립적인 공간이므로 데이터를 읽을 때 래치나 락 과정이 없음

<br>

> ### 👉 소트 머지 조인의 주용도 -> (1) 대용량&비등가, (2) 조인 조건식 X

<br>

### 4-3. 해시 조인 : 해시 맵을 활용한 조인 -> 대용량 & 등가 

<br>

> ### 👉 내부적으로 해시 테이블(해시함수)를 활용하여 데이터 조회 
- 해시 조인은 NL 조인처럼 조인 과정에서 발생하는 랜덤 액세스 부하 x, 소트 머지 조인처럼 양쪽 집합을 미리 정렬하는 부하 x
- Build Input이 PGA 메모리에 담김, 즉 인메모리 해시 조인일 때 가장 효과적임
<br>

> ### 👉 해시 조인의 기본 메커니즘 
- [SQL 문]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/19cd871a-8739-45db-a977-689388bee9c5" width="500" height="500"/>

<br>

- [SQL 수행 과정]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/cc24dae1-71c1-46cd-a85e-04a9bd2be415" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/3ca794d3-96f0-4cd5-ad10-95e78d09db0e" width="500" height="500"/>

- (1) Build 단계 - 작은 쪽 테이블을 읽어 해시 테이블(해시맵) 생성
- (2) Probe 단계 - 큰 쪽 테이블을 읽어 해시 테이블을 탐색하면서 조인 

> ### 🧑🏻‍🏫 조인 메서드 선택 기준
> <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/a1a170a2-4474-4f83-b95e-bb007f3a3395" width="500" height="500"/>

<br>

### 4-4. 서브쿼리 조인 : 스칼라 인라인 뷰, 캐시 기능 활용 -> 

> ### 👉 오라클에서 서브쿼리 3 가지 분류 -> (1) 인라인 뷰, (2) 중첩된 서브쿼리, (3) 스칼라 서브쿼리
- [서브쿼리 종류]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/6a9ed24b-04aa-4e1a-b891-a70bc3c663b8" width="500" height="500"/>
- (1) 인라인 뷰 : FROM 절에 사용한 서브쿼리 
- (2) 중첩된 서브쿼리 : 결과집합을 한정하기 위해 WHERE 절에 사용한 서브쿼리 
- (3) 스칼라 서브쿼리 : 한 레코드당 정확히 하나의 값을 반환하는 서브쿼리 

<br>

> ### 👉 서브쿼리 Unnesting = 중첩문을 쪼개서 따로따로 돌린다 
- [서브쿼리 Unnesting]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/5b9cc8fc-972f-4464-877f-cb92ad86ea0a" width="500" height="500"/>
- Unnesting이란 중첩을 부정한다는 의미
- 서브쿼리 Unnesting은 메인과 서브쿼리 간의 계층 구조를 풀어 서로 같은 레벨(flat한 구조)로 만들어 준다를 의미함
  - 중첩문을 쪼개서 구분해서 돌림 
  - 서브쿼리 Flattening이라고도 함 

<br>

> ### 👉 서브쿼리 Pushing = 서브쿼리 필터링을 먼저 처리 
- [서브쿼리 Pushing]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/84644636-2f5c-40b7-a934-732c6127819d" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/35379dda-db23-4c30-832d-adcce6e19f6d" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/a4cfc100-f25a-4cae-8873-d206a280640e" width="500" height="500"/>
- 위에서는 서브쿼리 필터링을 먼저 처리시켜서 조인 단계로 넘어가는 로우 수를 크게 줄임, 성능 향상  
- 서브쿼리 Pushing은 서브쿼리 필터링을 가능한 한 앞 단계에서 처리하도록 강제하는 기능
  - push_subq / no_push_subq 힌트 사용
  - 이 기능은 Unnesting 되지 않는 서브쿼리에만 동작
  - push_subq는 항상 no_unnest와 같이 기술하는 것이 올바름 

<br>

> ### 👉 스칼라 서브쿼리 = 메인 쿼리 레코드마다 정확히 하나의 값만 반환, 처리 과정에서 캐싱 작용 활용하여 성능 향상 
- [스칼라 서브쿼리]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/d261e8af-0763-4a6a-ba05-9d19b41e1100" width="500" height="500"/>
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/9f51a31d-9d1c-4359-a818-37488e981f7d" width="500" height="500"/>
- 캐싱은 쿼리 단위로 이루어짐, 쿼리를 시작할 때 PGA 메모리에 공간을 할당하고 쿼리를 수행하면서 공간을 채워나감 

<br>

- 스칼라 서브 쿼리 캐싱 효과는 입력 값의 종류가 소수여서 해시 충동 가능성이 작을 때 효과가 있음
  - 캐시 공간은 늘 부족함 




<br>
<br>

## 📌 05. 소트 튜닝 : 어떻게하면 소트 처리를 효율적으로할까?

<br>

### 5-1. 소트 연산에 대한 이해 : 소트가 빨라봐야 O(n * logn) 부담이 가는 작업 

> ### 👉소트 처리되는 영역은 2가지 -> (1) Sort Area(PGA), (2) 디스크 
- [디스크 소트 과정]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/20fca723-f67b-431c-b48f-ab181a5f976c" width="500" height="500"/>
- 소트는 크게 2가지 영역에서 처리됨
  - (1) 메모리 소트(In-Memory Sort) : PGA에 할당한 Sort Area에서 이루어짐 
  - (2) 디스크 소트(To-Disk Sort) : Sort Area에서 정렬 완료 x -> 디스크 공간까지 사용 
- 소트 연산은 메모리 집약적이고 CPU 집약적임
- 소트 오퍼레이션 종류
  - (1) SORT AGGREGATE : 소트 x, 전체 로우를 대상으로 집계를 수행할 때 사용 -> sum, max, min, avg
  - (2) SORT ORDER BY : 실제 소트
  - (3) SORT GROUP BY : 소팅 알고리즘을 사용해 그룹별 집계를 수행할 때 사용 
  - (4) SORT UNIQUE : 
  - (5) SORT JOIN : 소트 머지 조인을 사용할 때 사용 
  - (6) WINDOW SORT : 윈도우 함수(분석 함수)를 수행할 때 사용 

<br>

### 5-2. 소트가 발생하지 않도록 SQL 작성 : Union -> Union All, Distinct -> Exist, Minus -> Not Exist, 인덱스 활용 
- SQL 에서 불필요한 소트가 발생하지 않도록 중의해야함 
  - (1)Union -> Union All
    - [Union All]
    - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b1c6e9ee-e157-4658-8158-a20f49151e1a" width="500" height="500"/>
    - Union의 경우, 옵티마이저는 상단과 하단 두 집합 간 중복을 제거하려고 소트 작업을 수행
    - 이를 피하기 위해 조건절과 Union All을 적절히 사용해야함 
    
  <br>

  - (2) Distinct -> Exist
    - [Exists]
    - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/4d2d9e4e-7df4-41af-9f8e-d9c0d0acc657" width="500" height="500"/>  
    - Distinct의 경우, 조건에 해당하는 데이터를 모두 읽어서 중복을 제거해야함. 모든 데이터를 읽는 과정에 많은 I/O가 발생

  <br>

  - (3) Minus -> Not Exist
    - [Exists]
    - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/db28087f-2136-41cb-8a9a-9b9f89c7012e" width="500" height="500"/>

  <br>

  - (4) 인덱스 활용(조인 방식 변경)
    - [조인 방식 변경]
    - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/c77e6cc7-410f-4f6b-9ded-23b70a8b5b3b" width="500" height="500"/>
    - 테이블의 컬럼 중 인덱스가 걸려 있는 컬럼에 NL 조인 유도 -> 소트 연산 생략 가능 

<br>

### 5-3. 인덱스를 이용한 소트 연산 생략 : 인덱스의 특징 활용 -> (1) 범위탐색, (2) 정렬  

<br>

> ### 👉 인덱스는 이진트리로 구성되어 있어서 정렬이 이미 되어 있음, Sort Order By 생략 가능 
- 인덱스 컬럼 위치가 중요
  - 인덱스 선두 컬럼을 [종목코드 + 거래일시] 순으로 구성하면 소트 연산 생략 가능 

- [쿼리문]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/59a43e70-a687-48d8-ab99-0090240d1870" width="500" height="500"/>

- 소트 연산을 생략할 수 없는 경우
- 인덱스 선두 컬럼을 [종목코드 + 거래일시] 순으로 구성해야함 

<br>

- [실행계획]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/877b543b-ef47-4704-ac14-e221e0b8b976" width="500" height="500"/>

<br>


> ### 👉 Top N 쿼리 : 전체 결과집합 중 상위 N개 레코드만 선택하는 쿼리 

<br>

- [쿼리문]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/33cb253b-c00d-40f5-90d5-622b3c18b709" width="500" height="500"/>

<br>

- [인덱스 활용]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/e24465c5-71be-4e71-bb76-2a5416cdda39" width="500" height="500"/>

- 서브쿼리를 활용해야함 
- 인라인 뷰로 정의한 집합을 모두 읽어 거래일시 순으로 정렬한 중간 집합을 우선 만들고, 거기서 상위 열 개 레코드를 취하는 형태
- Sort Order By -> COUNT(STOPKEY) 
  - COUNT(STOPKEY) : 조건절에 부합하는 레코드가 아무리 많아도 그 중 ROWNUM 으로 지정한 건수만큼 결과 레코드를 얻으면 거기서 멈춤 



### 5-4. Sort Area를 적게 사용하도록 SQL 작성 :

> ### 👉 소트 데이터 줄이기 : 시간복잡도 뿐만 아니라 공간복잡도도 최소화하는 것이 중요함

- [쿼리문]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/b5c0846c-ddca-41b4-86e7-8be3d62fe3b0" width="500" height="500"/>
- 1번은 레코드당 107 바이트로 가공한 결과집합을 Sort Area에 담음
- 2번은 가공하지 않은 상태로 정렬을 완료하고 나서 최종 출력할 때 가공 -> Sort Area를 훨씬 적게 사용함 


<br>

> ### 👉 Top N 쿼리의 소트 부하 경감 원리 : 삽입 정렬 활용 

<br>

> ### 👉 분석함수에서의 Top N 소트 : 최적화된 함수 사용 
- 윈도우 함수 : 복잡한 쿼리 대신해주는 함수 -> rank, row_number


<br>
<br>

## 📌 06. DML 튜닝 : INSERT/DELETE/UPDATE를 효율적으로 사용하는 방법

<br>

### 06-1. 기본 DML 튜닝

<br>

> ### 👉 DML 성능에 영향을 미치는 요인 -> 인덱스, 무결성 제약, 조건절, 서브쿼리, Redo 로깅, Undo 로깅, Lock, 커밋

- DML은 데이터 조작어를 의미함 크게 3가지가 있음
  - (1) INSERT
  - (2) DELETE
  - (3) UPDATE

<br>

- 테이블은 Freelist를 통해 입력 블록을 할당 받음. 즉, 중간에 비어있는 공간에 Insert
  - [Freeliist]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/e834ef98-b4ea-4600-84b7-b65579b74348" width="500" height="500"/>
- 인덱스가 DML 성능에 영향을 미침, 수직 탐색 -> 입력할 블록 찾음 
  - [인덱스 INSERT]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/7b0441c1-9453-4edc-a169-5c140221e336" width="500" height="500"/>

  <br>

  - [인덱스 UPDATE]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/15f96d5b-4a37-49da-99ce-44c0f29203b5" width="500" height="500"/>
  - 내부적으로 두개의 오퍼페이션 발생 -> '삽입', '삭제'
  

- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/bf748e46-4243-4d55-875f-2902546ebe60" width="500" height="500"/>

<br>

- PK, FK 제약 < Check, Not Null 제약
  - 제약은 내부적으로 if-else 문이 적용된다고 생각
  - PK, FK 제약이 내부적으로 더 많은 부분을 확인하고 걸러줘야함 

<br>

- Redo 로깅과 DML 성능
  - 데이터 파일과 컨트롤 파일에 가해지는 모든 변경사항을 Redo 로그에 기록
    - DB에 바로 반영x, 수행x, 작업을 기록(명령을 기록함), 즉 내가한 작업을 기록함 

<br>

- Redo 로그의 용도는 크게 3가지
  - [Redo 로그]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/8e6384fa-c178-46fc-be19-9a68b820ee0f" width="500" height="500"/>
  - (1) DB Recovery
  - (2) Cache Recovery 
  - (3) Fast Commit

<br>

- Undo 로그의 용도는 크게 3가지
  - [Undo 로그]
  - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/7f8534df-c1ec-4854-a508-5a9cf2734ec9" width="500" height="500"/>
  - (1) Transaction Rollback
  - (2) Transaction Recovery(TX 실패 -> Undo 처리)
  - (3) Read Consistency

<br>

- MVCC : Multi-Version Concurrency Control
  - 같은 목록 n개 복제해서 전달
  - 오라클은 데이터를 크게 2가지 모드로 읽음
    - (1) Current : 있는 그래도 읽음 -> 디스크에서 캐시로 적재된 원본 블록을 현재 상태 그대로 읽음
    - (2) Consistent : 일관되게 읽음 -> 쿼리가 시작된 이후에 다른 TX에 의해 변경된 블록을 만남, 원복 블록으로부터 복사본 블록 생성, 거기에 Undo 데이터 적용하여 쿼리가 '시작된 시점'으로 되돌려서 읽는 방법
    - [Isolation 그림]
    - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/19c78bb6-ffdc-4492-bbf6-0ae4165c7cc9" width="500" height="500"/>
  
  
<br>

- Lock
  - Lock은 DML 성능에 매우 크고 직접적인 영향을 미침
  - Lock 레벨은 크게 4개로 구분
    - (1) 로우 레벨 
    - (2) 블록 레벨
    - (3) 익스텐트 레벨
    - (4) 테이블 레벨
    
 - 기본이 로우 레벨, 테이블 레벨
 - 로우 레벨 락 걸리면, 테이블 레벨 락 걸림 -> DDL 수행하지 못하게 막으려고 

<br>

- DB 버퍼 캐시
  - 변경된 블록을 모아 주기적으로 데이터 파일(디스크)에 일괄 기록하는 작업은 DBWR 프로세스가 담당
    - 지연된 쓰기, 메모리는 바로 사용/ 디스크는 지연 쓰기
  
- Redo 로그 버퍼
  - Redo 로그에 기록 -> Redo 로그로 복구 가능
  - Redo 로그는 파일. 즉, 디스크 I/O 발생 -> 오라클은 로그 버퍼 사용 

<br>

- [트랜잭션 데이터 저장과정]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/87001b89-d7db-42a5-84db-5eeb6eda7810" width="500" height="500"/>

<br>

- [DB Call]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/0dac7b9a-237d-409b-b3d7-3ddcadaea828" width="500" height="500"/>

<br>

- 인덱스 및 제약 해제를 통한 대량 DML 튜닝
  - 대량 Insert -> '무결성 제약'과 '인덱스' 해제하여 빠르게 Insert
    - 예를들어서, 100만건을 Insert한다고 했을 때, 매번 재약확인하는 것은 성능이 매우 떨어지는 작업
    - 즉, '무결성 제약'과 '인덱스'를 잠시 해제해두었다가 빠르게 Insert 하기

<br>

- Merge 문
  - 변경분 데이터를 DW에 반영하는 과정
    - (1) 전일 발생한 변경 데이터를 가간계 시스템으로부터 추출
      - [참고 사진]
      - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/3570c08c-09d2-4d96-a04e-bd8c5313aafa" width="500" height="500"/>

    - (2) CUSTOMER_DELTA(difference) 테이블을 DW 시스템으로 전송
      - [참고 사진]
      - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/cf14289b-3ca8-4788-82ab-0855c7c4f9df" width="500" height="500"/>
      - <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/59966b75-a876-48a3-b5a0-f2844b249b67" width="500" height="500"/>
  
  
<br>

### 06-2.  Direct Path I/O : append로 Insert 처리 

> ### 👉 Direct Path I/O는 캐시를 안거치고 바로 읽어오는 것


- [Direct Path I/O]
- <img src="https://github.com/jongheonleee/sql_tuning/assets/87258372/df4ccd9a-caba-4543-8a58-089bc93d8e37" width="500" height="500"/>

<br>

> ### 👉 일반적인 Insert 성능 떨어짐 -> Direct Path Insert(내부적으로 append로 Insert)

- [Insert 과정]
- <img src="" width="500" height="500"/>
- 캐싱, 로깅 처리가 발생하여 성능이 저하됨. 이를 해결하기 위해 나온 것이 'Direct Path Insert' -> append 로 데이터 추가 

<br>

- [Direct Path Insert 과정]
- <img src="" width="500" height="500"/>
- <img src="" width="500" height="500"/>
  - 1. Freelist 참조 x, HWM 뒤에 append로 붙임
  - 2. 블록을 버퍼캐시에서 탐색 x
  - 3. 버퍼캐시에 적재 x, 데이터 파일에 직접 기록
  - 4. Undo 로깅 x
  - 5. Redo 로깅 안 하게 할 수 있음

<br>

### 06-3.  파티션을 활용한 DML 튜닝 : 각 디스크 별로 테이블을 적절히 분리

- [파티셔닝]
- <img src="" width="500" height="500"/>
- 파티셔닝 : 테이블/인덱스 데이터를 특정 컬럼 값에 따라 별도 세그먼트에 나눠서 저장하는 것 

<br>

- 테이블을 분할하는 방법은 크게 2가지
  - [테이블 분할]
  - <img src="" width="500" height="500"/>
  - (1) 수평 분할 : 로우 분할
  - (2) 수직 분할 : 칼럼 분할 
  
<br>

- 파티셔닝이 필요한 이유
  - (1) 관리적 측면 : 파티션 단위 백업, 추가, 삭제 변경 -> 가용성 향상
  - (2) 성능적 측면 : 파티션 단위 조회 및 DML, 경합 또는 부하 분산 

<br>

- [해시 파티션]
- <img src="" width="500" height="500"/>

<br>

- [리스트 파티션]
- <img src="" width="500" height="500"/>

<br>

- [인덱스 파티션]
- <img src="" width="500" height="500"/>


<br>

### 06-4. Lock과 트랜잭션 동시성 제어

<br>

- 오라클에서 제공하는 여러 종류의 Lock -> 동시성 환경에서 데이터를 보호할 목적으로 사용 
  - DML Lock : 로우 작업
  - DDL Lock : 테이블 변경
  - 래치(latch) : 가벼운 락
  - 버퍼 Lock 
  - 캐시 Lock/Pin
  
<br>

- Lock은 크게 3가지로 구분
  - (1) 공유락
  - (2) 배타락 : R 공유 W 배타적  
  - (3) 낙관적인 락 

<br>

- <img src="" width="500" height="500"/>
- <img src="" width="500" height="500"/>
- <img src="" width="500" height="500"/>
- <img src="" width="500" height="500"/>

<br>

- 채번 방식에 따른 INSERT 성능 비교
  - (1) 채번 테이블 : 테이블에 번호를 저장함(시퀀스와 유사), 사용자가 만들어서 쓰는 법
    - [참고 그림]
    - <img src="" width="500" height="500"/>
  - (2) 시퀀스 오브젝트 : 오라클에서 제공하는 시스템, 안전함. 중간에 데이터 빠지는 경우 X
    - [참고 그림]
    - <img src="" width="500" height="500"/>
  - (3) Max + 1 조회 : 성능이 가장 떨어지는 방식
    - [참고 그림]
    - <img src="" width="500" height="500"/>

<br>

- [채번 방식 정리]
- <img src="" width="500" height="500"/>
- <img src="" width="500" height="500"/>




## 📌 07. SQL 옵티마이저 : 

<br>
<br>
