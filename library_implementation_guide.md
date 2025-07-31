# 도서관 관리 시스템 구현 가이드

## 📋 개요
이 문서는 Java를 이용한 도서관 관리 시스템을 효율적으로 구현하기 위한 단계별 가이드입니다.

**예상 소요 시간**: 3-4시간  
**난이도**: 중급  
**주요 개념**: 객체지향, 컬렉션, 예외처리, 상속, 다형성

---

## 🚀 구현 순서

### 1단계: 프로젝트 구조 설계 (15분)

#### 📁 패키지 구조
```
LibraryManagementSystem/
├── src/
│   ├── exception/     # 예외 클래스들
│   ├── model/         # 데이터 모델 클래스들
│   ├── service/       # 비즈니스 로직
│   └── main/          # 실행 및 UI
```

#### 📝 사전 계획
- [ ] 클래스 다이어그램 스케치
- [ ] 주요 기능 목록 작성
- [ ] 데이터 구조 설계 (HashMap vs ArrayList)

---

### 2단계: 예외 클래스 구현 (15분)

**구현 순서**:
1. `LibraryException` (부모 예외)
2. `BookNotFoundException`
3. `MemberNotFoundException` 
4. `BookAlreadyBorrowedException`

```java
// 예시: LibraryException.java
package exception;

public class LibraryException extends Exception {
    public LibraryException(String message) {
        super(message);
    }
}
```

**💡 중요한 이유**: 
- 다른 클래스들이 예외를 사용하므로 먼저 구현 필요
- 상속 관계를 고려한 계층 구조

---

### 3단계: 기본 모델 클래스 구현 (30분)

**구현 순서**:
1. `Person` 추상클래스 (기본 틀)
2. `Book` 클래스 (가장 독립적)
3. `Member` 클래스 (Person 상속)
4. `Librarian` 클래스 (Person 상속)

#### 핵심 포인트
- **Person 클래스**: 공통 필드(id, name, phone)와 추상 메서드 `getRole()`
- **Book 클래스**: 재고 관리, 대출 상태, 연체 확인 기능
- **Member 클래스**: 대출 목록 관리, 최대 대출 권수 제한
- **Librarian 클래스**: 부서, 직책 정보

```java
// 예시: Person 추상클래스 핵심 구조
public abstract class Person {
    protected String id;
    protected String name;
    protected String phone;
    
    public abstract String getRole();
}
```

---

### 4단계: 서비스 클래스 기본 구조 (20분)

#### Library 클래스 골격 구성
```java
public class Library {
    private HashMap<String, Book> books;
    private HashMap<String, Member> members;
    private HashMap<String, Librarian> librarians;
    
    // 생성자
    public Library(String libraryName) {
        // HashMap 초기화
    }
    
    // 메서드 시그니처만 작성 (구현은 다음 단계에서)
    public void addBook(...) { }
    public void borrowBook(...) throws LibraryException { }
    // ... 기타 메서드들
}
```

**💡 팁**: 구현부는 비워두고 시그니처만 작성하여 컴파일 오류 방지

---

### 5단계: 데이터 관리 기능 구현 (30분)

**구현 순서 (CRUD 기본)**:
1. `addBook()` - 책 추가
2. `addMember()` - 회원 추가
3. `addLibrarian()` - 사서 추가
4. `displayAllBooks()` - 전체 책 목록

#### 구현 체크리스트
- [ ] 중복 데이터 검증 (동일 ID 체크)
- [ ] HashMap을 이용한 효율적인 저장
- [ ] 적절한 예외 처리
- [ ] 성공/실패 메시지 출력

**💡 중요한 이유**: 데이터가 있어야 다른 기능들을 테스트할 수 있음

---

### 6단계: 검색 기능 구현 (20분)

**구현 순서**:
1. `searchBooksByTitle()` - 제목으로 검색
2. `searchBooksByAuthor()` - 저자로 검색
3. `getBooksByCategory()` - 카테고리별 조회

#### 구현 포인트
```java
public ArrayList<Book> searchBooksByTitle(String title) {
    ArrayList<Book> results = new ArrayList<>();
    for (Book book : books.values()) {
        if (book.getTitle().toLowerCase().contains(title.toLowerCase())) {
            results.add(book);
        }
    }
    return results;
}
```

- **대소문자 무시**: `toLowerCase()` 사용
- **부분 검색**: `contains()` 메서드 활용
- **빈 결과 처리**: 빈 ArrayList 반환

---

### 7단계: 핵심 비즈니스 로직 구현 (40분)

**구현 순서**:
1. `borrowBook()` - 대출 기능 ⭐
2. `returnBook()` - 반납 기능 ⭐
3. `displayMemberBorrowStatus()` - 회원 대출 현황

#### 대출 기능 구현 체크리스트
- [ ] 책 존재 여부 확인
- [ ] 회원 존재 여부 확인
- [ ] 책 대출 가능 여부 확인
- [ ] 회원 대출 가능 권수 확인
- [ ] 양쪽 객체 상태 업데이트 (Book + Member)
- [ ] 적절한 예외 처리

```java
public void borrowBook(String isbn, String memberId) throws LibraryException {
    // 1. 책 존재 확인
    Book book = books.get(isbn);
    if (book == null) {
        throw new BookNotFoundException("존재하지 않는 책입니다: " + isbn);
    }
    
    // 2. 회원 존재 확인
    Member member = members.get(memberId);
    if (member == null) {
        throw new MemberNotFoundException("존재하지 않는 회원입니다: " + memberId);
    }
    
    // 3. 대출 처리
    book.borrowTo(memberId);
    member.borrowBook(isbn);
}
```

**💡 가장 중요한 단계**: 복잡하고 중요한 기능이므로 충분한 시간 할당

---

### 8단계: UI 클래스 기본 구조 (30분)

#### LibraryManagementSystem 클래스 구성
```java
public class LibraryManagementSystem {
    private Library library;
    private Scanner scanner;
    
    // 1. 생성자와 초기 데이터
    public LibraryManagementSystem() {
        this.library = new Library("중앙도서관");
        this.scanner = new Scanner(System.in);
        initializeData(); // 테스트용 기본 데이터
    }
    
    // 2. 메인 메뉴 시스템
    public void run() {
        while (true) {
            displayMainMenu();
            // 메뉴 처리 로직
        }
    }
    
    // 3. 각 서브메뉴 골격
    private void handleBookManagement() { }
    private void handleMemberManagement() { }
    private void handleBorrowReturnManagement() { }
}
```

#### 초기 데이터 준비
- 테스트용 책 5-10권
- 테스트용 회원 3-5명
- 테스트용 사서 1-2명

---

### 9단계: UI와 서비스 연결 (30분)

**기능별 연결 순서**:
1. 도서 관리 메뉴 구현
2. 회원 관리 메뉴 구현
3. 대출/반납 메뉴 구현
4. 검색 메뉴 구현

#### 메뉴 구현 패턴
```java
private void handleBookManagement() {
    while (true) {
        // 메뉴 출력
        System.out.println("1. 도서 추가");
        System.out.println("2. 전체 도서 목록");
        // ...
        
        try {
            int choice = Integer.parseInt(scanner.nextLine());
            switch (choice) {
                case 1: addNewBook(); break;
                case 2: library.displayAllBooks(); break;
                // ...
            }
        } catch (NumberFormatException e) {
            System.out.println("숫자를 입력해주세요.");
        }
    }
}
```

---

### 10단계: 예외 처리 및 검증 (20분)

#### 안정성 향상 체크리스트
- [ ] 모든 사용자 입력에 대한 검증
- [ ] try-catch 블록 추가
- [ ] 사용자 친화적 오류 메시지
- [ ] 프로그램 비정상 종료 방지

```java
// 예외 처리 패턴
try {
    // 비즈니스 로직
    library.borrowBook(isbn, memberId);
} catch (LibraryException e) {
    System.out.println("대출 처리 중 오류: " + e.getMessage());
} catch (Exception e) {
    System.out.println("예상치 못한 오류가 발생했습니다: " + e.getMessage());
}
```

#### 입력 검증 예시
```java
// 빈 입력값 검증
if (isbn.isEmpty() || title.isEmpty()) {
    System.out.println("모든 필드를 입력해주세요.");
    return;
}

// 날짜 형식 검증
try {
    LocalDate publishDate = LocalDate.parse(dateStr);
} catch (DateTimeParseException e) {
    System.out.println("날짜 형식이 올바르지 않습니다. YYYY-MM-DD 형식으로 입력해주세요.");
}
```

---

### 11단계: 고급 기능 구현 (20분)

**시간이 남을 때 추가할 기능들**:
1. `displayOverdueBooks()` - 연체 도서 조회
2. `displayLibraryStatistics()` - 통계 정보
3. `displayAvailableBooks()` - 구매 가능 도서
4. `getLowStockBooks()` - 재고 부족 도서

#### 통계 기능 예시
```java
public void displayLibraryStatistics() {
    int totalBooks = books.size();
    long availableBooks = books.values().stream()
                              .filter(Book::isAvailable)
                              .count();
    
    System.out.println("총 도서 수: " + totalBooks + "권");
    System.out.println("대출 가능: " + availableBooks + "권");
    // ...
}
```

---

### 12단계: 테스트 및 디버깅 (20분)

#### 테스트 시나리오
1. **정상 시나리오**
   - [ ] 책 추가 → 회원 등록 → 대출 → 반납
   - [ ] 검색 기능 동작 확인
   - [ ] 통계 정보 정확성 확인

2. **예외 상황 테스트**
   - [ ] 존재하지 않는 책/회원으로 대출 시도
   - [ ] 이미 대출된 책 재대출 시도
   - [ ] 대출 한도 초과 시도
   - [ ] 잘못된 입력값 (빈 문자열, 잘못된 날짜 등)

3. **경계값 테스트**
   - [ ] 최대 대출 권수 경계
   - [ ] 연체 기준일 (14일) 경계
   - [ ] 빈 데이터 상황

---

## 🎯 구현 전략

### ⚡ 효율성을 위한 팁

#### 1. Bottom-Up 방식
- **기본 클래스 → 복합 클래스** 순서로 구현
- 의존성이 적은 클래스부터 시작

#### 2. 기능별 완성
- 한 기능을 완전히 구현한 후 다음 단계로
- 부분적 구현보다는 완전한 구현 우선

#### 3. 점진적 개선
- 기본 기능 → 고급 기능 순서
- 핵심 기능 우선, 부가 기능은 나중에

#### 4. 즉시 테스트
- 각 단계마다 간단한 테스트 수행
- 문제 발견 시 즉시 수정

### 🚨 주의사항

#### 1. 컴파일 오류 우선 해결
```java
// 메서드 시그니처만 먼저 작성하여 컴파일 오류 방지
public void borrowBook(String isbn, String memberId) throws LibraryException {
    // TODO: 구현 예정
    throw new UnsupportedOperationException("아직 구현되지 않음");
}
```

#### 2. 패키지 import 확인
```java
// 명시적 import로 타입 불일치 방지
import model.Book;
import model.Member;
import exception.LibraryException;
```

#### 3. 예외 처리는 마지막
- 기본 로직 완성 후 안정성 추가
- 모든 기능이 동작한 후 예외 상황 처리

---

## ⏰ 시간 배분 가이드

### 3시간 버전 (최소 기능)
- **1-7단계**: 핵심 비즈니스 로직 (2시간)
- **8-9단계**: UI 구현 (45분)
- **10단계**: 기본 예외 처리 (15분)

### 4시간 버전 (완전 기능)
- **1-7단계**: 핵심 비즈니스 로직 (2.5시간)
- **8-9단계**: UI 구현 (1시간)
- **10-12단계**: 안정성 및 고급 기능 (30분)

---

## 📚 참고 자료

### 핵심 Java 개념
- **객체지향**: 상속, 다형성, 캡슐화, 추상화
- **컬렉션**: HashMap, ArrayList, Stream API
- **예외처리**: try-catch, 사용자 정의 예외
- **입출력**: Scanner, System.out
- **날짜**: LocalDate, DateTimeFormatter

### 디자인 패턴
- **Template Method**: Person 추상클래스
- **Strategy**: 다양한 검색 방법
- **Exception Handling**: 계층적 예외 구조

---

## ✅ 완성도 체크리스트

### 기본 기능 (필수)
- [ ] 책 추가/조회
- [ ] 회원 등록/조회
- [ ] 도서 대출/반납
- [ ] 기본 검색 기능
- [ ] 메뉴 시스템

### 고급 기능 (선택)
- [ ] 연체 관리
- [ ] 통계 정보
- [ ] 회원 등급 관리
- [ ] 카테고리별 관리
- [ ] 사서 권한 관리

### 코드 품질
- [ ] 적절한 예외 처리
- [ ] 사용자 친화적 메시지
- [ ] 코드 주석
- [ ] 명명 규칙 준수
- [ ] 패키지 구조

---

## 🎓 학습 목표 달성도

이 프로젝트를 완성하면 다음 개념들을 실습할 수 있습니다:

- ✅ **객체지향 프로그래밍**: 상속, 다형성, 캡슐화
- ✅ **컬렉션 프레임워크**: HashMap, ArrayList 활용
- ✅ **예외 처리**: 사용자 정의 예외, try-catch
- ✅ **입출력 처리**: Scanner를 이용한 사용자 인터페이스
- ✅ **프로젝트 구조**: 패키지 분리, 모듈화
- ✅ **비즈니스 로직**: 실제 도서관 시스템과 유사한 로직 구현

---

**마지막 업데이트**: 2024년 12월  
**작성자**: AI Assistant  
**대상**: Java 중급 학습자