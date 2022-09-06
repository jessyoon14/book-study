# 1. 생성자 대신 정적 팩터리 메서드를 고려하라

### 클래스의 인스턴스를 얻을 수 있는 방법

1. public 생성자
2. 정적 팩터리 메서드 (static factory method)
    1. 생성자 외의 정적 메서드를 통해 객체 생성 (팩터리 디자인 패턴과는 다른 개념)
    2. 예시
        1. Boolean (boolean의 boxed class)
            1. boxed class: primitive type에 대한 wrapper class
                1. 참고: [https://enterkey.tistory.com/288](https://enterkey.tistory.com/288)
                2. 특징: primitive는 스택에, boxed primitive는 힙에 저장되므로 메모리 효율/접근속도는 primitive가 유리
                3. boxed의 장점
                    1. 명시적으로 null을 반환하고 싶을 때 (primitive는 default 값이 있음)
                    2. collection의 key/value로 사용 가능 (collection에는 primitive를 넣을 수 없다)
                    3. Parameterized types에서 사용 가능
                    4. (이외 primitive가 유리)
            
            ```java
            public static Boolean valueOf(boolean b) {
            	return b ? Boolean.TRUE : Boolean.FALSE;
            }
            ```
            
        2. TimeSpan(0,0,5,0,0) → TimeSpan.FromMinutes(), FromSeconds()
        3. enum의 valueOf (기본 타입을 받아 enum 객체 참조로 변경)

### 정적 팩터리 메서드의 장점

1. 이름을 가질 수 있다
    1. 생성자 + 매개변수보다 반환될 객체의 특성을 더 잘 표현
    2. 하나의 signature로는 하나의 생성자만 만들 수 있음 → 팩터리 메소드를 여러개 만들면 같은 시그니처가 여러개여도 헷갈리지 않음
    3. ex) BigInteger(int, int, Random) vs BigInteger.probablePrime
2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다
    1. 불필요한 객체 생성 감소
        1. immutable class: instance 미리 만들어 놓기 가능
        2. 인스턴스 캐싱 & 재활용 가능
        3. ex) enum의 valuOf (새로 생성하지 않음)
    2. instance-controlled class
        1. 언제 어느 인스턴스가 살아있는지 통제 (반복된 요청엔 같은 객체 반환)
        2. 좋은 점: singleton, noninstantiable 등 다양한 경우에 따라 한 값 당 instance가 한개만 생성되는 것을 보장할 수 있음
    3. 반환 타입의 하위 타입 객체 반환할 수 있음
        1. 반환할 객체의 클래스를 자유롭게 선택할 수 있음
        2. 자바 8부터 인터페이스에 정적 메서드 선언 가능
    4.  입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다
        1. 클라이언트는 팩터리가 반환하는 객체가 어느 클래스의 인스턴스인지 알 수 없고, 알 필요가 없다
        2. 팩터리나 반환 타입의 내부 구현이 바뀌어도 클라이언트는 전혀 영향이 없다
    5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다
        1. service provider framework을 만들기 좋음
            1. provider: 서비스의 구현체
            2. framework: 구현체들을 클라이언트에 제공하는 역할을 통제 (클라이언트를 구현체로부터 분리)
            3. 핵심 컴포넌트
                1. service interface: 구현체의 동작 정의
                2. provider registration API: 제공자가 구현체를 등록할 때 사용
                3. service access API: 클라이언트가 서비스의 인스턴스를 얻을 때 사용
                    1. 유연한 정적 팩터리
                    2. 클라이언트가 명시한 조건에 적합한 구현체 반환
                4. service provider interface: 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체 설명
            4. ex. JDBC (Java Database Connectivity)
                1. connection: service interface
                2. DriverManager.registerDriver: provider registration API
                3. DriverManager.getConnection: 서비스 접근 API
                4. Driver: 서비스 제공자 인터페이스

### 정적 팩터리 메서드의 장점

1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다
2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다

### 주로 사용하는 이름 규칙

1. from
2. of
3. valueOf
4. instance, getInstance
5. create, newInstance
6. getType
7. newType
8. type

### 한줄 요약

정적 팩터리 메서드와 public 생성자는 각자 장단점이 있으니 이해하고 사용해라

(그런데 정적 팩터리가 더 유리한 경우가 많으니 무작정 public 생성자는 쓰지 말자)

### 궁금증

- 단점이 너무 없는데, 그러면 public 생성자는 언제 써야하지?
    - 생성자가 딱 1개만 필요한 단순한 상황에서 써야하는 것 같다

### 소감

- 학교 과제에서 사용했던 코드들이(핀토스, 켄즈 등) 이런 규칙으로 짜여진 것이 보여서 신기함 (ex. OS 짤 때 프로젝트 4에서 사용했던 Files.newBufferedReader)