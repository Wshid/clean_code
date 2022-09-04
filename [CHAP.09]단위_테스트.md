## [CHAP.09] 단위 테스트
- TDD(Test Driven Development)

### TDD 법칙 세 가지
- 실제 코드를 짜기 전 **단위 테스트**부터 작성
- 법칙 세 가지
  - `실패하는 단위 테스트를 작성`할 때까지 실제 코드를 작성하지 X
  - 컴파일은 실패하지 않으면서
    - `실행이 실패하는 정도`로만 단위테스트 작성
  - 현재 실패하는 테스트를 `통과할 정도로만 실제 코드 작성`
- 위 법칙을 준수하면
  - 테스트 코드가 실제 코드와 함께 나옴
- 실제 코드가 맞먹을 정도로 많은 테스트는
  - 오히려 **관리 문제**를 유발하기도 함

### 깨끗한 테스트 코드 유지하기
- 테스트 코드가 지저분하면, 변경하기 어려워짐
- 테스크 코드가 복잡하다면, 테스트 케이스를 추가하는데 시간 소요가 많아짐
- 새 버전을 출시할 때마다,
  - TC를 유지 보수 하는 비용이 점차 늘어남
- 하지만 Test Suite가 없다면
  - **결함율**이 높아지게 됨
- **테스트 코드**는 **실제 코드** 못지 않게 중요함
  - 단 **깨끗하게 작성**해야함

#### 테스트는 유연성, 유지보수성, 재사용성을 제고
- 코드에 **유연성, 유지보수성, 재사용성**을 제공하는 버팀목이 **단위 테스트**
  - 테스트가 있다면 변경이 두렵지 않음
  - TC가 없다면, 모든 변경이 **잠정적인 버그**
- **Test Coverage**가 높을수록 변경 공포가 사라짐
  - 안심하고 아키텍쳐, 설계 변경이 가능
- **테스트 케이스가 있으면 변경이 쉬워짐**
- 테스트 코드가 지저분하면, 코드를 변경하는 능력이 떨어지며
  - 구조 개선 능력도 떨어짐

### 깨끗한 테스트 코드
- **가독성**이 좋아야 함
  - 오히려 실제 코드보다, 테스트 코드에서 더 중요
- 가독성을 높이려면?
  - **명료성, 단순성, 풍푸한 표현**
  - 최소의 표현으로 많은 것을 나타내기

##### LIST.9.2. SerializedPageResponderTest.java (리팩터링한 코드)
- 최대한 코드 잡음을 없앰, 중복 제거
```java
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildONe", "PageTwo");
  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", ...
  );
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
  WikiPage page = makePage("PageOne");
  makePages("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", ...
  );
  assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
  makePageWithContent("TestPageOne", "test page");
  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test");
```
- **BUILD-OPERATE-CHECK** 패턴이 위와 같은 **테스트 구조**에 적합
- 테스트는 세 부분으로 나뉨
  - **테스트 자료 생성**
  - **테스트 자료 조작**
  - **조작한 결과가 올바른지 확인**
- 잡다하고 세세한 코드가 모두 없어짐
  - 테스트 코드는 **본론**에 돌입해 **진짜 필요한 자료유형과 함수만 사용**
- 코드가 수행하는 기능을 이해하기 편함

#### 도메인에 특화된 언어
- **LIST.9.2**는 도메인 특화 언어(DSL)
  - API위에 **함수**와 **유틸리티**를 구현한 후, 그 함수와 유틸리티를 활용
    - 직접 시스템 조작 API를 활용하지 X
  - 테스트 코드를 짜기도, 읽기도 쉬움
- 구현된 **함수**와 **유틸리티**는
  - 테스트 코드에서 사용하는 **특수 API**가 됨
    - 테스트를 구현하는 당사자와 독자를 도와주는 테스트 언어
- 이런 테스트 API는 **처음부터 설계된 API X**
  - 잡다하고 세세한 사항으로 범벅된 코드를 계속 **리팩터링**하다가 진화된 API

#### 이중 표준
- **테스트 API** 코드에 적용하는 표준은
  - **실제 코드**에 적용하는 표준과 **다르다**
- 테스트는
  - **단순**, **간결**, **표현력 풍부**
  - 실제 코드처럼 **효율적 X**
    - 실제 환경이 아닌 테스트 환경에서 돌아가는 코드이기 때문
  - 실제 환경과 테스트 환경은 **요구사항**이 **다름**

##### LIST.9.3. EnvironmentControllerTest.java
- 프로토타입으로 제작하던 환경 제어 시스템에 속한 테스트 코드
  - 자세한 설명 없이도, 어떤 의미를 담는 코드인지 읽힘
- 코드
  ```java
  @Test
  public void turnOnLoTempAlarmAtThreshold() throws Exception {
    hw.setTemp(WAY_TOO_COLD);
    controller.tic();
    assertTrue(hw.heaterState());
    assertTrue(hw.blowerState());
    assertFalse(hw.coolerState());
    assertFalse(hw.hiTempAlarm());
    assertTrue(hw.toTempAlaram());
  }
  ```
  - 세세한 과정이 많으나, 최종 상태가 `온도가 급강하 하는지`에만 신경쓰면 됨
- 한계
  - 코드에서 점검하는 **상태 이름**과 **상태 값**을 확인하느라 가독성이 떨어짐
    - 이 상태일때 `assertTrue`인지.. `False`인지

##### LIST.9.4. EnvironmentControllerTest.java (리팩터링)
```java
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState());
}
```
- `tic`함수는 `wayTooCold`라는 함수를 만들어 숨김
- `HBchL`
  - 대문자는 켜짐, 소문자는 꺼짐을 의미
    - `heater, blower, coller, hi-temp-alarm, lo-temp-alarm` 순서
  - **그릇된 정보를 피하라**라는 규칙 위반에 가가우나
    - 의미만 안다면 직관적

##### LIST.9.5. EnvironmentControllerTest.java (더 복잡한 선택)
```java
@Test
public void turnOnCollerAndBlowerIfTooHot() throws Exception {
  tooHot();
  assertEquals("hBChl", hw.getState());
}

@Test
public void turnOnHeaderAndBlowerIfTooCold() throws Exception {
  tooCold();
  assertEquals("HBchl", hw.getSTate());
}
...
```
- 테스트 코드가 이해하기 쉬워짐

##### LIST.9.6. MockControlHardware.java
```java
public String getState() {
  String state = "";
  state += heater ? "H" : "h";
  state += blower ? "B" : "b";
  ...
  return state;
}
```
- 코드가 그렇게 효율이 좋지는 못함
  - `StringBuffer`를 사용해야 하는 등
- `StringBuffer`는 보기 어려움
  - 애초에 해당 어플리케이션은 **실시간 임베디드 시스템**
  - 컴퓨터 자원과 메모리가 제한적일 가능성이 높음
  - 단, 테스트 코드의 경우 **자원이 제한될 가능성이 낮음**
- 이것이 바로 **이중 표준의 본질**
  - 실제 환경에서는 절대로 안되지만, 테스트 환경에서는 문제 없는 방식
- 대개 **메모리**나 **CPU** 효율과 관련이 있는 경우
- **코드의 꺠끗함과는 전혀 무관**
