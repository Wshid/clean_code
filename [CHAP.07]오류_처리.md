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
