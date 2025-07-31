# 온라인 쇼핑몰 관리 시스템 구현 가이드 (2시간)

## 📋 개요
이 문서는 Java를 이용한 온라인 쇼핑몰 관리 시스템을 2시간 내에 효율적으로 구현하기 위한 단계별 가이드입니다.

**예상 소요 시간**: 2시간  
**난이도**: 중급  
**주요 개념**: 객체지향, 컬렉션, 상속, 다형성, 예외처리, 계산 로직

---

## 🎯 학습 목표

이 프로젝트를 완성하면 다음을 실습할 수 있습니다:
- ✅ **상속과 다형성**: Person → Customer/Manager
- ✅ **비즈니스 로직**: 할인 시스템, 적립금, 장바구니
- ✅ **컬렉션 활용**: HashMap, ArrayList를 이용한 데이터 관리
- ✅ **예외 처리**: 사용자 정의 예외 계층 구조
- ✅ **실무 로직**: 실제 쇼핑몰과 유사한 비즈니스 프로세스

---

## 🚀 구현 순서

### 1단계: 프로젝트 구조 설계 (10분)

#### 📁 패키지 구조
```
ShoppingMallSystem/
├── src/
│   ├── exception/                    # 예외 클래스들 (4개)
│   │   ├── ShopException.java
│   │   ├── InsufficientStockException.java
│   │   ├── CustomerNotFoundException.java
│   │   └── ProductNotFoundException.java
│   ├── model/                        # 데이터 모델 클래스들 (6개)
│   │   ├── Person.java               # 추상 부모 클래스
│   │   ├── Customer.java             # 고객 클래스 (등급, 적립금)
│   │   ├── Manager.java              # 관리자 클래스
│   │   ├── Product.java              # 상품 클래스 (재고, 할인)
│   │   ├── CartItem.java             # 장바구니 아이템
│   │   └── Order.java                # 주문 클래스
│   ├── service/                      # 비즈니스 로직 (1개)
│   │   └── ShoppingMall.java         # 핵심 서비스 클래스
│   └── main/                         # 실행 및 UI (1개)
│       └── ShoppingMallSystem.java   # 메인 실행 클래스
└── README.md
```

#### 📝 사전 계획
- [ ] 핵심 엔티티 파악: 상품, 고객, 주문, 장바구니
- [ ] 주요 기능 나열: 상품관리, 장바구니, 주문처리, 할인시스템
- [ ] 데이터 구조 설계: HashMap(빠른 검색), ArrayList(순서 유지)

---

### 2단계: 예외 클래스 구현 (10분)

구현 순서:
1. `ShopException` (부모 예외)
2. `InsufficientStockException` (재고 부족)
3. `CustomerNotFoundException` (고객 없음)
4. `ProductNotFoundException` (상품 없음)

```java
// 예시: ShopException.java
package exception;

public class ShopException extends Exception {
    public ShopException(String message) {
        super(message);
    }
}
```

**💡 중요한 이유**: 
- 비즈니스 예외 상황을 명확하게 구분
- 상속 계층으로 예외 관리 효율화

---

### 3단계: 기본 모델 클래스 구현 (25분)

구현 순서:
1. `Person` 추상클래스 (5분)
2. `Customer` 클래스 (10분) ⭐
3. `Manager` 클래스 (5분)
4. `Product` 클래스 (5분) ⭐

#### 핵심 포인트

**Person 클래스**:
```java
public abstract class Person {
    protected String id, name, email;
    public abstract String getUserType();
}
```

**Customer 클래스 (가장 중요)**:
- 적립금 관리: `addPoint()`, `usePoint()`
- 회원 등급 시스템: Bronze → Silver → Gold
- 할인율 계산: `getDiscountRate()`

**Product 클래스**:
- 재고 관리: `reduceStock()`, `addStock()`
- 할인 가격 계산: `getDiscountedPrice()`
- 상태 확인: `isInStock()`, `isLowStock()`

---

### 4단계: 장바구니 및 주문 클래스 (15분)

구현 순서:
1. `CartItem` 클래스 (5분)
2. `Order` 클래스 (10분)

#### CartItem 핵심 기능
```java
public class CartItem {
    private Product product;
    private int quantity;
    
    // 할인 적용된 총 가격 계산
    public int getTotalPrice(double discountRate) {
        return product.getDiscountedPrice(discountRate) * quantity;
    }
}
```

#### Order 핵심 기능
- 총 금액 자동 계산 (할인 + 적립금 사용 반영)
- 주문 상태 관리
- 주문 일시 자동 설정

---

### 5단계: 서비스 클래스 기본 구조 (15분)

#### ShoppingMall 클래스 골격
```java
public class ShoppingMall {
    private HashMap<String, Product> products;
    private HashMap<String, Customer> customers;
    private HashMap<String, ArrayList<CartItem>> carts; // 고객별 장바구니
    private ArrayList<Order> orders;
    private int orderCounter = 1;
    
    // 생성자에서 HashMap들 초기화
    public ShoppingMall(String mallName) {
        this.products = new HashMap<>();
        this.customers = new HashMap<>();
        this.carts = new HashMap<>();
        this.orders = new ArrayList<>();
    }
}
```

**💡 설계 포인트**:
- HashMap으로 ID 기반 빠른 검색
- 고객별 장바구니를 별도 HashMap으로 관리
- 주문 번호 자동 생성을 위한 카운터

---

### 6단계: 기본 데이터 관리 기능 (20분)

구현 순서:
1. `addProduct()` - 상품 추가 (5분)
2. `addCustomer()` - 고객 등록 (5분)
3. `addManager()` - 관리자 등록 (3분)
4. `displayAllProducts()` - 상품 목록 (2분)
5. `displayAvailableProducts()` - 구매 가능 상품 (2분)
6. 검색 기능들 (3분)

#### 구현 체크리스트
- [ ] 중복 ID 검증 (`HashMap.containsKey()`)
- [ ] 고객 등록 시 빈 장바구니 자동 생성
- [ ] 적절한 예외 처리
- [ ] 성공 메시지 출력

```java
public void addCustomer(String id, String name, String email) throws ShopException {
    if (customers.containsKey(id)) {
        throw new ShopException("이미 존재하는 고객 ID입니다: " + id);
    }
    
    Customer customer = new Customer(id, name, email);
    customers.put(id, customer);
    carts.put(id, new ArrayList<>()); // 빈 장바구니 생성
    System.out.println("고객이 등록되었습니다: " + name);
}
```

---

### 7단계: 장바구니 기능 구현 (15분)

구현 순서:
1. `addToCart()` - 장바구니에 상품 추가 (10분) ⭐
2. `displayCart()` - 장바구니 내용 출력 (5분)

#### 장바구니 추가 로직
```java
public void addToCart(String customerId, String productId, int quantity) 
        throws CustomerNotFoundException, ProductNotFoundException {
    // 1. 고객/상품 존재 확인
    Customer customer = customers.get(customerId);
    Product product = products.get(productId);
    
    // 2. 장바구니에서 기존 상품 찾기
    ArrayList<CartItem> cart = carts.get(customerId);
    CartItem existingItem = null;
    
    for (CartItem item : cart) {
        if (item.getProduct().getProductId().equals(productId)) {
            existingItem = item;
            break;
        }
    }
    
    // 3. 기존 상품이 있으면 수량 증가, 없으면 새로 추가
    if (existingItem != null) {
        existingItem.addQuantity(quantity);
    } else {
        cart.add(new CartItem(product, quantity));
    }
}
```

#### 장바구니 출력 기능
- 회원 등급과 할인율 표시
- 원가와 할인가 비교 표시
- 보유 적립금 표시
- 총 합계 계산

---

### 8단계: 핵심 주문 처리 로직 (25분)

**가장 중요한 단계** - 충분한 시간 할당

#### 주문 처리 플로우
```java
public void placeOrder(String customerId, int usePoint) throws ShopException {
    // 1. 고객 확인
    Customer customer = customers.get(customerId);
    
    // 2. 장바구니 확인
    ArrayList<CartItem> cart = carts.get(customerId);
    if (cart.isEmpty()) {
        throw new ShopException("장바구니가 비어있습니다.");
    }
    
    // 3. 적립금 사용 가능 여부 확인
    if (usePoint > customer.getPoint()) {
        throw new ShopException("사용 가능한 적립금을 초과했습니다.");
    }
    
    // 4. 재고 확인 및 차감
    for (CartItem item : cart) {
        item.getProduct().reduceStock(item.getQuantity());
    }
    
    // 5. 적립금 사용
    customer.usePoint(usePoint);
    
    // 6. 주문 생성
    String orderId = "ORD" + String.format("%04d", orderCounter++);
    Order order = new Order(orderId, customer, cart, usePoint);
    orders.add(order);
    
    // 7. 적립금 추가 (주문 금액의 1%)
    int earnedPoint = order.getTotalAmount() / 100;
    customer.addPoint(earnedPoint);
    
    // 8. 장바구니 비우기
    cart.clear();
}
```

#### 구현 체크리스트
- [ ] 모든 유효성 검사 (고객, 장바구니, 적립금)
- [ ] 재고 차감 및 예외 처리
- [ ] 주문 번호 자동 생성
- [ ] 적립금 사용 및 적립
- [ ] 회원 등급 자동 업데이트
- [ ] 장바구니 초기화

---

### 9단계: UI 클래스 기본 구조 (15분)

#### ShoppingMallSystem 클래스 구성
```java
public class ShoppingMallSystem {
    private ShoppingMall mall;
    private Scanner scanner;
    
    public ShoppingMallSystem() {
        this.mall = new ShoppingMall("자바 쇼핑몰");
        this.scanner = new Scanner(System.in);
        initializeData(); // 테스트용 데이터
    }
    
    public void run() {
        while (true) {
            displayMainMenu();
            int choice = Integer.parseInt(scanner.nextLine());
            
            switch (choice) {
                case 1: handleProductManagement(); break;
                case 2: handleCustomerManagement(); break;
                case 3: handleCartManagement(); break;
                case 4: handleOrderManagement(); break;
                case 5: mall.displayMallStatistics(); break;
                case 0: return;
            }
        }
    }
}
```

#### 초기 데이터 설정
```java
private void initializeData() {
    try {
        // 다양한 가격대의 상품들
        mall.addProduct("P001", "갤럭시 스마트폰", 800000, 10, "전자제품");
        mall.addProduct("P002", "애플 노트북", 1500000, 5, "전자제품");
        mall.addProduct("P003", "무선 이어폰", 150000, 20, "전자제품");
        // ... 더 많은 상품들
        
        // 테스트용 고객들
        mall.addCustomer("C001", "김철수", "kim@email.com");
        mall.addCustomer("C002", "이영희", "lee@email.com");
        
    } catch (ShopException e) {
        System.out.println("초기 데이터 설정 중 오류: " + e.getMessage());
    }
}
```

---

### 10단계: UI와 서비스 연결 (20분)

기능별 구현 순서:
1. 상품 관리 메뉴 (5분)
2. 고객 관리 메뉴 (3분)
3. 장바구니 관리 메뉴 (7분) ⭐
4. 주문 관리 메뉴 (5분) ⭐

#### 장바구니 관리 메뉴 (중요)
```java
private void handleCartManagement() {
    while (true) {
        System.out.println("1. 장바구니에 상품 추가");
        System.out.println("2. 장바구니 보기");
        System.out.println("0. 메인 메뉴로 돌아가기");
        
        int choice = Integer.parseInt(scanner.nextLine());
        
        switch (choice) {
            case 1: addToCart(); break;
            case 2: viewCart(); break;
            case 0: return;
        }
    }
}

private void addToCart() {
    try {
        System.out.print("고객 ID를 입력하세요: ");
        String customerId = scanner.nextLine().trim();
        
        System.out.print("상품 ID를 입력하세요: ");
        String productId = scanner.nextLine().trim();
        
        System.out.print("수량을 입력하세요: ");
        int quantity = Integer.parseInt(scanner.nextLine());
        
        mall.addToCart(customerId, productId, quantity);
        
    } catch (Exception e) {
        System.out.println("장바구니 추가 중 오류: " + e.getMessage());
    }
}
```

---

### 11단계: 예외 처리 및 안정성 (10분)

#### 예외 처리 패턴
```java
// 각 메뉴 핸들러에 일반 예외 처리 추가
private void handleProductManagement() {
    while (true) {
        try {
            // 메뉴 로직
        } catch (NumberFormatException e) {
            System.out.println("숫자를 입력해주세요.");
        } catch (Exception e) {
            System.out.println("오류가 발생했습니다: " + e.getMessage());
        }
    }
}

// 개별 기능에 구체적 예외 처리
private void placeOrder() {
    try {
        // 주문 로직
        mall.placeOrder(customerId, usePoint);
    } catch (NumberFormatException e) {
        System.out.println("적립금은 숫자로 입력해주세요.");
    } catch (Exception e) {
        System.out.println("주문 처리 중 오류: " + e.getMessage());
    }
}
```

#### 입력 검증 체크리스트
- [ ] 빈 문자열 검증
- [ ] 숫자 형식 검증 (NumberFormatException)
- [ ] 비즈니스 로직 예외 처리
- [ ] 프로그램 비정상 종료 방지

---

### 12단계: 고급 기능 및 통계 (10분)

시간이 남을 때 추가할 기능들:
1. `displayMallStatistics()` - 쇼핑몰 통계 (5분)
2. `getLowStockProducts()` - 재고 부족 상품 (2분)
3. `displayCustomerOrders()` - 고객 주문 내역 (3분)

#### 통계 정보 예시
```java
public void displayMallStatistics() {
    System.out.println("=== " + mallName + " 통계 정보 ===");
    
    // 상품 통계
    int totalProducts = products.size();
    long availableProducts = products.values().stream()
                                   .filter(Product::isInStock)
                                   .count();
    
    // 주문 통계 
    int totalOrders = orders.size();
    int totalRevenue = orders.stream()
                           .mapToInt(Order::getTotalAmount)
                           .sum();
    
    System.out.println("총 상품 수: " + totalProducts + "개");
    System.out.println("총 매출: " + String.format("%,d", totalRevenue) + "원");
    
    // 카테고리별 상품 수 분석
    HashMap<String, Integer> categoryCount = new HashMap<>();
    for (Product product : products.values()) {
        categoryCount.put(product.getCategory(), 
                        categoryCount.getOrDefault(product.getCategory(), 0) + 1);
    }
    
    System.out.println("카테고리별 상품 수:");
    for (Map.Entry<String, Integer> entry : categoryCount.entrySet()) {
        System.out.println("  " + entry.getKey() + ": " + entry.getValue() + "개");
    }
}
```

---

## ⏰ 시간 배분 가이드

### 📊 상세 시간표 (총 2시간)

| 단계 | 내용 | 시간 | 누적 |
|------|------|------|------|
| 1-2단계 | 프로젝트 구조 + 예외 클래스 | 20분 | 20분 |
| 3단계 | 기본 모델 클래스 | 25분 | 45분 |
| 4단계 | 장바구니 + 주문 클래스 | 15분 | 60분 |
| 5-6단계 | 서비스 기본 구조 + 데이터 관리 | 35분 | 95분 |
| 7단계 | 장바구니 기능 | 15분 | 110분 |
| 8단계 | 주문 처리 로직 (핵심) | 25분 | 135분 |
| 9-10단계 | UI 구현 및 연결 | 35분 | 170분 |
| 11-12단계 | 예외 처리 + 고급 기능 | 20분 | 190분 |
| **여유시간** | 테스트 및 디버깅 | 10분 | **120분** |

### 🎯 단계별 우선순위

#### 필수 구현 (90분)
- 1-8단계: 핵심 비즈니스 로직
- 기본 UI 구조

#### 권장 구현 (60분)
- 9-10단계: 완전한 UI 시스템
- 11단계: 예외 처리

#### 선택 구현 (여유시간)
- 12단계: 고급 기능 및 통계

---

## 🎯 구현 전략

### ⚡ 효율성을 위한 팁

#### 1. 핵심 기능 우선
- **장바구니 → 주문 처리**가 가장 중요
- UI는 최소한으로 구현 후 점진적 개선

#### 2. 테스트 주도 개발
```java
// 각 단계마다 간단한 테스트 수행
public static void main(String[] args) {
    ShoppingMall mall = new ShoppingMall("테스트몰");
    
    // 1. 데이터 추가 테스트
    mall.addProduct("P001", "테스트상품", 10000, 5, "테스트");
    mall.addCustomer("C001", "테스트고객", "test@test.com");
    
    // 2. 장바구니 테스트
    mall.addToCart("C001", "P001", 2);
    mall.displayCart("C001");
    
    // 3. 주문 테스트
    mall.placeOrder("C001", 0);
}
```

#### 3. 점진적 구현
- 기본 기능 완성 → 예외 처리 추가 → UI 개선
- 한 번에 완벽하게 만들려 하지 말고 단계적 개선

#### 4. 코드 재사용
```java
// 공통 검증 로직을 메서드로 분리
private void validateInput(String... inputs) {
    for (String input : inputs) {
        if (input.isEmpty()) {
            throw new IllegalArgumentException("모든 필드를 입력해주세요.");
        }
    }
}
```

### 🚨 주의사항

#### 1. 시간 관리
- **8단계 주문 처리**에 충분한 시간 할당 (25분)
- UI 구현에 너무 많은 시간 소모하지 않기
- 2시간 중 10분은 여유시간으로 남겨두기

#### 2. 복잡도 관리
```java
// 복잡한 로직은 메서드로 분리
public void placeOrder(String customerId, int usePoint) throws ShopException {
    Customer customer = validateCustomer(customerId);
    ArrayList<CartItem> cart = validateCart(customer);
    validatePoint(customer, usePoint);
    
    processOrder(customer, cart, usePoint);
}
```

#### 3. 데이터 일관성
- 주문 처리 시 재고 차감과 적립금 처리 동시 진행
- 장바구니와 실제 상품 재고 상태 일치 확인

---

## 📝 테스트 시나리오

### 🧪 기본 시나리오

#### 1. 상품 관리 테스트
```
1. 상품 추가 → P009 "신상품" 50000 10 "테스트"
2. 전체 상품 보기 → 9개 상품 확인
3. 카테고리별 조회 → "전자제품" 검색
```

#### 2. 고객 관리 테스트
```
1. 고객 등록 → C004 "홍길동" "hong@test.com"
2. 등급 확인 → Bronze 등급, 0점
```

#### 3. 장바구니 테스트
```
1. 장바구니 추가 → C001, P001, 1개
2. 장바구니 보기 → 할인율 0% 적용 확인
3. 추가 상품 → C001, P003, 2개
4. 장바구니 보기 → 총 3개 상품 확인
```

#### 4. 주문 테스트
```
1. 주문하기 → C001, 적립금 0점 사용
2. 주문 완료 → 적립금 적립 확인
3. 주문 내역 → C001 주문 내역 조회
```

### 🚫 예외 상황 테스트

#### 1. 잘못된 입력
- 존재하지 않는 고객 ID로 장바구니 추가
- 존재하지 않는 상품 ID로 장바구니 추가
- 보유 적립금보다 많은 적립금 사용 시도

#### 2. 재고 부족
- 재고보다 많은 수량 주문 시도
- 품절 상품 주문 시도

#### 3. 빈 데이터
- 빈 장바구니로 주문 시도
- 모든 필드가 비어있는 상태로 등록 시도

---

## 📊 평가 기준

### 🎯 기능별 배점 (100점)

#### 기본 기능 (60점)
- **상품 관리** (15점): 추가, 조회, 검색
- **고객 관리** (10점): 등록, 등급 시스템
- **장바구니** (15점): 추가, 조회, 할인 적용
- **주문 처리** (20점): 재고 차감, 적립금 처리, 주문 생성

#### 고급 기능 (25점)
- **할인 시스템** (10점): 등급별 할인 적용
- **적립금 시스템** (10점): 사용/적립, 등급 업데이트
- **통계 기능** (5점): 매출, 상품 현황

#### 코드 품질 (15점)
- **예외 처리** (5점): 적절한 예외 처리
- **사용자 인터페이스** (5점): 직관적인 메뉴 시스템
- **코드 구조** (5점): 패키지 구조, 명명 규칙

### ✅ 완성도 체크리스트

#### 필수 기능
- [ ] 상품 추가 및 조회
- [ ] 고객 등록
- [ ] 장바구니에 상품 추가
- [ ] 장바구니 내용 확인 (할인 적용)
- [ ] 주문 처리 (재고 차감, 적립금)
- [ ] 기본 메뉴 시스템

#### 고급 기능
- [ ] 회원 등급별 할인
- [ ] 적립금 사용 및 적립
- [ ] 주문 내역 조회
- [ ] 재고 부족 상품 조회
- [ ] 쇼핑몰 통계 정보

#### 안정성
- [ ] 예외 상황 처리
- [ ] 입력값 검증
- [ ] 프로그램 비정상 종료 방지

---

## 🔍 트러블슈팅

### 자주 발생하는 문제들

#### 1. 컴파일 오류
```java
// 문제: import 오류
import model.Customer; // 명시적 import 추가

// 문제: 타입 불일치
ArrayList<Product> results = mall.searchProductsByName(name);
```

#### 2. 논리 오류
```java
// 문제: 재고 차감 후 예외 발생 시 복구 안됨
// 해결: 재고 확인을 먼저 수행
for (CartItem item : cart) {
    if (item.getProduct().getStock() < item.getQuantity()) {
        throw new InsufficientStockException("재고 부족");
    }
}
// 모든 확인 완료 후 재고 차감
for (CartItem item : cart) {
    item.getProduct().reduceStock(item.getQuantity());
}
```

#### 3. 런타임 오류
```java
// 문제: NullPointerException
Customer customer = customers.get(customerId);
if (customer == null) {
    throw new CustomerNotFoundException("존재하지 않는 고객입니다");
}
```

### 디버깅 팁
1. **단계별 테스트**: 각 기능을 개별적으로 테스트
2. **로그 출력**: `System.out.println()`으로 중간 결과 확인
3. **데이터 확인**: HashMap과 ArrayList 내용을 출력하여 확인

---

## 🎓 학습 효과

### 실무 역량 향상
- **비즈니스 로직 설계**: 실제 쇼핑몰과 유사한 복잡한 로직 구현
- **데이터 관리**: 효율적인 자료구조 선택과 활용
- **예외 설계**: 비즈니스 예외 상황에 대한 체계적 대응

### 객체지향 설계 경험
- **상속과 다형성**: Person 계층 구조
- **캡슐화**: private 필드와 public 메서드
### 객체지향 설계 경험
- **상속과 다형성**: Person 계층 구조
- **캡슐화**: private 필드와 public 메서드
- **책임 분리**: 각 클래스의 명확한 역할 정의

### 실전 개발 프로세스
- **요구사항 분석**: 쇼핑몰 기능 분석
- **설계 → 구현 → 테스트**: 전체 개발 사이클 경험
- **점진적 개선**: 기본 기능부터 고급 기능까지

---

## 💡 성공을 위한 마지막 조언

### 구현 중 막힐 때
1. **단계별 접근**: 복잡한 기능을 작은 단위로 나누기
2. **테스트 우선**: 작은 기능부터 테스트하며 확신 갖기
3. **기본에 충실**: 완벽한 고급 기능보다 안정적인 기본 기능

### 시간 관리
- **핵심 기능 80% 완성** > 모든 기능 50% 완성
- **주문 처리 로직**에 가장 많은 시간 투자
- **UI는 단순하게**, 비즈니스 로직에 집중

### 코드 품질
```java
// 좋은 예: 명확한 메서드명과 적절한 분리
public void processOrder(Customer customer, ArrayList<CartItem> cart, int usePoint) {
    validateOrderRequest(customer, cart, usePoint);
    reduceStockForAllItems(cart);
    calculateAndApplyPayment(customer, cart, usePoint);
    createOrderRecord(customer, cart, usePoint);
    clearCustomerCart(customer);
}

// 나쁜 예: 하나의 메서드에 모든 로직
public void placeOrder() {
    // 100줄의 복잡한 로직...
}
```

---

## 🔗 관련 자료

### 참고 코드
- 전체 파일별 소스 코드 (12개 파일 제공)
- 컴파일 및 실행 방법

### 확장 학습
- **3-4시간 버전**: 도서관 관리 시스템 (더 복잡한 기능)
- **심화 과정**: Spring Boot를 이용한 웹 쇼핑몰
- **데이터베이스 연동**: MySQL과 JDBC 연결

### 추천 다음 단계
1. **웹 개발**: HTML/CSS/JavaScript 학습
2. **프레임워크**: Spring Boot 입문
3. **데이터베이스**: SQL 및 JPA 학습
4. **실무 프로젝트**: 팀 프로젝트로 확장

---

## 📚 참고 자료

### 핵심 Java 개념
- **객체지향**: 상속, 다형성, 캡슐화, 추상화
- **컬렉션**: HashMap, ArrayList, Stream API
- **예외처리**: try-catch, 사용자 정의 예외
- **입출력**: Scanner, System.out
- **날짜**: LocalDateTime, DateTimeFormatter

### 디자인 패턴
- **Template Method**: Person 추상클래스
- **Strategy**: 다양한 할인 정책
- **Exception Handling**: 계층적 예외 구조

---

## 🎉 마무리

이 가이드를 따라 구현하면 **실제 쇼핑몰과 유사한 완성도 높은 시스템**을 2시간 내에 만들 수 있습니다.

### 완성 후 자랑할 수 있는 기능들
- ✅ 실시간 할인 적용 장바구니
- ✅ 등급별 차등 할인 시스템  
- ✅ 적립금 사용/적립 시스템
- ✅ 재고 관리 및 부족 알림
- ✅ 주문 내역 및 통계 기능

### 포트폴리오 어필 포인트
- **비즈니스 로직 이해**: 실제 서비스 수준의 복잡한 요구사항 구현
- **객체지향 설계**: 상속, 다형성, 캡슐화 등 핵심 개념 활용
- **예외 처리**: 안정적인 시스템을 위한 체계적 예외 관리
- **사용자 경험**: 직관적이고 사용하기 쉬운 인터페이스

### 🎯 최종 체크포인트

#### 시작 전 준비사항
- [ ] Java 개발 환경 설정 완료
- [ ] 기본 객체지향 개념 이해
- [ ] HashMap, ArrayList 사용법 숙지
- [ ] try-catch 예외 처리 경험

#### 구현 완료 기준
- [ ] 상품 등록 → 장바구니 추가 → 주문 완료 플로우 동작
- [ ] 회원 등급별 할인 시스템 작동
- [ ] 적립금 사용/적립 기능 정상 작동
- [ ] 예외 상황에서 프로그램 비정상 종료 없음
- [ ] 기본 통계 정보 출력 가능

#### 평가 및 개선
- [ ] 동료와 코드 리뷰 진행
- [ ] 추가 기능 아이디어 도출
- [ ] 성능 최적화 방안 고민
- [ ] 실제 웹 서비스 확장 계획 수립

---

**마지막 업데이트**: 2024년 12월  
**작성자**: AI Assistant  
**대상**: Java 중급 학습자 (2시간 완성)

**행운을 빕니다! 멋진 쇼핑몰 시스템을 만들어보세요! 🚀**