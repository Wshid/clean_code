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