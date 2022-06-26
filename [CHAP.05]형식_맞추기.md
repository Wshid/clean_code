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
- **인스턴스 변수**: 클래스 맨 처음에 선언
  - 변수간에 거리도 두지 않음 
  - 잘 설계한 클래스에서는 대다수의 **클래스 메서드**가 **인스턴스 변수**를 사용하기 때문
  - 아직 위치에 대한 논쟁은 존재
    - `c++`: `scissors rule`에 따라, 모든 인스턴스 변수를 `클래스 마지막에 선언`
    - `java`: 클래스 맨 처음에 선언
  - 잘 알려진 위치에 **인스턴스 변수의 모음**이 중요
    - 변수 선언을 어디서 찾을지가 중요
- **종속 함수**
  - `한 함수`가 `다른 함수`를 호출한다면, 세로로 가까이 위치
  - 가능하다면 `호출하는 함수`를 `호출되는 함수`보다 먼저 배치
  - c.f. 상수를 적절한 위치에 두는 예시
    ```java
    // defaultPageName을 따로 받음(함수 내에서 상수 정의 x)
    // default value가 다른 상황에서 재사용성이 높아짐
    private String getPageNameOrDefault(Request request, String defaultPageName) {
      String pageName = request.getResource();
      if (StringUtil.isBlank(pageName))
        pageName = defaultPageName;
      
      return PageName;
    }
    ```
- **개념적 유사성**
  - 개념적인 친화도가 높은 코드들, 가까이 배치
  - `친화도가 높다`
    - 한 함수가 `다른 함수를 호출`해 생기는 종속성
    - 변수와 `그 변수를 사용하는 함수`
    - `비슷한 동작`을 수행하는 함수
      ```java
      // Junit 4.3.1
      public class Assert {
        static public void assertTure(String message, boolean condition) {
          if (!condition)
            fail(message);
        }

        static public void assertTure(boolean condition) {
          assertTrue(null, condition);
        } 
        
        static public void assertFalse(String message, boolean condition) {
          assertTrue(message, !condition);
        }

        static public void assertFalse(boolean condition) {
          assertFalse(null, condition)
        }
      }
      ```
      - 개념적인 친화도가 높음
      - **명명법**이 동일, **기능** 유사
      - 서로가 서로를 호출하는건 `부차적인 요인`
      - **종속적인 관계 x**, but 가까이 배치

#### 세로 순서
- 일반적으로 `함수 호출 종속성`는 **아래 방향**으로 유지
- **호출 하는 함수 -> 호출되는 함수**
  - 소스 코드 모듈이 자연스럽게 `고차원 -> 저차원`으로 이동
- `신문 기사`와 유사하게
  - **중요한 개념**을 먼저 배치
    - 이때 `세세한 내용`은 제외
  - **세세한 사항**은 가장 마지막에 배치

### 가로 형식 맞추기
- 한 행의 가로 길이의 정도?
  - 보통은 짧은 행이 바람직
    - e.g. 프로젝트 7개 예시, `20 ~ 60`: 40%
  - `100 ~ 120`도 좋음

#### 가로 공백과 밀집도
- **공백**을 활용하여 `밀접한 개념`과 `느슨한 개념`을 표현
  ```java
  private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
  }
  ```
  - **할당 연산자**를 강조하기 위해 **앙 옆 공백**
    - **할당문**은 `left/right`가 확실히 나누어짐
  - **함수 이름**과 이어지는 `괄호 사이`에는 **공백 x**
    - **함수**와 **인수**는 밀접한 연관이 있기 때문
  - **함수**를 호출하는 **괄호 안 인수**는 **공백**
    - 쉼표를 강조해 **인수**가 별개임을 보여주기 위함
- **연산자 우선순위**를 강조하기 위해서도 **공백**을 사용
  ```java
  public class Quadratic {
    public static double root1(double a, double b, double c) {
      double determinant = determinant(a, b, c);
      return (-b + Math.sqrt(determinant)) / (2*a);
    }

    public static double root2(int a, int b, int c) {
      double determinant = determinant(a, b, c);
      return (-b + Math.sqrt(determinant)) / (2*a);
    }
    
    private static double determinant(double a, double b, double c) {
      return b*b - 4*a*c;
    }
  }
  ```
  - **승수**사이에는 `공백 x`
    - **곱셈**은 우선순위가 가장 높음
  - **항** 사이에는 `공백`
    - **덧셈/뺄샘**은 우선순위가 곱셈보다 낮음
- 하지만 대부분의 `IDE`에서
  - **연산자 우선순위**를 고려하지 않음

#### 가로 정렬
- 예시
  ```java
  public class FitNesseExpediter implements ReponseSender
  {
    private   Socket          socket;
    private   InputStream     input;
    protected FitNesseContext context;
    ...
  }
  ```
- 위와 같은 정렬은 **유용하지 x**
  - 코드가 엉뚱한 부분을 강조, 진짜 의도가 가려짐
  - 위 내용을 읽어보면 `변수 유형`은 무시되고, `변수명`만 읽히기 쉬움
- 선언부와 초기화하는 부분만 별도로 구분
  - 선언부 & 생성자

#### 들여쓰기
- 소스파일은 outline과 계층이 유사
  - 파일 전체에 적용되는 범위
  - 파일 내 **개별 클래스** 범위
  - 클래스 내 각 **메서드** 범위
  - 블록 내 블록에 재귀적으로 적용되는 정보
- 계층에서 각 **수준**은 **이름을 선언하는 범위**이자 `선언문`, `실행문`을 해석하는 범위
- 범위(`scope`)로 이루어진 계층을 표현하기 위해 **들여씀**
- 들여쓰기 정도는 **계층**에서 **코드가 자리잡은 수준**에 비례
- 클래스 정의처럼 **파일 수준**은 들여쓰지 X
- 클래스 -> 메서드 -> 블록

#### 가짜 범위
- 빈 `while`문이나 `for`문
- 가능한 작성하지 않음
- 피자미 못할 경우
  - **빈 블록**을 올바르게 들여쓰고 **괄호**로 감쌈

### 팀 규칙
- 한 가지 규칙에 합의 해야함
- 모든 팀원은 이를 따라야 함
  - 괄호의 위치
  - 들여쓰기 글자 수
  - 변수와 메서드명
- 이후 정한 규칙대로 **IDE 설정**

### 밥 아저씨의 형식 규칙
#### CODE.5.6
```java
public class CodeAnalyzer implements JavaFileAnalysis {
  private int lineCount;
  private int maxLineWidth;
  private int widestLineNumber;
  private int totalChars;

  public CodeAnalyzer() {
    lineWidthHistogram = new LineWidthHistogram();
  }

  public static List<File> findJavaFiles(File parentDirectory) {
    List<File> files = new ArrayList<File>();
    findJavaFiles(parentDirectory, files);
    return files;
  }

  private static void findJavaFiles(File parentDirectory, List<File> files) {
    for (File file : parentDirectory.listFiles()) {
      if (file.getName().endsWith(".java"))
        files.add(file);
      else if (file.isDirectory())
        findJavaFiles(file, files);
    }
  }

  public void analyzeFile(File javaFile) throws Exception {
    BufferedReader br = new BufferedReader(new FileReader(javaFile));
    String line;
    while ((line = br.readLine()) != null)
      neasureLine(line);
  }

  private void measureLine(String line) {
    lineCount++;
    int lineSize = line.elngth();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
  }

  private void recordWidestLine(int lineSize) {
    if (lineSize > maxLineWidth) {
      maxLineWidth = lineSize;
      widestLineNumber = lineCount;
    }
  }

  public int getLineCount() {
    return lineCount;
  }

  public int getMaxLineWidth() {
    return maxLineWidth;
  }
  
  public int getWidestLineNumber() {
    return widestLineNumber;
  }

  public LineWidthHistogram getLineWidthHistogram() {
    return lineWidthHistogram;
  }

  public double getMeanLineWidth() {
    return (double)totalChars/lineCount;
  }

  public int getMedianLineWidth() {
    Integer[] sortedWidths = getSortedWidths();
    int cumulativeLineCount = 0;
    for (int width : sortedWidths) {
      cumulativeLineCount += lineCountForWidth(width);
      if (cumulativeLineCount > lineCount/2)
        return width;
    }
    throw new Error("Cannot get here");
  }

  private int lineCountForWidth(int width) {
    return lineWidthHistogram.getLinesforWidth(width).size();
  }

  private Integer[] getSortedWidths() {
    Set<Integer> widths = lineWidthHistogram.getWidths();
    Integer[] sortedWidths = (widths.toArray(new Integer[0]));
    Arrays.sort(sortedWidths);
    return sortedWidths;
  }
}
```