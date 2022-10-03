# 72. 표준 예외를 사용하라

### 표준 예외 사용의 장점

- 자바 표준 라이브러리가 재사용하기 좋은 예외들을 제공
- 다른 사람에게 가독성 ⬆️
- 예외 클래스가 적으면 메모리 사용량 ⬇️, 클래스 적재시간 ⬇️

→ 상황에 부합한다면 항상 표준 예외를 재사용하라!

(API 문서를 참고해서 예외가 던져지는 맥락이 부합하는지 꼭 확인하기)

### 셀프 반성

인턴 프로젝트를 할 때는 예외를 직접 정의해서 쓰는 것이 보기 좋다고 생각했는데, 그 수준에서 굳이 직접 정의해서 얻는 것은 없는 것 같다. 앞으로는 표준 예외를 사용해야겠다!

### 자주 사용하는 에외

- IllegalArgumentException: 호출자가 인수로 부적절한 값을 넘길 때
- NullPointerException: null 값이 허용되지 않는 메서드에 null이 들어올 때 (Illegal argument의 특수케이스)
- IndexOutOfBoundsException: Illegal argument의 특수케이스
- IllegalStateException: 대상 객체의 상태가 호출된 메서드를 수행하기에 적합하지 않을 때
- ConcurrentModificationException: 단일스레드용 객체를 여러 스레드에서 동시 수정할 때
- UnsupportedOperationException: 호출한 메서드를 지원하지 않을 때

### 주의

Exception, RuntimeException, Throwable, Error는 직접 재사용하지 말라

(여러 예외들을 포괄하는 상위 클래스라서 안정적으로 테스트할 수 없다. 추상 클래스라고 생각하기)

### 나만의 예외를 만들고 싶다면?

- 더 많은 정보를 제공하고 싶으면 표준 예외를 확장해도 좋다
- 그런데 직렬화를 제공해야하는데 이게 부담스러우니 새로 만들지 않는 것을 추천

### IllegalState vs IllegalArgument: 어느 예외를 사용할지 애매하다면?

- 인수 값이 무엇이든 어차피 실패했을거라면 IllegalStateException
- 그렇지 않다면 IllegalArgumentException