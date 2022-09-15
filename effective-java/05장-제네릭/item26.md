# 26. 로 타입은 사용하지 말라

### 제네릭 타입이란?

- 제네릭 클래스 & 제네릭 인터페이스처럼 클래스 / 인터페이스 선언에 type parameter를 사용한 것
- 예시: List<E> → E는 parameterized type

### 로 타입이란?

- 제네릭 타입을 정의하면, 연관된 raw type도 정의된다
    - raw type: type parameter를 사용하지 않은 generic type
    - 목적: generic 등장 전 코드와 호환
    - 예시: List<E>의 raw type은 List
- 쓰지 말아야 하는 이유
    - 로 타입을 쓰면 제네릭이 주는 안전성 & 표현력을 잃는다
- 절대 쓰면 안되는 로 타입을 만들어놓은 이유
    - 호환성 (자바는 10년간 제네릭이 없었다)

안좋은 예시

```java
// raw type을 사용하면 stamp 외 객체도 컴파일/실행이 된다
// 그러니 쓰지 말아라!
private final Collection stamps = ...

// raw type이라 컴파일러 오류가 나지 않음ㅠㅠ
stamps.add(new Coin(..)); 

// 컴파일러에서 꺼낼때 오류 발생
Stamp stamp = (Stamp) i.next(); // ClassCastException 발생
```

좋은 예시

```java
private final Collection<Stamp> stamps

// 여기서는 compiler error
stamps.add(new Coin(..)); 
```

### raw type 대체제 1: 임의 객체 허용

`List` (raw type)은 사용하면 안되지만, `List<Object>` (임의 객체를 허용)은 괜찮다

raw type vs 임의 객체 허용의 차이점

- **List<Object> (parameterized type)을 사용하지 않고 List (raw type)을 사용하면 타입 안전성을 잃는다**
- `List` : 제네릭 타입을 사용하지 않음
- `List<Object>` : 모든 타입을 허용하겠다는 의사를 컴파일러에게 명확히 전달함
- 제네릭 타입의 하위 타입 규칙
    - List<String>은 List의 하위타입이다
    - List<String>은 List<Object>의 하위타입이 아니다
    - 그래서
        - List를 받는 메소드는 List<String>을 줘도 괜찮다
        - List<Object>를 받는 메서드에 List<String>을 줄 수 없다

### raw type 대체제 2: Unbounded wildcard type

unbounded wildcard type `?`

- `Set<?>`: 어떤 타입도 담을 수 있는 Set
- generic type을 쓰고 싶지만 실제 type parameter에 신경쓰고 싶지 않을 때 사용
- wildcard는 안전하지만, raw type은 안전하지 않다!
    - `Collection`에는 어떤 원소든 넣을 수 있다 (타입 불변식을 훼손하기 쉬움)
    - `Collection<?>`에는
        - null 외 어떤 원소도 넣을 수 없다 (컴파일러 오류)
        - 꺼내는 객체의 타입도 전혀 알 수 없다
        - 이게 불편하면 generic method나 bounded wildcard type 사용하라

안좋은 예시

```java
static int numElementsInCommon(Set s1, Set s2) {
	int result = 0;
	for (Object o1 : s1)
		if (s2.contains(o1))
			result++;
	return result;
}
```

좋은 예시

```java
static int numElementsInCommon(Set<?> s1, Set<?> s2) {
	int result = 0;
	for (Object o1 : s1)
		if (s2.contains(o1))
			result++;
	return result;
}
```

### raw type을 써야하는 예외 상황

1. class literal에는 raw type을 써야 한다
    1. class literal: `String.class` → `Class<String> c = String.class;`
    2. class literal에는 parameterized type을 쓸 수 없다 (`List<String>.class` 불가)
2. instanceof
    1. runtime에는 generic type 정보 지워진다
    2. instanceof는 unbounded wildcard type,  외 parameterized type에는 적용 불가
    3. <?>, raw type 둘다 똑같이 동작하므로 더 깔끔한 raw type 사용

```java
if (o instanceof Set) {   // raw type
	Set<?> s = (Set<?>) o;  // wildcard type (o의 타입 확인 후 <?>으로 형변환 해야함!)
}
```

### 요약

1. raw type 쓰지 말라 (예외 발생할 수 있어 안전하지 않다)
2. Set<Object>, Set<?>, Set의 차이점을 알고 사용

### 소감

프로젝트를 하면서 generic을 써본적이 아직 없어서, 자바 프로젝트를 더 해야지 배울게 많다는 것을 깨달았다..!

이제 프로그래밍 언어 수업을 들으면 진짜 재미있게 들을 수 있을 것 같단 말이지ㅎㅎ