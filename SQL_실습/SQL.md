# SQL 서브쿼리

 SQL을 배우면서 가장 많이 막혔던 구간이 있다면 단연 **서브쿼리**다.
 이 글은 내가 직접 공부하면서 겪은 시행착오와 나만의 사고방식을 기록한 노트다.
 처음 SQL을 접하는 분들이 같은 곳에서 헤매지 않길 바라며 적는다.

---

## 들어가기 전에 — 우리가 쓸 데이터

이 글에서 사용하는 테이블은 **classicmodels** 라는 자동차 모형 판매 회사의 샘플 데이터셋이다.  
아래는 각 테이블의 주요 컬럼과 샘플 데이터다.

### 📦 `products` 테이블

| productCode | productName | productLine | MSRP |
|---|---|---|---|
| S10_1678 | 1969 Harley Davidson Ultimate Chopper | Motorcycles | 95.70 |
| S10_1949 | 1952 Alpine Renault 1300 | Classic Cars | 214.30 |
| S12_1099 | 1968 Ford Mustang | Classic Cars | 194.57 |
| S18_2625 | 1966 Shelby Cobra 427 S/C | Classic Cars | 98.43 |
| S24_3151 | Challenger 1971 | Vintage Cars | 74.60 |
| S32_1374 | 1997 BMW F650 ST | Motorcycles | 77.90 |
| S18_3482 | 1976 Ford Gran Torino | Classic Cars | 65.74 |

### 👥 `customers` 테이블

| customerNumber | customerName | country | creditLimit |
|---|---|---|---|
| 103 | Atelier graphique | France | 21000.00 |
| 112 | Signal Gift Stores | USA | 71800.00 |
| 114 | Australian Collectors, Co. | Australia | 117300.00 |
| 119 | La Rochelle Gifts | France | 118200.00 |
| 121 | Baane Mini Imports | Norway | 81700.00 |
| 124 | Mini Gifts Distributors Ltd. | USA | 210500.00 |
| 128 | Blauer See Auto, Co. | Germany | 59700.00 |

### 📋 `orders` 테이블

| orderNumber | orderDate | customerNumber |
|---|---|---|
| 10100 | 2003-01-06 | 363 |
| 10101 | 2003-01-09 | 128 |
| 10280 | 2004-08-17 | 112 |
| 10320 | 2004-11-01 | 114 |
| 10399 | 2004-12-30 | 103 |
| 10420 | 2005-05-29 | 124 |

### 🧾 `orderdetails` 테이블

| orderNumber | productCode | quantityOrdered | priceEach |
|---|---|---|---|
| 10100 | S18_1749 | 30 | 136.00 |
| 10100 | S18_2248 | 50 | 55.09 |
| 10101 | S18_1342 | 26 | 99.99 |
| 10280 | S10_1678 | 42 | 86.13 |
| 10320 | S12_1099 | 35 | 167.06 |
| 10399 | S18_2625 | 60 | 88.62 |

---

## PART A — 기본 집계 문제 (GROUP BY 감 잡기)

처음엔 쉬운 문제로 감을 잡아가면서 전체적인 흐름을 보는 게 중요하다.  
처음부터 서브쿼리 들이밀면 100% 멘붕온다. 기초부터 쌓자.

---

### Q01. productLine별 등록 제품 수

**문제 요구사항**
- `products` 테이블에서 제품 라인(productLine)별 총 제품 수를 구한다
- 별칭: `product_count`
- 정렬: `product_count` 내림차순

```sql
SELECT productLine, COUNT(*) AS product_count
FROM products
GROUP BY productLine
ORDER BY product_count DESC;
```

> **⚠️ 처음에 내가 한 실수**
>
> ```sql
> -- ❌ 잘못된 쿼리
> SELECT productLine, product_count
> FROM products
> GROUP BY productLine
> ORDER BY product_count DESC;
> ```
>
> `product_count`는 아직 존재하지 않는 별칭인데, SELECT 절에 그냥 넣으면 당연히 에러가 난다.  
> **별칭은 결과 헤더에 이름표를 붙이는 것**이지, 실제 컬럼이 아니다.  
> `COUNT(*) AS product_count` 처럼 집계함수와 함께 써야 비로소 만들어진다.

**실행 결과**

| productLine | product_count |
|---|---|
| Classic Cars | 38 |
| Vintage Cars | 24 |
| Motorcycles | 13 |
| Planes | 12 |
| Trucks and Buses | 11 |
| Ships | 9 |
| Trains | 3 |

---

### Q02. country별 고객 수

**문제 요구사항**
- `customers` 테이블에서 국가별 고객 수를 구한다
- 별칭: `customer_count`
- 정렬: `customer_count` 내림차순

내가 SQL 쿼리를 읽는 순서는 **FROM → GROUP BY → SELECT → ORDER BY** 순이다.  
실제 실행 순서와도 일치하기 때문에 이 방식으로 읽으면 로직이 자연스럽게 따라온다.

```sql
-- 3. 방을 만들었으니 이름표를 달고 데이터 개수 카운팅
SELECT country, COUNT(*) AS customer_count
-- 1. 테이블 정의: 여기서부터 시작
FROM customers
-- 2. country별로 방을 만들었다
GROUP BY country
-- 4. 내림차순 정렬로 마무리
ORDER BY customer_count DESC;
```

**실행 결과**

| country | customer_count |
|---|---|
| USA | 36 |
| Germany | 13 |
| France | 12 |
| Spain | 7 |
| Australia | 5 |
| UK | 5 |

---

### Q03. productLine별 평균 권장소비자가(MSRP)

**문제 요구사항**
- `products` 테이블에서 productLine별 평균 MSRP
- 별칭: `avg_msrp`, 소수점 둘째 자리 반올림
- 정렬: `avg_msrp` 내림차순

```sql
-- 3. productLine 방의 평균을 계산하고, 소수점 둘째 자리 반올림, 별칭 avg_msrp
SELECT productLine, ROUND(AVG(MSRP), 2) AS avg_msrp
-- 1. 테이블 정의
FROM products
-- 2. productLine별로 방을 만든다
GROUP BY productLine
-- 4. 내림차순 정렬
ORDER BY avg_msrp DESC;
```

> **💡 `ROUND(값, 자릿수)` 함수 정리**
>
> | 표현 | 의미 |
> |---|---|
> | `ROUND(AVG(MSRP), 0)` | 소수점 첫째 자리에서 반올림 → 정수 |
> | `ROUND(AVG(MSRP), 2)` | 소수점 셋째 자리에서 반올림 → 소수점 2자리 |
> | `ROUND(AVG(MSRP), -1)` | 일의 자리에서 반올림 → 십의 단위 |

**실행 결과**

| productLine | avg_msrp |
|---|---|
| Classic Cars | 127.91 |
| Vintage Cars | 98.36 |
| Motorcycles | 81.77 |
| Trucks and Buses | 80.96 |
| Ships | 78.26 |
| Planes | 75.47 |
| Trains | 58.53 |

---

### Q04. 2004년 총 주문 건수

**문제 요구사항**
- `orders` 테이블에서 2004년 주문 건수를 구한다
- `strftime('%Y', orderDate)` 로 연도 추출
- 별칭: `orders_in_2004`

```sql
-- 3. 카운팅하고 별칭 붙이기
SELECT COUNT(*) AS orders_in_2004
-- 1. 테이블 정의
FROM orders
-- 2. 조건이 있으니까 WHERE 사용, 연도 추출 함수 활용
WHERE strftime('%Y', orderDate) = '2004';
```

> **💡 `COUNT(*)` vs `COUNT(컬럼명)` 차이**
>
> - `COUNT(*)` → NULL 포함, 전체 행 수를 센다
> - `COUNT(컬럼명)` → 해당 컬럼에서 NULL인 행은 제외하고 센다
>
> 주문 건수처럼 **행 자체의 개수**를 세고 싶을 땐 `COUNT(*)`를 쓰는 게 안전하다.

**실행 결과**

| orders_in_2004 |
|---|
| 151 |

---

## PART B — JOIN 및 그룹 집계 (테이블 연결의 법칙)

> **⚡ 치트키**: 공통 컬럼명이 완전히 일치하면 `USING(컬럼명)`을 쓰면 빠르다!
>
> ```sql
> -- ON 방식
> JOIN orders o ON c.customerNumber = o.customerNumber
> -- USING 방식 (컬럼명이 같을 때만)
> JOIN orders o USING(customerNumber)
> ```

---

### Q05. 국가별 총 주문 금액 TOP 10

**문제 요구사항**
- `customers`, `orders`, `orderdetails` 3개 테이블 조인
- 국가별 총 매출 (`quantityOrdered * priceEach` 합계)
- 별칭: `total_revenue` (소수점 둘째 자리 반올림)
- 상위 10개만 출력

**사고 흐름**

> 1. "국가별"이라는 말이 나왔다 → `country` 컬럼이 있는 테이블이 `customers`이므로, 이게 **주인공(FROM)** 이다
> 2. 매출을 계산하려면 `orderdetails`가 필요 → 중간에 `orders` 테이블로 다리를 놔야 한다
> 3. `customers → orders → orderdetails` 순서로 이어 붙인다

```sql
SELECT c.country, ROUND(SUM(od.quantityOrdered * od.priceEach), 2) AS total_revenue
-- 1. 국가 컬럼은 customers에만 있으므로 기준점(주인공)
FROM customers c
-- 3. customers → orders 연결 (customerNumber 공통)
JOIN orders o USING(customerNumber)
-- 4. orders → orderdetails 연결 (orderNumber 공통)
JOIN orderdetails od USING(orderNumber)
-- 2. 국가별 그룹핑 (c 테이블이 주인공이라 c.country)
GROUP BY c.country
ORDER BY total_revenue DESC
LIMIT 10;
```

> **⚠️ JOIN 순서 헷갈렸던 점**
>
> `FROM` 바로 뒤에 오는 테이블이 이 쿼리의 **주인공**이다.  
> `JOIN`은 그 주인공에다가 새 테이블을 붙이는 것이다.  
> 그래서 `JOIN customers c ON c.customerNumber...` 이런 식으로 FROM에 이미 있는 테이블을 다시 JOIN하는 건 잘못된 것!

**실행 결과**

| country | total_revenue |
|---|---|
| USA | 3627982.83 |
| Spain | 1215686.92 |
| France | 1110916.52 |
| Australia | 630623.10 |
| New Zealand | 597287.68 |
| Germany | 754284.82 |
| UK | 478880.46 |
| Singapore | 288488.41 |
| Canada | 224078.56 |
| Denmark | 245637.15 |

---

### Q06. productLine별 매출 및 판매 수량

**문제 요구사항**
- `products`, `orderdetails` 조인 (`productCode` 공통)
- productLine별 총 매출액 (`total_revenue`) + 총 판매 수량 (`total_qty`)

```sql
SELECT p.productLine,
       ROUND(SUM(od.quantityOrdered * od.priceEach), 2) AS total_revenue,
       SUM(od.quantityOrdered) AS total_qty
-- 1. productLine 컬럼은 products에만 있으므로 주인공
FROM products p
JOIN orderdetails od USING(productCode)
GROUP BY p.productLine
ORDER BY total_revenue DESC;
```

**실행 결과**

| productLine | total_revenue | total_qty |
|---|---|---|
| Classic Cars | 3853922.49 | 33992 |
| Vintage Cars | 1797559.63 | 21069 |
| Motorcycles | 1121426.12 | 11663 |
| Trucks and Buses | 1024113.57 | 10777 |
| Planes | 954637.54 | 10727 |
| Ships | 663998.34 | 8127 |
| Trains | 188532.92 | 2712 |

---

### Q08. 지역(territory)별 고유 고객, 주문, 매출 요약

이 문제가 처음엔 제일 막막했다. 테이블이 **5개**나 연결되어야 해서.

**테이블 연결고리**

```
offices ─── employees ─── customers ─── orders ─── orderdetails
      officeCode       salesRepEmployeeNumber  customerNumber  orderNumber
```

**사고 흐름: 체인처럼 이어 붙이자**

```sql
SELECT o.territory,
       COUNT(DISTINCT c.customerNumber) AS customer_count,
       COUNT(DISTINCT ord.orderNumber)  AS order_count,
       ROUND(SUM(od.quantityOrdered * od.priceEach), 2) AS total_revenue
-- 1단계: 지역(territory)이 있는 offices가 출발점
FROM offices o
-- 2단계: offices ↔ employees (officeCode 공통)
JOIN employees e  ON o.officeCode = e.officeCode
-- 3단계: employees ↔ customers (employeeNumber ↔ salesRepEmployeeNumber)
JOIN customers c  ON e.employeeNumber = c.salesRepEmployeeNumber
-- 4단계: customers ↔ orders (컬럼명 일치 → USING 가능)
JOIN orders ord   USING(customerNumber)
-- 5단계: orders ↔ orderdetails (컬럼명 일치 → USING 가능)
JOIN orderdetails od USING(orderNumber)
GROUP BY o.territory
ORDER BY total_revenue DESC;
```

> **💡 COUNT(DISTINCT ...) 를 써야 하는 이유**
>
> 한 고객이 주문을 여러 번 할 수 있기 때문에 단순 `COUNT(customerNumber)`를 하면 주문 건수만큼 고객이 중복으로 잡힌다.  
> 순수하게 **몇 명의 고객이 있는지**를 세려면 `COUNT(DISTINCT customerNumber)` 로 중복을 제거해야 한다.

**실행 결과**

| territory | customer_count | order_count | total_revenue |
|---|---|---|---|
| NA | 56 | 273 | 3627982.83 |
| EMEA | 63 | 296 | 2980656.16 |
| APAC | 24 | 112 | 1261887.98 |
| Japan | 7 | 37 | 188167.81 |

---

## PART C — 서브쿼리 (드디어 본론)

여기서부터 SQL이 진짜 강력해지는 구간이다.  
서브쿼리는 **쿼리 안에 또 다른 쿼리**를 넣는 것인데, 처음엔 이게 왜 필요한지 감이 안 올 수 있다.

**핵심 개념**: 서브쿼리가 필요한 순간은 **"비교할 기준값 자체를 쿼리로 직접 구해야 할 때"** 다.

---

### Q10. 평균 신용한도 초과 고객 조회 (WHERE 서브쿼리)

**문제 요구사항**
- `customers` 테이블에서 전체 평균 `creditLimit`보다 높은 고객만 필터링
- 정렬: `creditLimit` 내림차순

**왜 서브쿼리가 필요한가?**

```sql
-- ❌ 이렇게 하고 싶은데...
WHERE creditLimit > 평균값
```
그런데 "평균값"이 얼마인지 모른다. 그걸 직접 구해야 한다.  
→ `WHERE` 절 안에 `AVG`를 계산하는 쿼리를 **괄호로 감싸서** 넣는다.

```sql
-- 3. 조건을 만족하는 고객의 정보를 조회
SELECT customerNumber, customerName, creditLimit
-- 1. 전체 고객 명단 테이블
FROM customers
-- 2. 서브쿼리: 전체 평균 creditLimit을 동적으로 계산
WHERE creditLimit > (
    SELECT AVG(creditLimit)   -- 전체 평균을 직접 계산
    FROM customers            -- 같은 테이블이어도 괜찮다
)
ORDER BY creditLimit DESC;
```

> **💡 서브쿼리의 실행 순서**
>
> SQL은 먼저 괄호 안의 서브쿼리를 실행해서 하나의 값(예: `67,659.00`)을 구한다.  
> 그 다음 바깥 쿼리가 `WHERE creditLimit > 67659.00` 처럼 동작한다.  
> 즉, 서브쿼리는 **값을 미리 계산해서 넣어주는 플레이스홀더** 역할을 한다.

**실행 결과** (상위 일부)

| customerNumber | customerName | creditLimit |
|---|---|---|
| 141 | Euro+ Shopping Channel | 227600.00 |
| 124 | Mini Gifts Distributors Ltd. | 210500.00 |
| 298 | Vida Sport, Ltd | 141300.00 |
| 151 | Muscle Machine Inc | 138500.00 |
| 114 | Australian Collectors, Co. | 117300.00 |
| 119 | La Rochelle Gifts | 118200.00 |

---

## 마치며 — 나만의 SQL 학습 3원칙

공부하면서 가장 효과적이었던 방법들을 정리하면 이렇다.

### 1. FROM → GROUP BY → SELECT → ORDER BY 순서로 읽자
SQL은 작성 순서(SELECT가 먼저)와 실행 순서가 다르다.  
**FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY** 순으로 실제 실행된다.  
쿼리를 읽을 때도 이 순서로 읽으면 로직이 훨씬 자연스럽게 이해된다.

### 2. JOIN은 "주인공에 붙인다"고 생각하자
`FROM` 뒤에 오는 테이블이 주인공이고, `JOIN`은 그 주인공에 새 테이블을 이어 붙이는 것이다.  
체인처럼 하나씩 연결한다고 생각하면 5개 테이블 JOIN도 무서울 게 없다.

### 3. 서브쿼리는 "비교 기준값을 직접 계산할 때" 쓴다
평균, 최대값, 최소값 등 **동적으로 구해야 하는 기준**이 있을 때 서브쿼리를 쓴다.  
먼저 서브쿼리 부분만 따로 실행해보고, 값이 제대로 나오면 바깥 쿼리에 붙이는 방식으로 디버깅하면 편하다.

---

*이 글이 SQL 서브쿼리에서 헤매고 있는 누군가에게 도움이 되길 바란다.*  
*더 궁금한 점이나 피드백은 댓글로 남겨주세요!* 🙂
