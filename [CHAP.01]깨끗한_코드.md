## [CHAP.01] 깨끗한 코드
- 나쁜 코드
  - 나중에 다시 정리한다?
  - 르블랑의 법칙(**leblanc's Law**)
  - 그 때는 다시는 오지 않음
- 나쁜 코드로 치르는 대가
  - 나쁜 코드가 쌓일수록 팀 생산성이 떨어짐
  - 생산성이 떨어지면 관리층은 나름대로 복구 시도
  - 생산성을 증대시키기 위해 인력 투입
    - 시스템 설계에 대해 조예가 깊지 않음
  - 설계 의도에 맞는 변경과 설계 의도에 반하는 변경을 구분하지 못함
  - 생산성을 높여야한다는 압박감
  - 더 나쁜 코드 많이 양산
- 원대한 재설계의 꿈
- 나쁜 코드의 위험을 이해하지 못하는 관리자 말을
  - 그대로 따르는 행동은 전문가 다빚 못함
- 원초적 난제
  - 빠르게 코드를 작성하려면, 결국 최대한 깨끗하게 코드를 유지해야 함
- **깨끗한 코드**란
  - 의존성을 최대한 줄이기
  - 성능을 최적으로 유지해야 사람들이 원칙 없는 최직화로 코드를 망치려는 유혹에 빠지지 않음
  - 보기에 즐거운 코드가 되어야 함
    - 깨끗한 코드
  - CPU자원을 낭비하지 않는 코드
- 깨진 창문
  - 창문이 깨진 건물은 누구도 상관하지 않음
  - 사람들은 관심을 끊음
  - 더 깨져도 무방
  - 이후에 자발적으로 창문을 깸
  - 창문이 깨지고 나면, 쇠퇴하는 과정이 진행됨
- 오류 처리가 잘 되어야 함
  - 메모리 누수, 경쟁 상태, 일관성 없는 명명법 등도 마찬가지
  - **세세한 사항까지 꼼곰하게 처리하는 코드**
- **한 가지를 잘하는 코드**
- **가독성이 좋은 코드**
  - 명쾌한 추상화, 단순한 제어문으로만 존재
    - 명쾌한(crisp) = 구체적인(concrete)
  - 설계자의 의도를 숨기지 않음
  - 어떤 **소설**을 읽듯이
- **다른 사람이 고치지 쉬운 코드**
- **테스트 케이스가 존재하는 코드**
- **주의 깊게 짠 코드**
  - 시간을 들여 깔끔하고 단정하게 정리한 코드
- **간단한 코드**
  - 모든 테스트를 통과
  - 중복이 없음
  - 시스템 내 모든 설계 아이디어 포함
  - 클래스, 메서드, 함수를 최대한 줄임
- 객체가 여러 기능을 수행 => 여러 객체로 나눔
- 메서드가 여러 기능을 수행 => **메서드 추출**(extract method)을 통해 아래와 같이 분할
  - 기능을 명확히 기술하는 메서드
  - 기능을 실제로 수행하는 메서드
- 중심 사항
  - **중복 줄이기**
  - **표현력 높이기**
  - 초반부터 **간단한 추상화** 고려하기
  - **한가지 기능만 수행하기**
- **짐작했던 기능**을 각 루틴이 수행한다면 깨끗한 코드
  - 예상대로 움직여야 하며, 놀랄만한 일이 없어야 함