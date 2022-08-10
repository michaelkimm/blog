<mark></mark>

<details>
<summary></summary>
<div markdown="1">
</div>
</details>


# **트랜잭션이란?**
### **정의**
데이터 베이스에서 하나의 논리적 기능을 수행하기 위한 작업 단위 (출처: [네이버 지식 백과](https://terms.naver.com/entry.naver?docId=3483740&cid=58439&categoryId=58439))

</br>

### **트랜잭션의 필요성**
- **데이터베이스 장애 대응**: 트랜잭션은 <mark>장애 발생 시 복구 단위</mark>로 사용된다. 장애가 일어나면 일련의 작업들이 모두 취소가 되거나 모두 실행이 되야한다. 일련의 작업들을 하나로 묶어주는 단위가 트랜잭션이다.
- **동시성 제어 단위**: 트랜잭션은 <mark>동시에 데이터 접근을 하는 작업을 서로 분리하는 단위</mark>다. 하나의 논리적 기능을 수행하는 쿼리들을 트랜잭션으로 묶으면 동시성 제어가 편리해진다. 일례로 DB 접근 시 데드락이 일어나면 트랜잭션 단위로 복구하여 DBMS를 정상 작동시킬 수 있다.

</br>
</br>


_그렇다면 논리적 기능을 수행하기 위한 작업 단위는 모두 트랜잭션이라고 말할 수 있을까요? 논리적 기능을 수행하지만 **에러가 생긴기는 작업 단위**면 의미가 있을까요_:question:

</br>

 트랜잭션은 논리적 기능을 수행하기 위한 작업 단위일 뿐 아니라 ACID 속성을 만족하는 개념입니다. **ACID 속성은 논리적 기능의 작업 단위를 보장할 뿐 아니라 에러에 대한 대처 기능 또한 내포**하죠. _ACID에 대해 분석해보며 두 기능을 실제로 보장하는지 알아봅시다._

>In the context of databases, a sequence of database operations that satisfies the ACID properties (which can be perceived as a single logical operation on the data) is called a transaction. [출처:Wikipedia](https://en.wikipedia.org/wiki/ACID)
     

</br>
</br>

# **트랜잭션의 성질(ACID)란?**
### **정의**
ACID는 <mark>에러와 사고로부터 DB의 데이터 무결성을 보장</mark>할 수 있는 데이터베이스 트랜잭션의 속성들입니다. 여기서 말하는 에러와 사고는 전원이 꺼지거나 프로그램이 종료되는 등 예기치 않은 사건를 뜻합니다. **여기서 말하는 데이터 무결성은 테이블 CREATE와 ALTER할 때 정해지는 속성**입니다!
> In computer science, ACID (atomicity, consistency, isolation, durability) is a set of properties of database transactions intended to guarantee data validity despite errors, power failures, and other mishaps. [출처:Wikipedia](https://en.wikipedia.org/wiki/ACID)

<br>

:warning:_주의: 처음 글을 읽으실 땐 정의만 읽으시는걸 권장합니다. 나머지 자세한 얘기는 트랜잭션에 대해 모두 이해한 뒤 읽으시는게 좋습니다:)_

<br>

## **1.원자성**
### **정의**
원자적인 트랜잭션은 indivisible 하거나 irreducible한 일련의 DB 작업입니다. 일련의 작업들이 모두 일어나거나 모두 일어나지 않습니다. [출처:WIKIPEDIA](https://en.wikipedia.org/wiki/Atomicity_(database_systems))
  - <mark>필요성</mark>: 원자성은 DB가 **부분적으로만 실행되고 마는 현상을 방지**합니다. 트랜잭션의 부분적 실행 완료(volataile한 작업이라 가정)은 전체 취소 보다 안좋은 현상이기 때문입니다. 부분적 실행 완료보단 전체 취소가 낫습니다.
  - <mark>CID와의 직교성</mark>: 원자성(Atomicity)는 일관성(Consistency), 고립성(Isolation)과 지속성(Durability)과 직교 관계 즉 Orthogonal 하지 않습니다. [출처:WIKIPEDIEA](https://en.wikipedia.org/wiki/Atomicity_(database_systems)#Orthogonality)
    - **일관성과의 관계**: 원자성이 보장이 안되면 계좌 이체 시 중간에 총액이 낮아진 채로 작업이 끝날 수 있습니다. **테이블의 데이터 무결성이 '총액의 동일성 보장'을 가지고 있다면 총액이 낮아진 현상은 문제**가 됩니다.
    - **고립성과의 관계**: 원자성은 고립성과 직교 관계가 아닙니다. 고립성은 데드락이 일어났을 때 원자성에 기대어 Rollback 기능을 수행하여 데이터 일관성을 유지하죠.
    - **지속성과의 관계**: 원자성이 보장돼야 지속성이 보장됩니다. 트랜잭션이 원자적이지 않다면 부분 성공한 작업들을 롤백할 수 없습니다.
  - <mark>구현</mark>: DBMS는 아래 세가지 방법으로 원자성을 구현합니다. **첫번째**는 테이블 접근시 트랜잭션이 lock을 사용하는 방법, **두번째**는 일련의 작업이 일어나기 전에 data의 copy를 얻는 방법이며(read-copy-update), **세번째**는 journaling(작업 로그를 남기는 방법)입니다. **벤더는 세가지 혹은 더 많은 방법을 조합하여 ACID를 지키려고 노력**합니다. [출처:WIKIPEDIA](https://en.wikipedia.org/wiki/Atomicity_(database_systems)#Implementation)


<br>

_**HOLY MOLY** 무슨말이야?_ :cry:
_아마 여러분의 머리속에 맴도는 생각일겁니다.
괜찮습니다. 정의만 기억하세요! 
트랜잭션에 대해 모두 이해한 뒤 다시 읽으면 모두 이해되실겁니다_:smile:

<br>

## **2.일관성**
### **정의**
일관성이란 트랜잭션이 데이터를 허용된 방식으로만 바꿔야 하는 성질을 의미한다. 데이터베이스에 작성된 모든 데이터는 **정의된 규칙에 따라 유효**해야 합니다. 즉 테이블이 생성될 때 CREATE 문과 ALTER 문의 <mark>무결성 제약 조건</mark>을 통해 명시된 규칙을 따라야 한다는 뜻입니다.

:star:_문제_

테이블의 무결성 제약 조건이 '금액 총합 유지'라고 가정해보죠. 트랜잭션 내에는 다음과 같은 일련의 쿼리가 있을겁니다.
1. A계좌에서 현금 인출
2. B계좌에 현금 입금



Q. **해당 트랜잭션은 항상 일관성이 유지될까요?** 
<details>
<summary>답변</summary>
<div markdown="1">
1번 쿼리만 끝난 상황은 트랜잭션 일관성이 유지되지 않는 상황입니다. 아래 트랜잭션 일관성 상태도의 불일치 상태의 데이터 베이스 상태죠. 즉 Consitency를 잘 지킨 트랜잭션은 트랜잭션 전후 범위에서 논의될 수 있습니다. 트랜잭선 수행 중일 때는 언제든 일관적이지 않은 상태가 될 수 있죠.
</div>
</details>

<br>

![트랜잭션 일관성 상태도](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrTRFY%2FbtqyZt3qRgv%2Fa6Lhh6N1iQPJSdnaFkbVsK%2Fimg.png)

<br>

## **3.고립성**
### **정의**

<br>

## **4.지속성**
### **정의**

</br>

# **트랜잭션 상태도**

![트랜잭션 상태도](https://t1.daumcdn.net/cfile/tistory/997656365AE1FCA40B)
  
</br>

# **동시성 제어**
### **정의?**
### **DBMS에서 동시성 제어가 필요한 이유?**

</br>

# **갱신손실**
### **정의?**
### **갱신 손실 문제가 왜 생길까?**
### **DBMS는 어떻게 갱신 손실 문제를 해결하나?**

<br>

# 락(Lock)
### **정의?**
### **락의 유형과 특징**

<br>

# 2단계 락킹
### 2단계 락킹이 무엇이고 왜 필요한지 설명해주세요.

<br>

# 데드락
### 정의?
### 데드락을 DBMS는 어떻게 해결하나요?
### 데드락을 방지하는 방법이 있을까요?
호연님께서 얘기하신 것
<br>

# 트랜잭션 동시 실행 문제
### 동시 실행 문제의 종류와 특징에 대해 설명해주세요.

<br>

표준을 알고 있는게 중요함. 그래야 구현체를 갈아끼울 수 있음

# 트랜잭션 고립 수준
### 트랜잭션 고립 수준은 왜 나뉘어질까요?
### 트랜잭션 고립 수준 종류에 대해 설명해주세요.
### 트랜잭션 고립 수준이 높으면 좋나요? 
- 왜 mysql은 Repeatable read를 default로 설정했을까요?
- 왜 oracle은 Read Committed를 default로 설정했을까요?

<br>

# 회복
### DB 회복 기능이 무엇이고 왜 필요한지 하드웨어 적인 특징과 함께 설명해주세요.
### REDO, UNDO 연산이 무엇이고 왜 구분되어 사용되지는지 설명해주세요.
### 즉시갱신과 지연갱신이 무엇인지 장단점과 함께 설명해주세요.
### 로그 기록을 남기는 방법의 단점과 해결 방안에 대해 설명해주세요.