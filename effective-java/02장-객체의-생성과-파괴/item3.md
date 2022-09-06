# 3. Private 생성자나 열거 타입(enum)으로 싱글턴임을 보증하라

### Singleton이란?

인스턴스를 하나만 생성할 수 있는 클래스

예시

- 무상태 객체 (ex. 함수)
- 설계상 유일해야하는 시스템 컴포넌트

문제: 클래스를 싱글턴으로 만들면, 이걸 사용하는 클라이언트를 테스트하기 어려움 (mocking 안됨)

### Singleton 생성 방식

- 1개만 생성하는 로직을 직접 구현
    - 공통: 생성자는 private으로 두고, public static 멤버로 인스턴스 접근
    - 방법 1: 싱글턴 객체를 public static final 변수로 지정
        
        ```java
        public class Elvis {
        	public static final Elvis INSTANCE = new Elvis();
        	private Elvis() {...}
        	public void leaveTheBuilding() {...}
        }
        ```
        
        - 장점
            - 싱글턴임이 API에 명확히 드러남
            - 간결함
    - 방법 2: 싱글턴 객체의 getter를 public static 함수로 제공
        
        ```java
        public class Elvis {
        	private static final Elvis INSTANCE = new Elvis();
        	private Elvis() {...}
        	public static Elvis getInstance() { return INSTANCE; }
        	public void leaveTheBuilding() {...}
        }
        ```
        
        - 장점
            - API 변경 없이 싱글턴 아니게 변경 가능
            - 원하면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음 (?)
- 방법 3
    - 원소가 1개인 enum type을 사용
    
    ```java
    public enum Elvis {
    	INSTANCE;
    	public void leaveTheBuilding() {..}
    }
    ```
    

### 한줄 요약

싱글턴을 쓸거면, 똑똑하게 사용하자

### 소감

코틀린에는 companion object 기능이 singleton을 관리해주니까 이런 패턴을 사용하지 않아도 될 것 같다.

코틀린이 진짜 좋은 언어인 것 같다.

그런데 singleton 기능을 위해서 enum을 사용하는 것은 아직 조금 부자연스럽게 느껴진다. (enum은 단순한 데이터만 저장해야한다고 생각했는데, enum에 복잡한 object를 넣어도 괜찮은 practice인지 좀 더 찾아볼 필요가 있다)