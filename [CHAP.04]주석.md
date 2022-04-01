## [CHAP.04] 주석
- 오래되고 조잡한 주석은
  - 거짓과 잘못된 정보를 퍼트려 해악을 끼침
- 사실상 **주석은 필요악**임
  - PL 자체가 표현력이 풍부하다면, 주석은 거의 필요하지 않음
- 코드를 의도로 표현하지 못하기 때문에 **주석**을 사용
  - 주석은 **실패**를 의미
- 주석이 필요한 상황일 때 고려해야 할 부분
  - 코드를 **의도**로 표현할 방법은 없을지
- 주석은 오래될수록 **코드에서 멀어짐**
- 주석이 필요 없을 수록 좋음

### 주석은 나쁜 코드를 보완하지 못함
- 코드에 주석을 포함하는 일반적인 이유
  - **코드 품질**이 나쁘기 때문
- `주석을 달아야겠다`가 아닌 `코드 품질을 수정`할 것

### 코드로 의도를 표현하라
- 비교
  ```java
  // bad
  if ((employee.flags & HOURLY_FLAG) && (employee.age > 65)) {
    ...
  }

  // good
  if (employee.isEligibleForFullBenefits()) {
    ...
  }
  ```
- 코드로 달려는 설명을 **함수**로 만들어 표현해도 충분함

### 좋은 주석
- 어떤 주석은 필요하거나 유익함

#### 법적인 주석
- 회사가 정립한 구현 표준에 맞춰 법적인 이유로 넣는 경우
  - 소스 파을 첫머리에 주석으로 들어가는
  - **저작권/소유권** 정보

#### 정보를 제공하는 주석
- 기본적인 정보를 주석으로 제공하면 편리
- e.g. 추상 메서드가 반환할 값 설명
  ```java
  // 테스트 중인 Responder 인스턴스를 반환
  protected abstract Responder responderInstance();
  ```
  - 단, 위 내용은 함수 이름에 그 내용을 담으면 더 좋음
    - `responderBeingTested`
- e.g. 정규표현식의 의미
  ```java
  // kk:mm:ss EEE, MMM dd, yyy 형식이다
  Pattern timeMatcher = Pattern.compile(
    "\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*"
  )
  ```
  - 이보다는, `시간`과 `날짜`를 변환하는 클래스를 만들어 코드를 옮기면, 더 좋고 깔끔해짐
    - 주석이 필요 없어짐

#### 의도를 설명하는 주석
- 주석이 구현 이해 뿐 아니라, 결정에 깔린 의도까지 설명
- e.g. 두 객체를 비교시, 어떤 객체보다, 자기 객체에 높은 우선순위를 주기
  ```java
  public int compareTo(Object o) {
    if(o isntanceof WikiPagePath) {
      WikiPagePath p = (WikiPagePath) o;
      String compressedName = StringUtil.join(names, "");
      String compressedArgumentName = StringUtil.join(p.names, "");
      String compressedName.compareTo(compressedArgumentName);
    }
    return 1; // 오른쪽 유형이므로 정렬 순위가 더 높음
  }
  ```
  ```java
  // 위 코드보다 나은 예시
  public void testConcurrentAddWidgets() throws Exception {
    WidgetBuilder widgetBuilder = new WidgetBuilder(new Class[]{BoldWidget.class});
    String text = "'''bold text'''";
    ParentWidget parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
    AtomicBoolean failFlag = new AtomicBoolean();
    failFlag.set(false);

    // 스레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도
    for (int i = 0; i< 25000; i++) {
      WidgetBuilderThread widgetBuilderThread = new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
      Thread thread = new Thread(widgetBuilderThread);
      thread.start();
    }
    assertEquals(false, failFlag.get());
  }
  ```