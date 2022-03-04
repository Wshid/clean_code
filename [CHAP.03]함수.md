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

#### List.3.5. Employee and Factory
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