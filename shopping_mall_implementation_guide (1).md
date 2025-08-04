# 온라인 쇼핑몰 시스템 구현 문제

## 📋 문제 개요
**Java를 이용하여 온라인 쇼핑몰 관리 시스템을 구현하시오.**

**제한시간: 3시간**  
**사용 기술: Java (컬렉션 프레임워크, 예외 처리, 상속, 추상 클래스)**

---

## 🎯 구현 요구사항

### 1. 패키지 구조
```
totallab/
├── model/          # 도메인 모델 클래스들
├── service/        # 비즈니스 로직 클래스
├── exception/      # 예외 처리 클래스들
└── main/           # 메인 실행 클래스
```

---

## 🏗️ 필수 구현 클래스 및 기능

### A. 모델 클래스 (totallab.model 패키지)

#### 1. Person 추상 클래스
- **필드**: id(String), name(String), email(String)
- **생성자**: 모든 필드를 매개변수로 받는 생성자
- **메서드**: 
  - 모든 필드의 getter 메서드
  - `abstract String getUserType()` - 추상 메서드
  - `toString()` 메서드 재정의
- **요구사항**: Customer와 Manager 클래스의 부모 클래스 역할

#### 2. Customer 클래스 (Person 상속)
- **추가 필드**: 
  - `int point` - 적립금
  - `String membershipLevel` - 회원 등급 (Bronze, Silver, Gold)
  - `LocalDateTime joinDate` - 가입일
- **생성자**: id, name, email을 받아 초기화 (point=0, membershipLevel="Bronze")
- **핵심 메서드**:
  - `void addPoint(int amount)` - 적립금 추가 및 등급 자동 업데이트
  - `boolean usePoint(int amount)` - 적립금 사용 (잔액 부족시 false 반환)
  - `double getDiscountRate()` - 등급별 할인율 반환 (Gold:10%, Silver:5%, Bronze:0%)
  - `getUserType()` 구현 - "고객" 반환
- **등급 승급 조건**: Bronze(기본) → Silver(50,000점 이상) → Gold(100,000점 이상)

#### 3. Manager 클래스 (Person 상속)
- **추가 필드**: `String department`, `int employeeId`
- **생성자**: 모든 필드를 매개변수로 받는 생성자
- **메서드**: 추가 필드의 getter 메서드, `getUserType()` 구현 - "관리자" 반환

#### 4. Product 클래스
- **필드**: 
  - `String productId`, `String name`, `int price`, `int stock`, `String category`
  - `double rating`, `int reviewCount` (초기값 0.0, 0)
- **핵심 메서드**:
  - `boolean reduceStock(int quantity)` - 재고 감소 (부족시 InsufficientStockException 발생)
  - `void addStock(int quantity)` - 재고 증가
  - `int getDiscountedPrice(double discountRate)` - 할인 적용된 가격 계산
  - `boolean isInStock()` - 재고 존재 여부
  - `boolean isLowStock()` - 재고 부족 여부 (5개 이하)
  - `void addReview(double newRating)` - 리뷰 추가 및 평점 업데이트

#### 5. CartItem 클래스
- **필드**: `Product product`, `int quantity`
- **메서드**:
  - `void addQuantity(int amount)` - 수량 증가
  - `int getTotalPrice(double discountRate)` - 할인 적용된 총 가격

#### 6. Order 클래스
- **필드**: 
  - `String orderId`, `Customer customer`, `ArrayList<CartItem> items`
  - `LocalDateTime orderDate`, `int totalAmount`, `int usedPoint`, `String status`
- **생성자**: 주문 생성시 총 금액 자동 계산 (할인 + 적립금 사용 반영)
- **메서드**: `void updateStatus(String newStatus)` - 주문 상태 변경

---

### B. 예외 처리 클래스 (totallab.exception 패키지)

#### 1. ShopException 클래스
- **상속**: Exception 클래스 상속
- **역할**: 쇼핑몰 시스템의 기본 예외 클래스

#### 2. 구체적 예외 클래스들 (모두 ShopException 상속)
- `CustomerNotFoundException` - 고객을 찾을 수 없을 때
- `ProductNotFoundException` - 상품을 찾을 수 없을 때  
- `InsufficientStockException` - 재고 부족 시

---

### C. 서비스 클래스 (totallab.service 패키지)

#### ShoppingMall 클래스
**필드**: 
- `HashMap<String, Product> products` - 상품 관리
- `HashMap<String, Customer> customers` - 고객 관리
- `HashMap<String, Manager> managers` - 관리자 관리
- `ArrayList<Order> orders` - 주문 내역
- `HashMap<String, ArrayList<CartItem>> carts` - 고객별 장바구니
- `String mallName`, `int orderCounter`

**필수 구현 메서드**:

1. **상품 관리**
   - `void addProduct(String productId, String name, int price, int stock, String category)`
   - `void displayAllProducts()` - 전체 상품 목록 출력
   - `void displayAvailableProducts()` - 구매 가능한 상품만 출력
   - `ArrayList<Product> searchProductsByName(String name)` - 상품명 검색
   - `ArrayList<Product> getProductsByCategory(String category)` - 카테고리별 조회
   - `ArrayList<Product> getLowStockProducts()` - 재고 부족 상품 조회

2. **고객 관리**
   - `void addCustomer(String id, String name, String email)`
   - `void addManager(String id, String name, String email, String department, int employeeId)`

3. **장바구니 관리**
   - `void addToCart(String customerId, String productId, int quantity)`
   - `void displayCart(String customerId)` - 장바구니 내용 출력 (할인율 적용하여 계산)

4. **주문 처리**
   - `void placeOrder(String customerId, int usePoint)` - 주문 처리
     - 재고 확인 및 차감
     - 적립금 사용 처리
     - 주문 금액의 1% 적립금 추가
     - 장바구니 비우기
   - `void displayCustomerOrders(String customerId)` - 고객 주문 내역 조회

5. **통계 정보**
   - `void displayMallStatistics()` - 쇼핑몰 통계 정보 출력

---

### D. 메인 클래스 (totallab.main 패키지)

#### ShoppingMallSystem 클래스
- **필드**: `ShoppingMall mall`, `Scanner scanner`
- **기능**: 콘솔 기반 사용자 인터페이스 제공
- **메뉴 구조**:
  ```
  === 메인 메뉴 ===
  1. 상품 관리
  2. 고객 관리  
  3. 장바구니 관리
  4. 주문 관리
  5. 통계 정보
  0. 종료
  ```

- **상세 메뉴 구조**:

#### 1. 상품 관리 메뉴
```
=== 상품 관리 ===
1. 상품 추가
2. 전체 상품 보기
3. 구매 가능한 상품 보기
4. 상품 검색 (이름)
5. 카테고리별 상품 보기
6. 재고 부족 상품 보기
0. 메인 메뉴로 돌아가기

상품 추가 선택 시 입력 항목:
- 상품 ID 입력
- 상품명 입력  
- 가격 입력
- 재고 수량 입력
- 카테고리 입력

검색 기능 선택 시:
- 검색할 상품명 키워드 입력
- 검색 결과 목록 출력
- "총 N개의 상품을 찾았습니다." 메시지

카테고리별 조회 선택 시:
- 검색할 카테고리명 입력
- 해당 카테고리 상품들 출력
```

#### 2. 고객 관리 메뉴  
```
=== 고객 관리 ===
1. 고객 등록
2. 관리자 등록
3. 고객 주문 내역 조회
0. 메인 메뉴로 돌아가기

고객 등록 선택 시 입력 항목:
- 고객 ID 입력
- 이름 입력
- 이메일 입력

관리자 등록 선택 시 입력 항목:
- 관리자 ID 입력
- 이름 입력
- 이메일 입력
- 부서 입력
- 사번 입력

주문 내역 조회 선택 시:
- 조회할 고객 ID 입력
- 해당 고객의 모든 주문 내역 출력
- 각 주문의 상품 상세 정보 출력
```

#### 3. 장바구니 관리 메뉴
```
=== 장바구니 관리 ===
1. 장바구니에 상품 추가
2. 장바구니 보기
0. 메인 메뉴로 돌아가기

장바구니 추가 선택 시 입력 항목:
- 고객 ID 입력
- 상품 ID 입력
- 구매 수량 입력

장바구니 보기 선택 시:
- 고객 ID 입력
- 장바구니 내용 출력 형식:
  * 회원등급 및 할인율 표시
  * 보유 적립금 표시
  * 각 상품별 원가/할인가 표시
  * 장바구니 총 합계 표시
```

#### 4. 주문 관리 메뉴
```
=== 주문 관리 ===
1. 주문하기
2. 고객 주문 내역 보기
0. 메인 메뉴로 돌아가기

주문하기 선택 시:
- 고객 ID 입력
- 현재 장바구니 내용 자동 표시
- 사용할 적립금 입력 (0 입력시 사용 안함)
- 주문 완료 후 다음 정보 출력:
  * 주문번호
  * 결제금액
  * 사용 적립금
  * 적립 포인트 (결제금액의 1%)
  * 현재 적립금 잔액
  * 현재 회원등급

주문 내역 보기 선택 시:
- 고객 ID 입력
- 해당 고객의 주문 목록 출력
- 각 주문별 상품 상세 정보 출력
```

#### 5. 통계 정보 메뉴
```
=== 통계 정보 ===
(선택 없이 바로 표시 후 메인메뉴 복귀)

출력 내용:
- 총 상품 수: N개
- 구매 가능한 상품: N개  
- 재고 부족 상품: N개
- 등록 고객 수: N명
- 총 주문 수: N건
- 총 매출: N원
- 카테고리별 상품 수:
  * 전자제품: N개
  * 컴퓨터: N개  
  * 의류: N개
  * 가방: N개
```

**초기 데이터 설정 메서드**: 
- 기본 상품 8개 이상 등록 (다양한 카테고리로 구성)
- 기본 고객 3명 이상 등록  
- 기본 관리자 1명 이상 등록

**입력 데이터 검증 요구사항**:
- 빈 문자열 입력 시 "모든 필드를 입력해주세요." 메시지
- 숫자 입력 오류 시 "숫자를 입력해주세요." 메시지  
- 존재하지 않는 ID 조회 시 적절한 예외 메시지
- 잘못된 메뉴 선택 시 "잘못된 선택입니다. 다시 입력해주세요." 메시지

**출력 형식 요구사항**:
- 가격은 천단위 콤마로 표시 (예: 1,500,000원)
- 날짜는 yyyy-MM-dd 형식으로 표시
- 주문 시간은 yyyy-MM-dd HH:mm 형식으로 표시
- 각 메뉴 구분을 위한 적절한 구분선(===) 사용
- 사용자 친화적인 안내 메시지 제공

---

## 🎯 핵심 비즈니스 로직 요구사항

### 1. 회원 등급 시스템
- **Bronze** (기본): 할인 없음
- **Silver** (50,000점 이상): 5% 할인
- **Gold** (100,000점 이상): 10% 할인

### 2. 적립금 시스템
- 주문 완료 시 결제 금액의 **1% 적립**
- 적립금으로 결제 가능
- 등급 승급은 적립금 누적 기준

### 3. 재고 관리
- 주문 시 재고 자동 차감
- 재고 부족 시 주문 불가
- 재고 5개 이하시 "재고부족" 상태 표시

### 4. 장바구니 기능
- 동일 상품 추가 시 수량 증가
- 고객별 독립적인 장바구니 관리
- 할인율 적용된 가격으로 표시

### 5. 주문 처리 플로우
1. 장바구니 내용 확인
2. 재고 확인 및 차감
3. 적립금 사용 처리
4. 총 결제 금액 계산 (할인 + 적립금 사용)
5. 적립금 추가 (결제 금액의 1%)
6. 장바구니 비우기
7. 주문 내역 저장

---

## ✅ 평가 기준

### 기본 구현 (60점)
- [ ] 모든 필수 클래스 구현 완료
- [ ] 상속과 추상 클래스 올바른 사용
- [ ] 예외 처리 체계 구축
- [ ] 기본 CRUD 기능 동작

### 비즈니스 로직 (25점)
- [ ] 회원 등급별 할인 시스템 정확한 구현
- [ ] 적립금 시스템 정확한 구현
- [ ] 재고 관리 로직 정확한 구현
- [ ] 주문 처리 플로우 완전한 구현

### 코드 품질 (10점)
- [ ] 적절한 캡슐화와 정보 은닉
- [ ] 의미있는 변수명과 메서드명
- [ ] 적절한 주석과 JavaDoc
- [ ] 코드 가독성

### 사용자 인터페이스 (5점)
- [ ] 직관적인 메뉴 구조
- [ ] 적절한 입력 검증
- [ ] 사용자 친화적인 출력 형식

---

## 🚨 주의사항

1. **패키지 구조를 정확히** 따를 것
2. **예외 처리를 적절히** 활용할 것
3. **컬렉션 프레임워크** (HashMap, ArrayList)를 효과적으로 사용할 것
4. **객체지향 원칙**을 준수할 것 (캡슐화, 상속, 다형성)
5. **메모리 효율성**을 고려할 것
6. **toString() 메서드**를 모든 모델 클래스에서 의미있게 구현할 것

---

## 📝 제출 항목

1. **소스 코드**: 모든 .java 파일
2. **실행 결과**: 주요 기능 동작 스크린샷 또는 출력 결과
3. **README 파일**: 
   - 프로그램 실행 방법
   - 구현한 주요 기능 설명
   - 특별히 신경쓴 부분 설명

---

## 💡 구현 팁

### 우선순위 전략
1. **기본 모델 클래스** 먼저 완성
2. **예외 처리** 빠르게 구현
3. **ShoppingMall 핵심 기능** 단계별 구현
4. **UI는 간단하게** 구현

### 테스트 방법
- 각 클래스 완성 후 즉시 간단한 테스트 실행
- main 메서드에서 기본 시나리오 테스트
- 예외 상황도 반드시 테스트

### 시간 관리
- **1시간**: 기본 모델 클래스들
- **1시간**: ShoppingMall 서비스 로직
- **30분**: Order 클래스와 주문 처리
- **30분**: UI 및 최종 테스트

**행운을 빕니다! 🚀**
