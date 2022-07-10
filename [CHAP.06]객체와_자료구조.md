## [CHAP.06] 객체와 자료구조
- 변수롤 `private`으로 정의하는 이유
  - 남들이 **변수**에 의존하지 X
- 그렇다면 왜 `get,set`함수를 `public`하게 설정할까?

### 자료 추상화
#### LIST.6.1. 구체적인 Point 클래스
```java
public class Point {
  public double x;
  public double y;
}
```
- 좌표값을 읽고 설정하도록 강제
- 구현을 노출함
- `private`하더라도 `getter/setter`를 노출하면, 구현을 외부로 노출하는 것
- 함수라는 **계층**을 넣더라도 구현이 감춰지지 않음
  - 구현을 감추려면 **추상화**가 필요함

#### LIST.6.2. 추상적인 Point 클래스
```java
public interface Point {
  double getX();
  double getY();
  void setCartesian(double x, double y);
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```
- `interface`는 자료구조를 **명백**하게 표현
  - 오히려 자료구조 **이상**을 표현함
  - 클래스 메서드가 **접근 정책**을 강제
    - e.g. 좌표를 읽을 때 각 값을 개별적으로 읽어야 함
- 결과적으로 **추상 인터페이스**를 제공해야
  - 사용자가 **구현**을 모른체 **자료의 핵심**을 조작할 수 있음

### LIST.6.3. 구체적인 Vehicle 클래스
```java
public interface Vehicle {
  double getFuelTankCapacityInGallons();
  double getGallonsOfGasoline();
}
```

### LIST.6.4. 추상적인 Vehicle 클래스
```java
public interface Vehicle {
  double getPercentFuelRemaining();
}
```
- 자료를 세세하게 공개하는 것보다 **추상적인 개념**으로 표현하는 것이 좋음
- `interface`나 `조회/설정`만으로는 **추상화가 이루어지지 X**
- **아무 생각 없이 조회/설정 함수 추가는 X**

### 자료/객체 비대칭
- 객체는 **추상화** 뒤로 자료를 숨긴 채
  - **자료를 다루는 함수**만 공개
- **자료 구조**는 자료를 그대로 공개, 함수를 별도로 제공 X
- 위의 두 정의는 **본질적으로 상반됨**

#### LIST.6.5. 절차적인 도형
```java
...
public class Geometry {
  public double area(Object shape) throws NoSuchShapeException {
    if (shape instanceof Square) {
      Square s = (Square)shape;
      return s.side * s.side
    }
    else if (shape instanceof Rectangle) {
      Rectangle r = (Rectangle) shape;
      return r.height * r.width;
    }
    else if ...
  }
}

```
- 클래스가 **절차적**
- 만약 `area`외 `perimeter()`와 같은 함수를 추가하게 되면, **도형 클래스**자체는 영향 받지 X
- 만약 새 **도형**을 추가하려면, `Geometry`클래스에 속한 함수를 모두 고쳐야 함

#### LIST.6.6. 다형적인 도형
```java
public class Square implements Shape {
  private Point topLeft;
  private double side;

  public double area() {
    return side*side;
  }
}

public class Rectangle implements Shape {
  private Point topLeft;
  private double height;
  private double width;

  public double area() {
    return height * width;
  }
}

...
```
- `area`는 다형(polymorphic) 메서드
  - `Geometry` 클래스와 연관 X
- 새 도형을 추가하더라도 **기존 함수 영향 X**
- 단, 새 함수를 추가하려면 **도형 클래스 전부를 고쳐야함**
- `LIST.6.5`, `LIST.6.6`은 상호보완 관계
  - 사실상 반대 성격

#### 정리
- **절차적인 코드**
  - `자료구조 변경 X, 새 함수 추가가 쉬움`
  - `새로운 자료구조 추가 어려움, 모든 함수 변경 필요`
- **객체 지향 코드**
  - `기존 함수 변경 X, 새 클래스 추가가 쉬움`
  - `새로운 함수 추가 어려움, 모든 클래스 변경 필요`
- 새로운 함수가 아닌 **새로운 자료 타입**이 필요할 때
  - **클래스**와 **객체 지향 기법**이 적합
- 새로운 자료 타입이 아닌 **새로운 함수**가 필요할 때
  - **절자적인 코드**과 **자료 구조**가 더 적합
- 분별있는 프로그래머는
  - **모든 것이 객체라는 생각이 미신**임을 앎
  - 때로는 `단순한 자료 구조`와 `절차적인 코드`가 가장 적합한 상황도 존재