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

#### 의도를 명료하게 밝히는 주석
- 때때로 모호한 인수나 반환값은 의미를 읽기 좋게 표현하면 이해가 쉬워짐
- 인수나 반환값이 표준 라이브러리나 `변경하지 못한 코드`로 속한다면
  - 의미를 명료하게 밝히는 주석이 유용
- 물론 `그릇된 주석`을 달아놓을 위험은 높음
- 이 주석을 달 때 더 나은 방법이 없는지 고민하고 정확히 달 것

#### 결과를 경고하는 주석
- 다른 프로그래머에서 결과를 경고할 목적으로 주석 사용
```java
// 여유 시간이 충분하지 않다면 실행하지 말 것
public void _testWithReallyBigFile() {
  writeLines...
}
```
- 물론 요즘에는 `@Ignore`속성을 이용하여 test case를 끌 수 있음
- 구체적인 설명은 `@Ignore`속성에 문자열로 넣어 줌
- `junit4`가 나오기 전까지는
  - 메서드 이름 앞에 `_`로 표기
- 적절한 예제
  ```java
  public static SimpleDateFormat makeStandardHttpDateFormat() {
    // SimpleDateFormat은 스레드에 안전하지 못함
    // 따라서 각 인스턴스를 독립 생성 필요
    SimpleDateFormat df = new SimpleDateFormat("...");
    ...
  }
  ```
  - 더 나은 해결책이 있을 수 있겠으나, 여기서는 주석이 합리적
  - 프로그램 효율을 높이기 위해 `정척 초기화 함수`를 사용하려던 프로그래머가 **주석**으로 실수를 면함

#### TODO 주석
- 앞으로 할일
  ```java
  // TODO-MdM 현재 필요하지 않음
  // 체크아웃 모델 도입시 함수 필요 없음
  protected VersionInfo makeVersion() throws Exception {
    return null;
  }
  ```
- 당장 프로그래머가 구현하기 어려운 업무를 기술
  - 더 이상 필요없는 기능 삭제
  - 누군가에게 문제를 봐달라는 요청
  - 더 좋은 이름 개선 부탁
  - 발생할 이벤트에 맞춰 코드를 고치기
- 단, 어떤 용도로 사용하던
  - `나쁜 코드를 남겨놓는 핑계가 되어선 안됨`
- IDE에서 `TODO`를 모아보는 기능을 제공하여, 주석을 잊을 염려는 없으나
  - `TODO`로 도배된 코드는 좋지 않음
- 주기적으로 `TODO` 주석을 점검해
  - 없애도 괜찮은 주석은 없애라고 권함

#### 중요성을 강조하는 주석
- 중요하지 않다고 여겨질 내용에 대해 중요성을 강조하기 위함
  ```java
  String listItemContent = match.group(3).trim();
  // trim은 매우 중요, trim은 문자열에서 시작 공백을 제거
  // 문자열 시작 공백 존재시, 다른 문자열로 인식하기 때문
  new ListItemWidget(this, listItemContent, this.level + 1);
  return buildList(text.substring(match.end()));
  ```

#### 공개 API에서 JavaDocs
- 공개 API를 구현한다면, `JavaDocs`를 작성할 것
- 물론 어느 주석과 마찬가지로
  - 독자를 오도하거나
  - 잘못 위치하거나
  - 그릇된 정보를 전달할 가능성이 존재
