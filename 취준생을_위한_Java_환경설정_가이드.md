# 취준생을 위한 Java 개발 환경 설정 완벽 가이드

> 🎯 **목표**: Visual Studio Code에서 Java 개발을 시작하기 위한 완전한 환경 구축

## 📋 준비사항 체크리스트

### 필수 소프트웨어
- [ ] **JDK (Java Development Kit)** 설치 
  - Oracle JDK 또는 OpenJDK 8 이상
  - 다운로드: [Oracle JDK](https://www.oracle.com/java/technologies/downloads/) 또는 [OpenJDK](https://adoptium.net/)
- [ ] **Visual Studio Code** 설치
  - 다운로드: [VS Code 공식 사이트](https://code.visualstudio.com/)

### JDK 설치 확인 방법
```powershell
java -version
javac -version
```
위 명령어가 정상적으로 버전을 출력하면 JDK가 올바르게 설치된 것입니다.

---

## 🛠️ 단계별 환경 설정

### 1단계: VS Code 확장 프로그램 설치

**필수 확장 프로그램:**
- **Extension Pack for Java** (vscjava.vscode-java-pack)
  - 자바 개발에 필요한 모든 확장을 한 번에 설치
  - 포함 내용: Language Support, Debugger, Test Runner, Maven, Project Manager

**설치 방법:**
1. VS Code 실행
2. 좌측 사이드바의 확장 프로그램 아이콘 클릭 (Ctrl+Shift+X)
3. "Extension Pack for Java" 검색 후 설치

### 2단계: 프로젝트 폴더 생성

```
myJavaProject/
├── src/              # 소스 코드 폴더
├── out/              # 컴파일된 클래스 파일
├── .vscode/          # VS Code 설정
│   └── tasks.json
├── .github/          # GitHub 관련 설정
│   └── copilot-instructions.md
└── README.md         # 프로젝트 문서
```

### 3단계: 첫 번째 Java 프로그램 작성

**파일 위치**: `src/HelloWorld.java`

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("안녕하세요! 자바 개발 환경에 오신 것을 환영합니다!");
        System.out.println("Hello World from Java!");
        
        // 기본적인 변수 선언과 출력
        String name = "Java Developer";
        int year = 2025;
        
        System.out.println("이름: " + name);
        System.out.println("년도: " + year);
    }
}
```

### 4단계: 컴파일 및 실행

**방법 1: 터미널 사용 (PowerShell)**
```powershell
# 1. 컴파일
javac src/HelloWorld.java -d out

# 2. 실행
java -cp out HelloWorld
```

**방법 2: VS Code 내장 기능**
- `F5` 키를 눌러서 디버그 모드로 실행
- 또는 코드 에디터 상단의 `Run` 버튼 클릭

---

## ⚙️ VS Code 설정 파일

### tasks.json 설정
**파일 위치**: `.vscode/tasks.json`

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Java: Compile and Run",
            "type": "shell",
            "command": "javac",
            "args": [
                "src/HelloWorld.java",
                "-d",
                "out"
            ],
            "group": "build",
            "isBackground": false
        }
    ]
}
```

### Copilot 설정
**파일 위치**: `.github/copilot-instructions.md`

GitHub Copilot이 한국어 주석과 초보자 친화적인 코드를 생성하도록 설정합니다.

---

## 🎯 취준생 학습 로드맵

### 1주차: Java 기본 문법
- [ ] 변수와 데이터 타입
- [ ] 연산자
- [ ] 조건문 (if, switch)
- [ ] 반복문 (for, while)

### 2주차: 객체지향 프로그래밍
- [ ] 클래스와 객체
- [ ] 생성자
- [ ] 메서드
- [ ] 캡슐화

### 3주차: 상속과 다형성
- [ ] 상속 (extends)
- [ ] 메서드 오버라이딩
- [ ] 추상 클래스
- [ ] 인터페이스

### 4주차: 예외처리와 컬렉션
- [ ] try-catch 문
- [ ] ArrayList, HashMap
- [ ] 제네릭 기초

---

## 🚨 자주 발생하는 문제와 해결방법

### 문제 1: "javac를 찾을 수 없습니다"
**원인**: JDK가 설치되지 않았거나 PATH 환경변수가 설정되지 않음
**해결**: JDK 재설치 후 환경변수 설정 확인

### 문제 2: "클래스를 찾을 수 없습니다"
**원인**: 클래스패스가 올바르지 않음
**해결**: `java -cp out HelloWorld` 명령어에서 경로 확인

### 문제 3: VS Code에서 Java 문법 하이라이팅이 안됨
**원인**: Extension Pack for Java가 설치되지 않음
**해결**: 확장 프로그램 재설치

---

## 🤔 수동 vs 자동 프로젝트 생성

### 일반적인 취준생의 프로젝트 생성 과정 (수동)

**1단계: 폴더 수동 생성**
```
Windows 탐색기에서:
1. 새 폴더 만들기 (예: MyJavaProject)
2. src 폴더 수동 생성
3. VS Code에서 폴더 열기 (File > Open Folder)
```

**2단계: 파일 수동 생성**
```
VS Code에서:
1. 새 파일 만들기 (Ctrl+N)
2. HelloWorld.java로 저장 (Ctrl+S)
3. 코드 직접 타이핑
```

**3단계: 터미널에서 수동 컴파일/실행**
```powershell
javac HelloWorld.java
java HelloWorld
```

### 자동화 도구의 장점

✅ **시간 절약** - 몇 초만에 완전한 프로젝트 구조 생성  
✅ **실수 방지** - 폴더 구조나 설정 파일 누락 없음  
✅ **일관성** - 항상 같은 품질의 프로젝트 템플릿  
✅ **모범 사례** - 업계 표준에 맞는 구조 자동 적용  

### 취준생이 알아야 할 현실

**학습 단계에서는:**
- 수동 과정을 통해 기본기 이해 필요
- 각 파일의 역할과 구조 파악 중요

**실무에서는:**
- Maven, Gradle, Spring Boot CLI 등 자동화 도구 사용
- 프로젝트 템플릿과 보일러플레이트 코드 활용
- IDE의 프로젝트 생성 마법사 사용

**💡 추천 학습 방법:**
1. 처음에는 수동으로 프로젝트 생성해보기
2. 구조와 원리 이해 후 자동화 도구 활용하기
3. 실무 환경에 맞는 도구들 경험해보기

---

## 💡 취준생을 위한 추가 팁

### 코딩 테스트 준비
1. **알고리즘 사이트 추천**
   - 백준 온라인 저지
   - 프로그래머스
   - LeetCode

2. **추천 학습 순서**
   - 기본 문법 → 자료구조 → 알고리즘 → 실전 문제

### 면접 준비
- **기술면접 단골 질문**
  - OOP의 4대 특징
  - JVM 동작 원리
  - 가비지 컬렉션
  - 컬렉션 프레임워크

### 프로젝트 포트폴리오
- **초급자 프로젝트 아이디어**
  - 계산기 만들기
  - 주소록 관리 시스템
  - 간단한 게임 (숫자 맞추기, 가위바위보)
  - 파일 입출력을 활용한 데이터 관리

---

## 📚 참고 자료

- [Oracle Java Documentation](https://docs.oracle.com/en/java/)
- [VS Code Java 개발 가이드](https://code.visualstudio.com/docs/languages/java)
- [GitHub Copilot 사용법](https://docs.github.com/copilot)

---

## ✅ 환경 설정 완료 체크리스트

- [ ] JDK 설치 및 버전 확인
- [ ] VS Code 설치
- [ ] Extension Pack for Java 설치
- [ ] 프로젝트 폴더 구조 생성
- [ ] HelloWorld.java 작성 및 실행 성공
- [ ] 컴파일 및 실행 명령어 숙지
- [ ] VS Code 태스크 설정 완료

**🎉 축하합니다! 이제 Java 개발을 시작할 준비가 완료되었습니다!**
