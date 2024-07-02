# Create Table
![shopping](https://github.com/secgyu/Database/blob/main/WebShoppingDB/WebShoppingDB.png)

1️⃣
> 주키 및 두 개의 외래 키 선언을 포함하여 OrderInfo 테이블을 생성하기 위한
  Create Table 문장을 작성하여라. 각 애트리뷰트의 의미에 맞게 타입을 적절하게 선택해야 한다.
```sql
CREATE TABLE Company (
    NAME VARCHAR2(100) PRIMARY KEY,
    address VARCHAR2(200),
    netWorth NUMBER(15, 2),
    President VARCHAR2(100),
    CONSTRAINT fk_president FOREIGN KEY (President) REFERENCES Member(NAME)
);
```

2️⃣
> OrderInfo 테이블에 배송을 담당하는 택배사인 shippingCompany라는 애트리뷰트를 추가한다고 하자.
  OrderInfo 테이블의 운영 시 문제가 발생하는가? 그런 경우 해결책을 제시하여라.

- **데이터 중복 :** 같은 주문 번호(OrderNo)와 제품 번호(pNo)에 대해 여러 번의 배송 회사(shippingCompany)가 기록될 수 있다.
- **데이터 무결성 :** shippingCompany가 여러 번 기록될 경우, 배송 정보를 관리하기 어려울 수 있다.

**해결책**
- **shippingCompany를 Order 테이불에 추가** : 주문마다 한 번만 기록되도록 한다.
- **중복 데이터를 피하기 위해 트리거 사용** : 동일한 OrderNo와 pNo가 중복되지 않도록 트리거 설정한다.

3️⃣
> "주문 날짜는 회원의 가입 날짜 이후여야 한다"를 Order 테이블의 CHECK 제약으로 작성하여라.
```sql
ALTER TABLE "Order"
ADD CONSTRAINT chk_order_date
CHECK (orderDate >= (SELECT joinDate FROM Member WHERE Member.ID = "Order".memberID));
```

4️⃣
> "각 주문의 배송지와 회원 주소는 항상 일치해야 한다"를 CHECK 제약으로 Order 테이블에 작성하여라.
```sql
ALTER TABLE "Order"
ADD CONSTRAINT chk_shipping_address
CHECK (shippingAddr = (SELECT address FROM Member WHERE Member.ID = "Order".memberID));
```

5️⃣
> "주문된 각 상품에 대해 평균 상품 수량은 모두 5개 이상이 되어야 한다"라는 제약을 CHECK와 ASSERTION으로 각각 선언하여라. 두 가지 선언이 이 제약을 완벽하게 보장하는가? 그렇지 않은 경우 그 이유를 설명하여라.

```sql
ALTER TABLE OrderInfo
ADD CONSTRAINT chk_avg_count CHECK (count >= 5);
```
```sql
CREATE ASSERTION avg_count_check
CHECK (
    (SELECT AVG(count) FROM OrderInfo) >= 5
);
```
- **CHECK** : CHECK 제약 조건은 개별 행의 조건을 검사하므로, 전체 평균을 보장하지 않는다.
- **ASSERTION** : ASSERTION은 대부분의 DBMS에서 지원되지 않으므로, 대신 트리거나 프로그램 로직사용을 해야한다.
  
