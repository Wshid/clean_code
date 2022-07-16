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

### 디미터 법칙
- 잘 알려진 heuristic
  - **모듈**은 자신이 조작하는 객체의 속사정을 **몰라야함**
- **객체**는 자료를 숨기고 **함수를 공개**
  - 객체는 조회 함수로 **내부 구조 공개 X**
- 클래스 C의 메서드 `f`에 대해 다음과 같은 객체의 메서드만 호출해야 함
  - 클래스 C
  - f가 생성한 객체
  - f 인수로 넘어온 객체
  - C 인스턴스 변수에 저장된 객체
- 하지만 위 객체에서 허용된 **메서드**가 반환하는 **객체의 메서드**는 호출 X
- 디미터 법칙을 어기는 예시
  ```java
  // getOptions를 타고 getScratchDir을 타고.. 호출
  final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
  ```

#### 기차 충돌
- 위 예시가 **기차 충돌**(train wreck)의 예씨
  - 일반적으로 조잡, 피하는 편이 좋음
- 위 예시의 올바른 방식: 코드 나누기
  ```java
  Options opts = ctxt.getOptions();
  File scratchDir = opts.getScratchDir();
  final String outputDir = scratchDir.getAbsolutePath();
  ```
  - 하지만 위 코드 역시, 함수가 많은 객체를 탐색할 줄 안다는 의미
- 디미터 법칙을 위반하는가
  - `ctxt, Options, ScratchDir`이 **객체**인지 **자료 구조**인지에 따라 다름
    - 객체라면, 내부 내용을 숨겨야 하므로 **위반**
    - 자료구조라면, 당연히 내부 구조를 노출하므로, 디미터 법칙이 적용되지 x
- 위 예시에서는 `getter`를 사용하기에 모호
- 다음과 같이 구현시 디미터 법칙 거론 필요 x
  ```java
  final String outputDir = ctxt.options.scratchDir.absolutePath;
  ```
- 자료구조/객체 단순 정의
  - **자료 구조**는 무조건 함수 없이 **공개 변수**만 포함
  - **객체**는 **비공개 변수**와 **공개 함수**를 포함
- 하지만 단순한 자료 구조에도 `getter/setter`를 정의하라 요구하는
  - framework와 표준(e.g. bean)이 존재

#### 잡종 구조
- 위와 같은 혼란으로 때때로 절반은 **객체**, 절반은 **자료구조**인 잡종 구조가 나옴
- **잡종 구조**
  - 중요한 기능을 수행하는 함수 존재
  - 공개변수, 공개 조회/설정 함수 존재
  - 공개 조회/설정 함수는 **비공개 변수 노출**
- **잡종 구조의 단점**
  - 새로운 함수 및 자료 구조 추가하기 어려움
  - 여러가지 단점만 모은 구조

#### 구조체 감추기
- 만약 `ctxt, options, scratchDir`이 진짜 객체라면
  - 기차 구조를 가지면 안됨
  - 객체는 **내부구조**를 **숨겨야 하기 때문**
- 1차 개선 및 한계
  ```java
  // ctxt 객체에 공개해야 하는 메서드가 너무 많아짐
  ctxt.getAbsolutePathOfScratchDirectoryOption();

  // 객체가 아니라 자료 구조를 반환한다고 가정했을 때 
  ctxt.getScratchDirectoryOption().getAbsolutePath();
  ```
- `ctxt`가 **객체**라면 **뭔가를 하라고** 말해야지, 속을 드러내선 안됨
- 동일 모듈의 내부 코드 확인
  ```java
  String outFile = outputDir + "/" + className.replace('.', '/') + ".class";
  FileOutputStream fout = new FileOutputStream(outFile);
  BufferedOutputStream bos = new BufferedOutputStream(fout);
  ```
  - 다소 불편한 구조
    - `.`, `/`, `extension`, `File object`를 마구 섞은 상태
  - 임시 디렉터리의 절대 경로를 얻으려는 이유
    - 임시 파일을 생성하기 위함
- 2차 개선: `ctxt`에게 임시 파일을 생성하도록 한다면?
  ```java
  BufferedOutputStream bos = ctxt.createScratchFileSystem(classFileName);
  ```
  - 객체에게 맡기기 적당한 임부로 보임
  - `ctxt`가 내부구조를 드러내지 않으면서
    - 모듈에서 해당 함수는 자신이 몰라야 하는 **여러 객체 탐색 필요 x**
  - 디미터 법칙 위반 x

### 자료 전달 객체
- **자료 구조체**의 전형적인 형태는
  - **공개 변수**만 존재
  - **함수가 X**
  - 클래스
- 자료 구조체를 때때로 `DTO`(Data Transfer Object)라고 함
- 보통 `db`와 통신하거나, `socket`으로 받은 메세지 구문 분석시 유용
- `DTO`는 `db`에 저장된 **가공되지 않은 정보**를
  - `application code`에서 사용할 객체로 변환하는 **일련의 단계**에서 가장 처음으로 사용하는 구조체
- 또 다른 일반적인 형태: `bean` 구조

#### LIST.6.7. address.java
- `bean`은 `private` 변수를 `getter/setter`로 조작
- 일부 `OO 순수주의자`나 만족시킬 뿐, 별다른 이익 제공 x
```java
public class Address {
  private String street;
  private String streetExtra;
  ...

  public String getStreet() {
    return street;
  }

  public String streetExtra() {
    return streetExtra;
  }
}
```

#### 활성 레코드
- `DTO`의 특수한 형태
- **공개 변수**가 있거나 **비공개 변수**에 `getter/setter`가 있는 **자료 구조**
- 대게 `save/find`와 같은 **탐색 함수** 존재
- `db table`이나 `다른 소스`에서 **자료를 직접 변환한 결과**
- **활성 레코드**에 `비즈니스 규칙 메서드`를 추가해 이런 **자료 구조**를 **객체**로 취급하는 개발자가 많음
  - 바람직하지 않음
  - **잡종 구조**가 나오기 때문
- **해결책**
  - **활성 레코드**는 **자료 구조**로 간주
  - **비즈니스 규칙**을 담으면서 **내부 자료**를 숨기는 객체는 따로 생성
    - `내부 자료`는 `활성 레코드의 인스턴스`일 가능성이 높음

### 결론
- **객체**는 **동작 공개/자료 숨김**
  - 기존 동작을 변경하지 않으면서, **객체 타입**을 추가하기 쉬움
  - 기존 객체에 **새 동작 추가는 어려움**
- **자료구조**는 별다른 동작 없이 **자료 노출**
  - 기존 자료 구조에 **새 동작** 추가는 쉬움
  - 기존 함수에 **새 자료 구조 추가**는 어려움