## [CHAP.05] 형식 맞추기
- 프로그래머라면 **깔끔하게 맞춰 코드를 작성**해야 함
  - 간단한 규칙을 정하고 그 규칙을 따르기
  - 필요시 규칙을 **자동으로** 적용하는 도구 활용

### 형식을 맞추는 목적
- **코드 형식**은 중요
- 오늘 구현한 기능이 **다음 버전에서 변경될 확률은 매우 높음**
- 오늘 구현한 코드의 **가독성**은
  - 바뀔 코드의 **품질**에 지대한 영향을 미침
- 오랜 시간이 지나 원래 코드의 **흔적**을 찾아보기 어려울정도로 코드가 변경되어도
  - 맨 처음 잡은 **구현 스타일**과 **가독성 수준**은
  - **유지보수 용이성**과 **확장성**에 영향을 미침
- 원래 코드가 사라져도 **개발자의 스타일**과 **규율**은 사라지지 않음

### 적절한 행 길이를 유지하라
- Java에서의 **파일 크기**는 **클래스 크기**와 밀접함
- `JUnit, FitNesse, testNG, Time and Money(tam), JDepend, Ant, Tomcat`의 프로젝트 예시를 토대로
  - **500줄**을 넘지 않고 **200줄** 정도의 파일로도 커다란 시스템 구축 가능
  - 일반적으로 **큰 파일**보다 **작은 파일**이 이해하기 쉬움

#### 신문 기사처럼 작성하라
- **이름**은 **간단**하면서도 설명이 가능하도록
- 이름만 보고도
  - 올바른 **모듈**을 살펴보고 있는지 확인할수 있도록
- 소스 파일의 **첫 부분**은
  - **고차원 개념**과 **알고리즘**을 설명
- 아래로 내려갈 수록 **의도**를 세세하게 묘사
- 마지막에는 가장 **저차원 함수**와 **세부 내역** 확인

#### 개념은 빈 행으로 분리하라
- 각 행은 **수식**이나 **절**을 나타냄
- 일련의 **행묶음**은 완결된 생각 하나 표현
- **생각** 사이는 **빈 행**을 넣어 분리해야 마땅함
- **패키지 선언부, `import`문, 각 함수 사이**에는 **빈 행**이 존재

#### 세로밀집도
- **줄바꿈**이 **개념**을 분리한다면
  - **세로 밀집도**는 **연관성**을 나타냄
- **밀접한 코드 행**은 **세로**로 가까이 놓아야 함

##### LIST.5.3, LIST.5.4.
```java
// List 5.3.
public class ReporterConfig {
  private String m_className;

  private List<Property> m_properties = new ArrayList<Property>();
  public void addProperty(Property property) {
    m_properties.add(property);
  }
}

// List 5.4. 가독성이 좋은 코드
// 변수 2개에 메서드 1개인 클래스라는 사실이 드러남
public class ReporterConfig {
  private String m_className;
  private List<Property> m_properties = new ArrayList<Property>();

  public void addProperty(Property property) {
    m_properties.add(property);
  }
}
```

#### 수직 거리
- 서로 밀접한 개념은 **세로로 가까이** 두어야 함
- 타당한 근거가 없다면 **서로 밀접한 개념**은 **한 파일**에 속해야 함
  - `protected` 변수를 피해야 하는 이유
- 같은 파일에 속할 정도로 **밀접한 두 개념**은 **세로 거리**로 연관성을 표현
  - 연관성: 한 **개념**을 이해하는데 **다른 개념**이 중요한 정도
  - 연관성이 깊으나 두 개념이 멀리 떨어져있을 경우
    - **가독성이 좋지 않음**
- **변수 선언**
  - 변수는 사용하는 위치에 **최대한 가까이** 선언
  - 지역 변수는 **각 함수 맨 처음에 선언**
- `Junit 4.3.1.` 예시
  ```java
  private static void readPreferences() {
    // 지역변수 선언
    InputStream is = null;
    try {
      is = new FileInputStream(getPreferencesFile());
      setPreferences(new Properties(getPreferences()));
      getPreferences().load(is);
    } catch (IOException e) {
      try {
        ...
      }
    }
  }
  ```
- 루프를 제어하는 변수는 **루프문 내부**에 선언
  ```java
  public int countTestCases() {
    int count = 0;
    // 루프문에서 제어하는 변수
    for (Test each : tests)
      count += each.countTestCases();
    return count;
  }
  ```
- 드물지만, 다소 `긴 함수`에서 **블록 상단**이나 **루프 직전**에 변수를 선언하는 경우
  - `TestNG` 예시
    ```java
    for(XmlTest test : m_suite.getTests()) {
      // 변수 선언
      TestRunner tr = m_runnerFactory.newTestRunner(this, test);
      tr.addListener(m_testReporter);
      ...
    }
    ```