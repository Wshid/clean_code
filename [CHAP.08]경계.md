## [CHAP.08] 경계
- 시스템을 개발할 때, 외부 소스를 활용하게 됨

### 외부 코드 사용하기
- 인터페이스 긴장
  - 제공자: 적용성을 최대한 넓히기
  - 사용자: 자신의 요구에 집중하는 인터페이스
- e.g. `java.util.Map`에는 다양한 인터페이스를 제공
  - 그만큼 **기능성**과 **유연성**이 큼
  - 어떤 객체나 담을 수 있음
  - `clear()`과 같은 내용 임의 삭제 기능도 제공
- 예시
  ```java
  // Map이 반환하는 Object 변환 책임이 client에 존재
  Map sensors = new HashMap();
  Sensor s = (Sensor)sensors.get(sensorId);

  // Generics 사용, but Map<String, Sensor>가 사용자에게 필요하지 않는 기능까지 제공하는 문제 존재
  Map<String, Sensor> sensors = new HashMap<Sensor>();
  ...
  Sensor s = sensors.get(SensorId);
  ```
  - 프로그램상 `Map<String, Sensor>` 인스턴스를 여기저기로 넘기게 되면
    - `Map`인터페이스가 변할 경우 수정할 코드가 상당히 많아짐
    - 실제 `java 5`가 `generics`를 지원하면서 `Map` 인터페이스가 변경됨
- `Map`을 잘 사용한 예시
  - `Sensors` 사용자는 `generics`가 사용되었는지 여부에 신경 쓸 필요가 X
  - `generics`의 사용 여부는 `Sensors`안에서 결정
  ```java
  public class Sensors {
    private Map sensors = new HashMap();

    public Sensor getById(String id) {
      return (Sensor) sensors.get(id);
    }
  }
  ```
  - **경계 인터페이스**인 `Map`을 `Sensor`안으로 숨김
    - `Map`인터페이스가 변하더라도, 나머지 프로그램에는 영향을 미치지 X
  - `Sensor`클래스는 프로그램에 **필요한 인터페이스만 제공**
  - `Map` 그리고 `Map`과 같은 **경계 인터페이스**를 이용할 때는
    - 이를 이용하는 **클래스**나 **클래스 계열 밖**으로 **노출되지 않도록 주의**
  - `Map` 인스턴스를 **공개 API의 인수**로 넘기거나 **반환값**으로 사용하지 X

### 경계 살피고 익히기
- **외부 패키지**여도 테스트 필요
- 개발자가 코드를 작성하여 **외부 코드**를 호출하는 대신에,
  - 간단한 **테스트 케이스**를 작성해, **외부 코드**를 익히는 방식
  - 이를 **학습 테스트**라고 함
- 학습 테스트는
  - 프로그램에서 사용하려는 방식대로 외부 API를 호출
  - 통제된 환경에서 `API`를 제대로 이해하는지 확인하기 위함
    - **사용하려는 목적**에 초점

### log4j 익히기
- log4j를 적용하기 위한 여러가지 테스트 이후 나온 결과
  ```java
  public class LogTest {
    private Logger logger;

    @Before
    public void initialize() {
      logger = Logger.getLogger("logger");
      logger = removeAllAppenders();
      Logger.getRootLogger().removeAllAppenders();
    }

    @Test
    public void basicLogger() {
      BasicConfigurator.configure();
      logger.info("basicLogger");
    }

    @Test
    public void addAppenderWithStream() {
      logger.addAppender(new ConsoleAppender(
        new PatternLayout("%p %t %m%n"),
        ConsoleAppender.SYSTEM_OUT));
      logger.info("addAppenderWithStream");
    }

    @Test
    public void addAppenderWithoutStream() {
      logger.addAppender(new ConsoleAppender(
        new PatternLayout("%p %t %m%n")));
        logger.info("addAppenderWithoutStream");
    }
  }
  ```
- 하나를 구현하면서, `log4j`를 전체적으로 파악할 수 있었음

### 학습 테스트는 공짜 이상이다
- 학습 테스트에 드는 비용은 X
  - 어쨌든 API를 배워야 하기 때문
- 필요한 지식만을 확보하는 손쉬운 방법
- **학습 테스트**는 **이해도**를 높여주는 정확한 실험
  - 투자보다 노력하여 얻는 성과가 더 큼
- 패키지의 **새 버전**이 나올 경우,
  - **학습 테스트**를 돌려 차이가 있는지 확인 가능
- **학습 테스트**는 패키지가 **예상**대로 도는지 확인
  - 새 버전이 우리 코드와 호환되지 않으면 **이 사실이 바로 드러남**
- **실제 코드**와 동일한 방식으로 **인터페이스**를 사용하는
  - **테스트 케이스**가 필요함
- **경계 테스트**가 있다면 패키지의 **새 버전**으로 이전하기 쉬워짐

### 아직 존재하지 않는 코드를 사용하기
- **경계**의 또 다른 유형
  - `아는 코드 / 모르는 코드`를 분리
  - 모르는 코드를 아예 인터페이스로 분리
  - 이후 아는 부분만을 작업
  - 차후 인터페이스로 연동(타 부서와)
- 우리가 바라는 **인터페이스**를 구현하면
  - 우리가 전적으로 통제 한다는 장점이 생김
  - **가독성**과 **코드 의도**가 드러남
- 우리에게 필요한 **인터페이스**를 정의(e.g. 받을 객체)
  - `ADAPTER pattern`으로 **API 사용**을 캡슐화 하여
  - `API`가 바뀔 때 수정한 코드를 한 곳으로 모음
- 이렇게 설계시, 테스트도 매우 편함
  - 이후 **경계 테스트 케이스**를 생성하여
  - 우리가 API를 올바로 사용하는지 테스트 가능

### 깨끗한 경계
- 소프트웨어 설계가 우수하다면
  - **변경**하는데 많은 투자와 재작업이 필요하지 X
  - 엄청난 시간, 노력, 재작업 요구 X
- 통제하지 못하는 코드를 사용할 경우
  - **너무 많은 투자**를 하거나 **향후 변경 비용**이 지나치게 커지지 않도록 각별히 주의 필요
- **경계**에 위치하는 코드는 **깔끔하게 분리**
- **기대치**를 정의하는 **테스트 케이스**도 작성
- 이쪽 코드에서 **외부 패키지**를 세세하게 알아야 할 필요가 X
- 통제가 불가능한 **외부 패키지**에 의존하는 대신
  - 통제가 가능한 우리 코드에 의존하는 것이 훨씬 좋음
- **외부 패키지를 호출하는 코드를 가능한 줄여 경계를 관리**
- `Map`과 같이 새로운 클래스로 **경계**를 감싸거나
  - `ADAPTER`패턴을 사용하여 우리가 원하는 인터페이스를
    - **패키지가 제공하는 인터페이스**로 변환
- 어느 방법이든 **가독성**이 높아지며
  - **경계 인터페이스**를 사용하는 **일관성**도 높아지며
  - **외부 패키지**가 변했을 때 **변경할 코드가 줄어듦**