## [CHAP.03] 함수
- 함수를 잘 만드는 법

### 작게 만들어라
- 첫째 규칙: 작게 만들기
- 둘째 규칙: **더 작게 만들기**

#### 블록과 들여쓰기
- `if/else/while`문등에 들어가는 **블록**은 한 줄이어야 함
- 대개 이곳에서 **함수 호출**
- **바깥을 감싸는 함수**(enclosing function)이 작아질 뿐만 아니라,
  - **블록 안에서 호출하는 함수 이름**을 적절히 짓는다면
  - 이해하기 쉬워짐
- **중첩 구조**가 생길만큼 함수가 커지면 안됨
- 함수에서 **들여쓰기 수준**은 **1단**이나 **2단**을 넘어가면 안됨

### 한가지만 해라
- 함수는 **한가지만** 할 것
  - **그 한가지를 잘 해야함**
- 지정한 함수 이름 아래에서
  - **추상화 수준**이 **하나인 단계**만 수행한다면, 함수는 한가지 작업만 함
- 함수를 만드는 이유?
  - 큰 개념(함수 이름)을 **다음 추상화 수준**에서 **여러가지**로 나눠 수행하기 위함
- 함수가 한가지만을 하는지 **판단하는 방법**
  - 단순히 **의미 있는 이름**으로 **다른 함수 추출**이 가능할 경우
  - 그 함수는 **여러 작업**을 하는 것

#### 함수 내 섹션
- 한 가지 작업만 하는 함수는 자연스럽게 **섹션**으로 나누기 어려움

#### 함수 당 추상화 수준은 하나로
- 함수가 **한가지**만 하려면,
  - 함수 내 모든 문장의 **추상화 수준이 동일**해야 함
- 한 함수 내에서 **추상화 수준을 섞지 말 것**
  - `근본 개념`인지 `세부사항`인지 구분하기가 어려움

#### 위에서 아래로 코드 읽기: 내려가기 규칙
- 코드는 `위에서 아래로 이야기처럼`읽혀야 좋음
- 한 함수 다음에는 **추상화 수준**이 **한 단계 낮은** 함수가 와야 함
- 위에서 아래로 프로그램을 읽으면
  - **함수 추상화 수준**이 한 번에 한 단계씩 낮아짐
  - => **내려가기 규칙**
- 하지만 **추상화 수준이 하나**인 함수를 구현하기는 어려움
  - 매우 중요한 규칙
- 핵심: **짧으면서 한 가지만 하는 함수**

### Switch 문
- `switch`문은 작게 만들기 어려움
- `case`가 두개인 구문도
  - 너무 길며, `단일 블록`이나 `함수`를 선호
- 본질적으로 `switch`문은 `n`가지를 처리
- 각 `switch`문을 **저차원 클래스**에 숨기고, 절대로 반복하지 않는 방법은 존재
  - **다형성**(polymorphism)을 이용

#### LIST.3.4. Payroll.java
```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
  switch(e.type) {
    case COMMISSIONED:
      return calculateCommissionedPay(e);
    case HOURLY:
      return calculateHourlyPay(e);
    case SALARIED:
      return calculateSalariedPay(e);
    default:
      throw new InvalidEmployeeType(e.type);
  }
}
```
- 위 함수의 문제
  - **함수가 김**
    - 새 직원 유형을 추가하게 되면 더 길어짐
  - **한가지 작업만 수행하지 않음**
  - **SRP**(Single Responsibility Principle) 위반
    - 코드를 변경할 이유가 여럿이기 때문
  - **OCP**(Open Closed Principle) 위반
    - 새 직원 유형을 추가할 때마다
    - 코드를 변경하기 때문
  - 심각한 문제
    - 위 함수와 **구조가 동일한 함수**가 무한정 존재
      ```java
      isPayDay(Employee e, Date date);
      deliverPay(Employee e, Money pay);
      ...
      ```

#### LIST.3.5. Employee and Factory
- 위 코드의 문제를 해결한 코드
- `switch`문을 **추상 팩터리**(Abstract Factory)에 숨김
- 팩토리는 `switch`구문을 사용해, 적절한 `Employee` 파생 클래스 인스턴스 생성
- `calculatePay, isPayday, deliverPay`등의 함수는
  - `Employee` 인터페이스를 거쳐 호출됨
  - 그러면 **다형성**으로 인해 실제 **파생 클래스**의 함수가 실행
- 코드
  ```java
  public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
  }

  public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
  }

  public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
      switch (r.type) {
        case COMMISSIONED:
          return new CommisionedEmployee(r);
        case HOURLY:
          return new HourlyEmployee(r);
        case SALARIED:
          return new SalariedEmployee(r);
        default:
          throw new InvalidEmployeeType(r.type);
      }
    }
  }
  ```
- 일반적으로 `switch`는 한번은 참을만 함
  - **다형적 객체**를 생성하는 코드 안에서만
- 이렇게 **상속 관계**로 숨긴 후에는
  - 절대로 다른 코드에 노출하지 않음

### 서술적인 이름을 사용하라
- 코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 **깨끗한 코드**
- 이름이 길어도 괜찮음
- **길고 서술적인 이름 > 짧고 어려운 이름**
- **길고 서술적인 이름 > 주석**
- 여러 단어가 **쉽게 읽히는** 명명법
  - 이후, 여러 단어를 이용해 **함수 기능**을 잘 표현하는 이름
- 이름을 붙일때는 **일관성**이 있어야 함
- **모듈**내에서 **함수명**은 `같은 문구, 명사, 동사` 사용
  - `includeSetupAndTeardownPages`
  - `includeSetupPages`
  - `includeSuiteSetupPage`
- 위 내용들을 보고 `includeTeardownPages` 등이 있는지 질문이 생겼을때
  - 있다면, `짐작하는 대로`라면 좋음

### 함수 인수
- 이상적인 **인수 개수**는 `0개`
  - 이후 `1개, 2개`순
- `3개`는 가능한 피하는 편이 좋으며
- `4개`이상은 **특별한 이유**가 필요
  - 특별한 이유가 있더라도, 사용하면 안됨
- 인스턴스 변수로 선언하는 대신, 함수 인수로 넘겼을 때
  - 코드를 읽는 사람이 그 `인수를 해석`해야 함
  - 함수 이름과 인수 사이에 `추상화 수준`이 다를 수 있음
  - 코드를 읽는 사람이, `현 시점에 별로 중요하지 않은 세부사항`을 알아야 함
- **테스트 관점**에서 인수가 어려움
  - 인수의 수만큼 검증이 필요
- 어려움: **출력 인수 > 입력 인수**
  - 대개 함수에서 **인수**로 결과를 받으리라 기대하지 않음
- **최선은 인수가 없는 경우**
  - 차선은 **인수가 1개**인 경우

#### 많이 쓰는 단항 형식
- 함수에 인수를 `1개`를 넘기는 경우

##### 인수에 질문을 던지는 경우
- `boolean fileExists("MyFile")`

##### 인수로 뭔가를 **변환**하여 결과를 반환하는 경우
- `InputStream fileOpen("MyFile")`
  - `String`의 파일 이름을 `InputStream`으로 변환
- 함수 이름을 지을 때, **두 경우를 명확히 구분**
  - `명령/조회` 분리

##### 이벤트 함수
- `입력 인수`만 주로 있으며, `출력 인수`는 없음
- 프로그램은 함수 호출을 **이벤트**로 해석해,
  - **입력 인수**로 **시스템 상태**를 변경
- 예시
  ```java
  passwordAttemptFailedNtimes(int attempts)
  ```
- 이벤트 함수는 조심히 사용할 것
  - 이벤트라는 사실이 **코드에 명확하게 드러나야 함**
- **이름**과 **문맥**을 주의해서 사용하기
  
##### 이외 케이스는 피하기
- `void includeSetupPageInto(StringBuffer pageText)`와 같은 형식은 피해야 함
  - **변환 함수**에서 **출력 인수**를 사용하면 혼란스러움
  - **입력 인수**를 변환하는 함수라면, **변환 결과**는 **변환 값**으로 리턴
- `StringBuffer transform(StringBuffer in)`이 더 좋은 형식
  - 입력인수를 그대로 돌려준다 하더라도, **변환 함수 형식**을 따르는 편이 좋음
  - 최소한 **변환 형태**는 유지하기 때문

#### 플래그 인수
- 함수로 `boolean`값을 넘기는 관례는 X
  - **함수가 여러가지를 처리한다고 공표하기 때문**
- 예시
  ```java
  // Wrong!
  render(boolean isSuite)

  // Good!
  renderForSuite()
  renderForSingleTest()
  ```
  - 위와 같이 함수를 쪼개야 함

#### 이항 함수
- 인수가 `2개`인 함수는 `1개`인 함수보다 이해하기 어려움
  ```java
  // 아래 함수보다 이해하기 쉬움
  writeField(name)
  writeField(outputStream, name) // 첫 인자를 무시해야 하는데 시간이 걸림
  ```
  - 또한 `무시한다는 사실`에 오류가 숨어드는 경우가 많음
- 이항 함수가 적절한 경우?
  - `Point p = new Point(0, 0)`: 좌표계
    - 비교적 자연적인 값
- 단, `outputStream, name`은 한 값을 표현하지도, 자연적이지도 않음
- `assertEquals`함수에도 문제가 존재
  ```java
  // expected에 actual을 집어넣는 등의 오류..
  assertEquals(expected, actual)
  ```
  - `expected` 다음에 `actual`이 온다는 순서를 **인위적으로 기억해야함**
- 이항 함수는 되도록 **단항 함수**로 변경하기(3)
  - `writeField` 메서드를 `outputStream` 클래스 구성원으로 변경
    - `outputStream.writeField(name)`
  - `outputStream`을 현재 클래스 구성원 변수로 만들어 인수로 넘기지 X
  - `FileWriter`라는 새 클래스를 구성하여, 구성자에서 `outputStream`을 받고, `write`를 구현

#### 삼항 함수
- 신중히 고려해야 함
- 예외
  ```java
  // 부동 소수점 비교가 상대적이라는 의미
  assertEquals(1.0, amount, .001)
  ```

#### 인수 객체
- 인수가 `2~3`개 필요하다면, 일부를 **독자적인 클래스 변수**로 선언할 가능성을 짚어보기
  ```java
  Circle makeCircle(double x, double y, double radius);
  Circle makeCircle(Point center, double radius);
  ```
  - 결국 개념을 표현하게 되는 구조

#### 인수 목록
- 인수 개수가 **가변적**인 함수도 때로는 필요
  - `String.format`
- 사실상 이항함수
  ```java
  public String format(String format, Object... args)
  ```
- 가변함수를 취하는 모든 함수에, 원리가 적용됨
  - 가변 인수를 취하는 함수는 `단항, 이항, 삼항`함수로 취급할 수 있음
  - 단, 이를 넘어서는 인수를 사용할 경우엔 문제 발생
    ```java
    void monad(Integer... args)
    void dyad(String name, Integer... args)
    void triad(String name, int count, Integer... args)
    ```

#### 동사와 키워드
- **함수의 의도**나 **인수의 순서/의도**를 표현하려면
  - 좋은 함수 이름이 있어야 함
- `함수:인수 = 동사:명사`
  ```java
  // V(N)
  write(name)
  // Better good
  writeField(name)
  ```
- 함수 이름에 **키워드**를 추가하기
  - **함수 이름**에 **인수 이름**을 넣기
  - `assertEquals`보다 `assertExpectedEqualsActual(expected, actual)`가 좋음

### 부수 효과를 일으키지 마라
- 예상치 못한 클래스 변수의 변경등을 의미
- 함수로 넘어온 인수나, 시스템 전역변수 수정 등
  많은 케이스의 경우 **시간적인 결합**(teporal coupring)이나,
    **순서 종속성**(order dependecy)를 가지게 됨
- **시간적인 결합**을 가지게 되면
  - 해당 메서드는 **혼란**을 일으키게 됨
  - 특정 상황에서만 호출 가능하기 때문
- 시간적인 결합이 필요하다면 **함수명에 명시 필요**

#### 출력 인수
- 일반적으로 `인수 = 함수 입력`
  - 위와 같이 작업해야함
- 예시
  ```java
  appendFooter(s);
  
  // 위 메서드보다 직관적
  report.appendFooter(s);
  ```
- 일반적으로 **출력 인수**는 피해야 함
- 함수에서 **상태 변경**이 필요하다면
  - 함수가 속한 **객체 상태 변경 방식**을 택할 것

### 명령과 조회를 분리하라
- 함수는 아래 둘중에 하나의 로직만 수행 필요
  - 무언가를 수행하거나
  - 무언가에 답해야 함
- **둘다 하면 안됨**
- 혼란을 초래하는 예시
  ```java
  // attribute 속성을 찾아, value로 설정
  // 성공시 true, 아니면 false 반환
  public boolean set(String attribute, String value);

  // 모호한 코드, `set`이 동사인지, 형용사인지 애매함
  // username이 unclebob라면.. 정도로 해석될 수 있음
  if (set("username", "unclebob")) ...
  ```
- 예시 해결 방법: `명령/호출`을 분리하기
  ```java
  if (attributeExists("username")) {
    setAttribute("username", "unclebob");
    ...
  }
  ```

### 오류 코드보아 예외를 사용하기
- **명령 함수**에서 **오류 코드 반환 방식**은
  - **명령/조회 규칙**을 위반함
- 예시
  ```java
  if (deletePage(page) == E_OK)
  ```
  - 중첩되는 처리 유발
  - 오류 코드 반환시, 오류 코드를 바로 처리해야한다는 문제에 직면
- 위 방식을 **예외**를 처리하여 사용하면 깔끔해짐

#### Try/Catch 블록 뽑아내기
- `Try/Catch`는 본래 추함
  - 코드 구조에 혼란을 야기하며,
  - 정상 동작과 오류 처리 동작을 섞게됨
- 그에 따라, `Try/Catch`를 별도 분리하는게 좋음
  ```java
  public void delete(Page page) {
    try {
      deletePageAndAllReferences(page);
    } catch (Exception e) {
      logError(e);
    }
  }

  private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
  }

  private void logError(Exception e) {
    logger.log(e.getMessage());
  }
  ```
- **정상 처리**동작과 **오류 처리**동작을 구문

#### 오류 처리도 한 가지 작업이다
- 함수는 `한 가지` 작업만 해야 함
- 오류 처리 역시, `한 가지` 작업을 의미
- **오류를 처리하는 함수**는 **오류만 처리 해야함**
- 함수에 `try`가 있다면
  - `catch/finally` 구문으로 끝나야 함

#### Error.java 의존성 자석
- 오류 코드 반환
  - 어디선가 **오류 코드**를 정의한다는 의미
- 이는 **의존성 자석**(magnet)을 의미
- 다른 클래스에서 `Error` 클래스를 사용한다면,
  - `Error Enum`변경시, 클래스 전부를 **재컴파일**및 **재배치**
- 오류 코드 대신 **예외**를 사용하면
  - `Exception` 클래스에서 **파생**된 내용을 사용하게 됨
  - 그에 따라 **재컴파일/재배치**없이도 새 예외 클래스 추가 가능

### 반복하지 마라
- 중복은 문제
- 많은 기법이나 규칙들이 **중복**을 없애거나 제거할 목적으로 나옴
  - 관계형 데이터베이스에 **정규 형식**
  - 객체 지향 프로그래밍: 코드를 **부모 클래스**로 몰아넣음
  - 구조적 프로그래밍, AOP(Aspect Oriented Programming), COP(Component Oriented PRogramming) 역시 제거 전략

### 구조적 프로그래밍
- **모든 함수**와 **함수내 모든 블록**에
  - **입구**(entry)와 **출구**(exit)는 하나만 존재해야함을 의미
  - 함수는 `return`이 하나여야 함
- loop안에서 `break/continue`를 사용하면 안되며,
  - `goto`는 절대 X
- 단, `함수다 작다`면, 별 이득은 없음
  - `함수가 아주 클 때`만 상당한 이득
- 함수를 작게 만든다면
  - `return/break/continue`를 여러 차례 사용해도 좋음
  - 오히려 때로는, **단일 입/출구 규칙**(single entry-exit rule)보다 의도를 잘 표현함
  - `goto`는 피해야함

### 함수를 어떻게 짜죠?
- 소프트웨어를 짜는 행위는 **글짓기**와 유사
- 처음에는 길고 복잡(들여쓰기 단계도 많고, 중복 다수 존재, 인수도 긴 상황)
- 이후 **코드 다듬기, 함수 만들기, 이름 변경, 중복 제거**
  - 메서드는 줄이고 순서 변경
  - 전체 클래스 분할
  - 계속 **단위 테스트**는 통과해야함

### 결론
- 함수 작성의 목표
  - **시스템이라는 이야기를 풀어나가는데에 있음**
  - 함수가 분명하고, 정확한 언어로 깔금하게 같이 맞아 떨어져야
  - 이야기를 풀어가기가 쉬워짐

#### LIST.3.7. SetupTeardownIncluder
```java


public class SetTeardownIncluder {
  private PageData pageData;
  private boolean isSuite;
  private WikiPage testPage;
  private StringBuffer newPageContent;
  private PageCrawler pageCrawler;

  public static String render(PageData pageData) throws Exception {
    return rendder(pageData, false);
  }

  public static String render(PageDta pageData, boolean isSuite) throws Exception {
    return new SetupTeatdownIncluder(pageData).render(isSuite);
  }

  private SetupTearDownIncluder(PageData pageData) {
    this.pageData = pageData;
    testPage = pagaData.getWikiPage();
    pageCrawler = testPage.getPageCrawler();
    newPageContent = new StringBuffer();
  }

  private String render(boolean isSuite) throws Exception {
    this.isSuite = isSuite;
    if (isTestPage())
      includeSetupAndTeardownPages();
    return pageData.getHtml();
  }

  private boolean isTestPage() throws Exception {
    return pageData.hasAttribute("Test");
  }

  private void includeSetupAndTeardownPages() throws Exception {
    includeSetupPages();
    includePageContent();
    includeTeardownPages();
    updatePageContent();
  }

  private void includeSetupPages() throws Exception {
    if (isSuite)
      includeSuiteSetupPage();
    includeSetupPage();
  }

  private void includeSuiteSetupPage() throws Exception {
    include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
  }

  private void includeSetupPage() throws Exception {
    include("Setup", "-setup");
  }

  private void includePageContent() throws Exception {
    newPageContent.append(pageData.getContent());
  }

  private void includeTeardownPages() throws Exception {
    includeTeardownPage();
    if (isSuite)
      includeSuiteTeardownPage();
  }

  private void includeTeardownPage() throws Exception {
    include("TearDown", "-teardown");
  }

  private void includeSuiteTeardownPage() throws Exception {
    include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
  }

  private void updatePageConent() throws Exception {
    pageData.setContent(newPageContent.toString());
  }

  private void include(String pageName, String arg) throws Exception {
    WikiPage inheritedPage = findInheritedPage(pageName);
    if (inheritedPage != null) {
      String pagePathName = getPathNameForPage(inheritedPage);
      buildIncludeDirective(pagePathName, arg);
    }
  }

  private WikiPage findInheritedPage(String pageName) throws Exception {
    return PageCrawlerImpl.getInheritedPage(pageName, testPage);
  }

  private String getPathNameForPage(WikiPage page) throws Exception {
    WikiPagePath pagePath = pageCrawler.getFullPath(page);
    return PathParser.render(pagePath);
  }

  private void buildIncludeDirective(String pagePathName, String arg) {
    newPageContent
      .append("\n!innclude ")
      .append(arg)
      .append(" .")
      .append(pagePathName)
      .append("\n");
  }
}
```