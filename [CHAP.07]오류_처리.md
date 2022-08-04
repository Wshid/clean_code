## [CHAP.07] 오류 처리
- **오류 처리**는 프로그램에 반드시 필요한 요소
- **오류 처리**는 중요하나,
  - 오류 처리 코드로 인해 **프로그램 논리**를 이해하기 어려워진다면 **깨끗한 코드**로 부르기 어려움

### 오류 코드보다 예외를 사용하라
- 오류가 발생하면 **예외**를 던지는 편이 좋음
  - 호출자 코드가 깔끔해짐

#### LIST.7.2. DeviceController.java (예외사용)
```java
public class DeviceController {
  ...

  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }
}
```

### Try-Catch-Finally 문부터 작성하라
- 어떤점에서 `try`블록은 **트랜잭션**과 유사
  - `try`에서 무슨일이 생기든 `catch`블록은 **일관성** 있게 유지해야 함
  - 예외가 발생할 코드를 짤 때는 `try-catch-finally`문으로 시작하는 편이 좋음
    - `try`블록에서 무슨 일이 생기든지, 호출자가 기대하는 상태를 정의하기 쉬워짐
- 강제로 **예외**를 일으키는 **테스트 케이스**를 작성 한후,
  - `테스트를 통과하게 될 코드`를 작성하는 법을 권함
  - 자연스럽게 `try`블록의 **트랙잭션 범위**부터 구현하게 되므로
    - 범위 내에서 **트랜잭션 본질**을 유지하기 쉬워짐

### 미확인(Unchecked) 예외를 사용하라
- 발생할 수 있는 모든 예외를 기입하면, 비용상 문제 발생
  - 예외 정의 및 리팩터링시 수정사항이 많아짐

### 예외에 의미를 제공하라
- **예외**를 던질 때는 **전후 상황**을 충분히 덧붙이기
  - 오류가 발생한 **원인**과 **위치**를 찾기 쉬워짐
- 오류 메세지에 `stacktrace`외 정보를 담아 던질 것

### 호출자를 고려해 예외 클래스를 정의하라
- **오류**를 정의할 떄 **오류를 잡아내는 방법**을 고려하기
- 외부 라이브러리가 던질 예외를 모두 `catch`문에 명시 x
  - 예외 대응 방식이 유사하다면, 하나의 에러 유형만 반환
    ```java
    // ACMEPort 클래스가 던지는 예외를 잡아 변환하는 wrapper 클래스
    LocalPort port = new LocalPort(12);
    try {
      port.open();
    } catch (PortDeviceFailure e) {
      reportError(e);
      logger.log(e.getMessage(), e);
    } finally {
      ...
    }
    ```
    ```java
    public class LocalPort {
      private ACMEPort innerPort;

      public LocalPort(int portNumber) {
        innerPort = new ACMEPort(portNumber);
      }

      public void open() {
        try {
          innerPort.open();
        } catch (DeviceResponseException e) {
          throw new PortDeviceFailure(e);
        } catch (ATM1212UnlockedException e) {
          throw new PortDeviceFailure(e);
        } catch (GMXError e) {
          throw new PortDeviceFailure(e)
        }
      }
      ...
    }
    ```
- **외부 API**를 사용할 때 유용한 방식
  - 외부 라이브러리와의 **의존성**이 크게 줄어듦
  - 테스트 하기도 쉬워짐
  - 특정 업체가 API를 설계한 방식에 발목 잡히지 않음
- 흔히 **예외 클래스** 하나만 있어도 충분한 코드가 많음
  - **예외 클래스**에 포함된 정보로 **오류**를 구분해도 괜찮은 경우
- `하나의 예외`는 잡아내고, 다른 예외는 무시해도 괜찮은 경우에는 `여러 예외 클래스`를 사용할 것

### 정상 흐름을 정의하라
- 때로는 **중단**이 적합하지 않을 때도 있음
- 예시 코드
  ```java
  try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += express.getTotal();
  } catch(MealExpensesNotFound e) {
    m_total += getMealPerDiem();
  }
  ```
  - 예외가 논리를 따라가기 어렵게 만듦
- 조금 더 간결한 코드
  ```java
  MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
  m_total += expenses.getTotal();
  ```
  - `ExpenseReportDAO`를 고쳐 언제나 `MealExpense` 객체를 반환
  ```java
  poublic class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
      // 기본값으로 일일 기본 식비 반환
    }
  }
  ```
- 위 코드 패턴을 **특수 사례 패턴**(Special Case Pattern)이라고 함
  - **클래스**를 만들거나 **깩체**를 조작해 **특수 사례**를 처리하는 방식
  - C에서 **예외적인 상황**을 처리할 필요가 없어짐
    - **클래스**나 **객체**가 **예외적인 상황**을 캡슐화하여 처리하기 때문

### null을 반환하지 마라
- `null`은 **호출자**에게 문제를 떠넘김
  - 누구 하나라도 `null` 확인을 빼먹으면 장애지점이 됨
- `null`을 반환하고 싶다면
  - **예외**를 던지거나, **특수 사례 객체**를 반환
- **외부 API**가 `null`을 반환한다면
  - **감싸기 메서드**를 구현해 예외를 던지거나
  - **특수 사례 객체**를 반환하는 방식을 고려
    - 많은 경우에 **특수 사례 객체**가 손쉬운 해결책
- 리스트를 반환하는 메서드에서도, **빈 리스트**를 반환하는 방식이 더 나음(`null`보다)
  - `Collections.emptyList()`

### null을 전달하지 마라
- 정상 인수로 `null`을 기대하는 API가 아니라면
  - `null`을 전달하는 코드는 최대로 피하기
- 기존 `NPE`가 발생하는 코드를 다음과 같이 1차 개선 - 새로운 예외 유형
  ```java
  public class MetricsCalculator
  {
    public double xProjection(Point p1, Point p2) {
      if (p1 == null || p2 == null) {
        throw new InvalidArgumentException("Invalid argument, ...");
      }
      return (p2.x - p1.x) * 1.5;
    }
  }
  ```
  - `NPE` 처리는 가능하나, `InvalidArgumentException`를 잡아내는 처리기가 필요
- 2차 개선 - `assert`문
  ```java
  public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
      assert p1 != null : "p1 should not be null";
      assert p2 != null : "p2 should not be null";
      return (p2.x - p1.x) * 1/5;
    }
  }
  ```
  - 코드 가독성은 좋으나, `null`을 전달할경우 여전히 **실행 오류**가 발생
- 대다수의 프로그래밍 언어는
  - **호출자**가 실수로 넘기는 `null`을 적절히 처리하는 방법이 X
- 그러기에, 애초에 `null`을 넘기지 못하도록 금지하는 정책이 합리적

### 결론
- **깨끗한 코드**는 읽기도 좋아야 하지만 **안정성**도 높아야 함
- **오류 처리**를 프로그램 **논리**와 분리하면
  - **독립적인 추론**이 가능해지며
  - 코드 **유지보수성**도 높아짐