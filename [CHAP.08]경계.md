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