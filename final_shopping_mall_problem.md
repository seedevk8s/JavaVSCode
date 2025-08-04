# 온라인 쇼핑몰 시스템 구현 문제

## 📋 문제 개요
**Java를 이용하여 기본적인 온라인 쇼핑몰 관리 시스템을 구현하시오.**

**제한시간: 2-3시간**  
**난이도: 중급 (컬렉션, 예외처리, 상속 활용)**  
**대상: Java 기초 ~ 컬렉션 프레임워크까지 학습한 취준생**

---

## 🎯 요구사항 개요

### 핵심 기능
- ✅ **상품 관리**: 등록, 조회, 검색, 재고 관리
- ✅ **고객 관리**: 고객/관리자 등록, 정보 조회
- ✅ **장바구니**: 상품 추가, 수량 관리, 조회
- ✅ **주문 처리**: 장바구니 → 주문 → 재고 차감
- ✅ **통계 정보**: 매출, 상품 현황, 카테고리별 분석

### 제외된 복잡한 기능
- ❌ 적립금/포인트 시스템
- ❌ 회원 등급/할인 시스템  
- ❌ 결제 시스템
- ❌ 배송 관리

---

## 🏗️ 구현해야 할 클래스 구조

### 📦 패키지 구조
```
totallab/
├── model/          # 도메인 모델 클래스들
├── service/        # 비즈니스 로직 클래스
├── exception/      # 예외 처리 클래스들
└── main/           # 메인 실행 클래스
```

---

## 📋 상세 구현 요구사항

### A. 모델 클래스 (totallab.model 패키지)

#### 1. Person 추상 클래스 ⭐⭐⭐
```java
public abstract class Person {
    protected String id;     // 고유 ID
    protected String name;   // 이름
    protected String email;  // 이메일
    
    // 생성자, getter 메서드들
    public abstract String getUserType(); // 추상 메서드
    // toString() 재정의
}
```

**구현 포인트:**
- Customer와 Manager의 공통 부모 클래스
- 추상 메서드로 다형성 구현
- 캡슐화를 위한 protected 필드 사용

#### 2. Customer 클래스 (Person 상속) ⭐⭐
```java
public class Customer extends Person {
    private LocalDateTime joinDate; // 가입일
    
    // 생성자에서 가입일 자동 설정
    // getUserType() 구현 - "고객" 반환
    // toString()에서 가입일 포함하여 출력
}
```

#### 3. Manager 클래스 (Person 상속) ⭐⭐
```java
public class Manager extends Person {
    private String department; // 부서
    private int employeeId;   // 사번
    
    // 생성자, getter 메서드들
    // getUserType() 구현 - "관리자" 반환
    // toString()에서 부서, 사번 포함하여 출력
}
```

#### 4. Product 클래스 ⭐⭐⭐
```java
public class Product {
    private String productId;  // 상품 ID
    private String name;      // 상품명
    private int price;        // 가격
    private int stock;        // 재고
    private String category;  // 카테고리
    private double rating;    // 평점 (0.0 ~ 5.0)
    private int reviewCount;  // 리뷰 개수
    
    // 핵심 메서드들
    public void reduceStock(int quantity) throws InsufficientStockException
    public void addStock(int quantity)
    public boolean isInStock()
    public boolean isLowStock() // 재고 5개 이하 체크
    public void addReview(double newRating)
}
```

**중요한 비즈니스 로직:**
- `reduceStock()`: 재고 부족 시 예외 발생
- `isLowStock()`: 재고 5개 이하면 true
- `addReview()`: 평점 평균 자동 계산

#### 5. CartItem 클래스 ⭐⭐
```java
public class CartItem {
    private Product product;  // 상품
    private int quantity;    // 수량
    
    public void addQuantity(int amount)    // 수량 증가
    public int getTotalPrice()             // 총 가격 계산
    // toString()으로 상품정보와 총액 표시
}
```

#### 6. Order 클래스 ⭐⭐
```java
public class Order {
    private String orderId;              // 주문 ID
    private Customer customer;           // 주문 고객
    private ArrayList<CartItem> items;   // 주문 상품들
    private LocalDateTime orderDate;     // 주문일시
    private int totalAmount;            // 총 주문금액
    private String status;              // 주문 상태
    
    // 생성자에서 총 금액 자동 계산
    public void updateStatus(String newStatus)
}
```

---

### B. 예외 처리 클래스 (totallab.exception 패키지) ⭐⭐

#### 계층적 예외 구조
```java
ShopException (기본 예외)
├── CustomerNotFoundException
├── ProductNotFoundException  
└── InsufficientStockException
```

**각 예외 클래스 구현:**
```java
public class ShopException extends Exception {
    public ShopException(String message) { super(message); }
}

// 나머지 예외들도 동일한 패턴으로 구현
```

---

### C. 서비스 클래스 (totallab.service 패키지) ⭐⭐⭐⭐

#### ShoppingMall 클래스 (핵심 비즈니스 로직)
```java
public class ShoppingMall {
    private HashMap<String, Product> products;      // 상품 관리
    private HashMap<String, Customer> customers;    // 고객 관리  
    private HashMap<String, Manager> managers;      // 관리자 관리
    private ArrayList<Order> orders;               // 주문 내역
    private HashMap<String, ArrayList<CartItem>> carts; // 고객별 장바구니
    private String mallName;
    private int orderCounter; // 주문 번호 생성용
}
```

**필수 구현 메서드들:**

#### 🛍️ 상품 관리 메서드
```java
// 기본 CRUD
void addProduct(String productId, String name, int price, int stock, String category)
void displayAllProducts()
void displayAvailableProducts() // 재고 있는 상품만

// 검색 기능  
ArrayList<Product> searchProductsByName(String name)
ArrayList<Product> getProductsByCategory(String category)
ArrayList<Product> getLowStockProducts() // 재고 부족 상품
```

#### 👥 고객 관리 메서드
```java
void addCustomer(String id, String name, String email)
void addManager(String id, String name, String email, String department, int employeeId)
void displayCustomerOrders(String customerId) // 고객 주문 내역
```

#### 🛒 장바구니 관리 메서드
```java
void addToCart(String customerId, String productId, int quantity)
void displayCart(String customerId) // 장바구니 내용과 총액 표시
```

#### 📦 주문 처리 메서드
```java
void placeOrder(String customerId) throws ShopException
// 핵심 처리 로직:
// 1. 고객 존재 확인
// 2. 장바구니 비어있는지 확인  
// 3. 재고 확인 및 차감
// 4. 주문 생성 및 저장
// 5. 장바구니 비우기
```

#### 📊 통계 정보 메서드
```java
void displayMallStatistics()
// 출력 내용:
// - 총 상품 수, 구매 가능한 상품 수, 재고 부족 상품 수
// - 등록 고객 수, 총 주문 수, 총 매출
// - 카테고리별 상품 수
```

---

### D. UI 클래스 (totallab.main 패키지) ⭐⭐⭐

#### ShoppingMallSystem 클래스
**메뉴 구조:**
```
=== 메인 메뉴 ===
1. 상품 관리
2. 고객 관리  
3. 장바구니 관리
4. 주문 관리
5. 통계 정보
0. 종료
```

**서브메뉴 구조:**

##### 1. 상품 관리 서브메뉴
```
1. 상품 추가
2. 전체 상품 보기
3. 구매 가능한 상품 보기  
4. 상품 검색 (이름)
5. 카테고리별 상품 보기
6. 재고 부족 상품 보기
0. 메인 메뉴로 돌아가기
```

##### 2. 고객 관리 서브메뉴
```
1. 고객 등록
2. 관리자 등록
3. 고객 주문 내역 조회
0. 메인 메뉴로 돌아가기
```

##### 3. 장바구니 관리 서브메뉴
```
1. 장바구니에 상품 추가
2. 장바구니 보기
0. 메인 메뉴로 돌아가기
```

##### 4. 주문 관리 서브메뉴
```
1. 주문하기
2. 고객 주문 내역 보기
0. 메인 메뉴로 돌아가기
```

**초기 데이터 설정:**
```java
private void initializeData() {
    // 기본 상품 8개 이상 (다양한 카테고리)
    // 기본 고객 3명 이상
    // 기본 관리자 1명 이상
}
```

---

## ⚡ 핵심 비즈니스 로직 요구사항

### 1. 재고 관리 시스템
```java
// 주문 시 재고 자동 차감
try {
    for (CartItem item : cart) {
        item.getProduct().reduceStock(item.getQuantity());
    }
} catch (InsufficientStockException e) {
    throw new ShopException("주문 실패: " + e.getMessage());
}
```

### 2. 장바구니 시스템
- 동일 상품 추가 시 **수량 증가**
- 고객별 **독립적인 장바구니** 관리
- 장바구니 총액 **실시간 계산**

### 3. 주문 처리 플로우
```
장바구니 내용 확인 → 재고 확인 → 재고 차감 → 주문 생성 → 장바구니 비우기
```

### 4. 검색 기능
- **상품명 검색**: 부분 일치 (대소문자 무시)
- **카테고리 검색**: 정확 일치 (대소문자 무시)
- **재고 부족 상품**: 재고 5개 이하

---

## 🎯 구현 우선순위 및 시간 배분

### ⏰ 시간 배분 (총 3시간)
```
30분: 기본 모델 (Person, Customer, Manager, Product)
30분: 예외 클래스 + CartItem + Order  
90분: ShoppingMall 서비스 로직 (핵심)
30분: UI 시스템 + 테스트
```

### 📋 구현 체크리스트

#### Phase 1: 기반 구조 (1시간)
- [ ] Person 추상 클래스 완성
- [ ] Customer, Manager 상속 구현
- [ ] 4개 예외 클래스 완성
- [ ] Product 클래스 핵심 메서드 구현
- [ ] CartItem, Order 클래스 완성

#### Phase 2: 핵심 로직 (1시간)  
- [ ] ShoppingMall 기본 구조 완성
- [ ] 상품 관리 메서드들 구현
- [ ] 고객 등록 기능 구현
- [ ] 장바구니 기능 구현

#### Phase 3: 고급 기능 (1시간)
- [ ] 주문 처리 로직 완성
- [ ] 검색 기능들 구현  
- [ ] 통계 정보 기능 구현
- [ ] UI 시스템 완성

---

## ✅ 평가 기준

### 기본 구현 (70점)
- [ ] **클래스 설계**: 모든 필수 클래스 구현 (20점)
- [ ] **상속 활용**: Person 상속 구조 올바른 구현 (10점)  
- [ ] **예외 처리**: 계층적 예외 구조 및 적절한 활용 (10점)
- [ ] **컬렉션 활용**: HashMap, ArrayList 효과적 사용 (15점)
- [ ] **기본 CRUD**: 상품/고객 등록, 조회 기능 (15점)

### 비즈니스 로직 (20점)
- [ ] **재고 관리**: 주문 시 재고 차감 로직 (8점)
- [ ] **장바구니**: 수량 관리, 총액 계산 (7점)  
- [ ] **주문 처리**: 완전한 주문 플로우 구현 (5점)

### 코드 품질 (10점)
- [ ] **캡슐화**: private 필드, public 메서드 적절한 사용 (3점)
- [ ] **네이밍**: 의미있는 변수명, 메서드명 (3점)
- [ ] **가독성**: 적절한 주석, 코드 정렬 (4점)

---

## 🚨 주의사항

### 필수 준수사항
1. **패키지 구조** 정확히 준수
2. **예외 처리** 적절히 활용 (try-catch-throws)
3. **컬렉션 프레임워크** 효과적 사용
4. **객체지향 원칙** 준수 (캡슐화, 상속, 다형성)

### 구현 팁
1. **단계별 테스트**: 각 클래스 완성 후 즉시 테스트
2. **예외 상황 고려**: 존재하지 않는 ID, 재고 부족 등
3. **사용자 친화적 출력**: 천단위 콤마, 명확한 메시지
4. **메모리 효율성**: 방어적 복사, 적절한 자료구조 선택

### 디버깅 가이드
```java
// 간단한 테스트 메서드 예시
public static void quickTest() {
    try {
        ShoppingMall mall = new ShoppingMall("테스트몰");
        mall.addProduct("P001", "테스트상품", 10000, 5, "테스트");
        mall.addCustomer("C001", "테스트고객", "test@test.com");
        mall.addToCart("C001", "P001", 2);
        mall.displayCart("C001");
        mall.placeOrder("C001");
        System.out.println("✅ 기본 기능 테스트 성공!");
    } catch (Exception e) {
        System.out.println("❌ 오류: " + e.getMessage());
    }
}
```

---

## 📝 제출 항목

### 1. 소스 코드
- 모든 .java 파일 (패키지 구조 유지)
- 컴파일 오류 없이 실행 가능해야 함

### 2. 실행 결과
- 주요 기능별 실행 스크린샷 또는 콘솔 출력
- 전체 시나리오 테스트 결과

### 3. README 파일
```markdown
# 쇼핑몰 시스템 구현 보고서

## 구현한 주요 기능
- 상품 관리: ✅ 
- 고객 관리: ✅
- 장바구니: ✅  
- 주문 처리: ✅

## 사용한 기술
- 상속과 다형성: Person → Customer, Manager
- 예외 처리: 계층적 예외 구조
- 컬렉션: HashMap (빠른 검색), ArrayList (순서 보장)

## 특별히 신경쓴 부분
- 재고 관리의 정확성
- 사용자 친화적인 UI
- 예외 상황 처리

## 실행 방법
1. 컴파일: javac -d . *.java
2. 실행: java totallab.main.ShoppingMallSystem
```

---

## 🎉 성공 기준

### 최소 요구사항 (Pass)
- 모든 클래스 컴파일 성공
- 기본 상품 등록/조회 가능
- 장바구니 추가/조회 가능  
- 간단한 주문 처리 가능

### 우수 구현 (Excellent)
- 모든 검색 기능 정상 작동
- 예외 상황 완벽 처리
- 통계 정보 정확한 계산
- 직관적이고 안정적인 UI

**화이팅! 실무에서 통하는 개발 실력을 기르는 좋은 기회입니다! 🚀**
