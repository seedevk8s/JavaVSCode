# Java 기본 개발 프로젝트

Visual Studio Code에서 Java 개발을 위한 기본 환경입니다.

## 프로젝트 구조
```
myDev/
├── src/
│   └── HelloWorld.java    # 메인 자바 파일
├── .github/
│   └── copilot-instructions.md
├── .vscode/
│   └── tasks.json
└── README.md
```

## 필요한 소프트웨어
- Java Development Kit (JDK) 8 이상
- Visual Studio Code
- Extension Pack for Java

## 실행 방법

### 1. 터미널에서 실행
```powershell
# 컴파일
javac src/HelloWorld.java -d out

# 실행
java -cp out HelloWorld
```

### 2. VS Code에서 실행
- `F5`를 눌러서 디버그 모드로 실행
- 또는 코드 에디터에서 `Run` 버튼 클릭

## 학습 내용
- Java 기본 문법
- 변수와 데이터 타입
- 메서드 정의와 호출
- 객체지향 프로그래밍 기초

## 다음 단계
1. 더 많은 Java 예제 추가
2. 클래스와 객체 학습
3. 배열과 컬렉션 학습
4. 예외처리 학습
