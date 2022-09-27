# 49. 매개변수가 유효한지 검사하라

오류는 가능한 한 빨리 잡아라 (그렇지 않으면 발생 지점이나 오류 자체를 감지하기 어렵다)

⇒ input의 유효 조건은 반드시 문서화 + 메서드 진입하자마자 검사

### Failure Atomicity

*failure atomicity: 객체가 이상한 상태가 되려고 하면 무조건 exception을 던져서, 객체가 절대로 이상하거나 모호한 상태가 되지 않음을 보장

메개변수 검사를 안하면 아래 두 가지 중 하나가 발생하며 failure atomicity를 어기게 된다

- 메서드 수행 중 모호한 예외와 함께 실패
- 예외는 던지지 않으나, 객체를 이상한 상태로 만들어 미래에 관련없어 보이는 오류 발생

실제로 내가 failure atomicity를 위해서 코딩할 때는, 확인 코드가 쓸데없이 늘어나는 느낌이어서 자주 생략하게 된다.

그런데 제대로된 OOP 코드를 위해서는, 줄 수가 다소 늘어나더라도 늘 이런 input 확인을 해줘야하는 것 같다!

### 문서화

- public, protected 메서드는 input 값의 제약과, 잘못되었을 때 발생하는 예외까지 문서화해야 한다
- `@throws` 자바독 태그 사용
- exception 종류: IllegalArgumentException, IndexOutOfBoundsException, NullPointerException
    - 지금까지는 늘 IllegalArgumentException로 통일해서 던졌는데, 이렇게 종류별로 던지는게 더 읽기 편할 것 같다

### null check 및 범위 검사

- null check: `Objects.requireNonNull`
    - 예시
        
        ```java
        this.strategy = Objects.requireNonNull(strategy, "예외 메시지")
        ```
        
    - 자바 7부터
    - null 검사를 수동으로 하는 것보다 편하다
    - 입력값을 반환하므로, 값을 사용하는 동시에 null 검사 가능
- 범위 검사
    - `checkFromIndexSize` , `checkFromToIndex` , `checkIndex`
    - 리스트 & 배열 전용

### (공개되지 않은) 내부 메서드에서의 input 통제

- 메서드 input이 유효한 것을 우리가 보장할 수 있으니, `assert`로 유효성을 검증할 수 있다
- 클라이언트가 이 메서드들이 포함된 패키지를 어떻게 사용해도 무조건 참이 보장되어야 한다
- 일반적인 유효성 검사와의 차이
    - 실패 시 AssertionError
    - 런타임에 효과도, 성능저하도 없다

### 주의

- 메서드에서 직접 사용하지 않고, 나중에 사용하기 위해 저장하는 변수는 특히 신경써서 검사해야 한다
    - 사용할 때 exception이 발생하면 원인을 추적하기 어려우니, 변수를 받는 시점에서 모든 검사를 끝내자
- 생성자
    - 클래스 불변식을 어기는 객체가 만들어지지 않기 위해 꼭 유효성 검사를 하자
- 매개변수 유효성 검사의 예외
    - 유효성 검사 비용이 지나치게 높거나 계산과정에서 암묵적으로 검사가 수행될 때
    - 예시) Collections.sort(List) → 비교할 수 없는 타입들이 들어있다면 내부에서 Exception을 던지니, 호출 전에 비교 가능 여부를 확인 안해도 된다
- 유효성 검사시 던지는 예외가 문서화된 예외와 다르면 exception translate로 예외 번역

유효성을 얼마나 꼼꼼히 확인해야 실용적인지 아직 잘 모르겠지만, 지금으로써는 public은 늘 확인하기, private은 확인 안하거나 assert 쓰기를 하면 좋을 것 같다. 내가 이걸 알고 핀토스를 짜고 켄즈를 짰으면 과제 시간이 훨씬 줄지 않았을까ㅎㅎ

그리고 ML을 짜도 이렇게 유효성 검사를 하면 dimension 맞추기가 더 수월하지 않을까?