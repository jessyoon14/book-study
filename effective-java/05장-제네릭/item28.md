# 28. 배열보다는 리스트를 사용하라

### 배열과 제네릭 타입의 차이

1. covariance
    - 정의: type A, B의 관계가 List<A>, List<B> 등 복잡한 타입 사이의 관계에도 적용이 되는가?
        - [https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science)](https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science))
    - 배열은 covariant
        - Sub가 Super의 하위 타입 ⇒ Sub[]는 Super[]의 하위 타입
        - 이로 인해 배열은 type safe 하지 않다
            
            ```java
            // runtime error
            Object[] objectArray = new Long[1];
            objectArray[0] = "타입이 달라 넣을 수 없다"; // ArrayStoreException
            ```
            
    - 제네릭은 invariant
        - List<Type1>, List<Type2>는 서로 하위/상위 타입이 아님
        - type safe하다 (모든 에러를 컴파일 중 잡을 수 있다)
            
            ```java
            // compiler error
            List<Object> ol = new ArrayList<Long>(); // 서로 호환되지 않는 타입
            ```
            
2. reify
    1. 배열은 reify (실체화)
        1. 배열은 런타임에도 자신의 원소 타입을 인지하고 확인 (exception 발생 가능)
    2. 제네릭
        1. 타입정보가 런타임에는 지워져서 컴파일 때만 확인하며, 런타임에는 타입을 알 수 없다

→ 이로 인해 배열 & 제네릭은 궁합이 안좋다

배열은 제네릭 타입, parameterized type, type parameter로 사용할 수 없다

(new List<E>[] 등은 컴파일 시 제네릭 배열 생성 오류)

### 제네릭 배열

- type unsafe하기 때문에 허용되지 않는다
- 허용된다면, classCastException이 일어날 수 있기 때문이다

```java
List<String>[] stringLists = new List<String>[1]; // 컴파일러 에러 (generic array)
List<Integer> intList = List.of(42);
Object[] objects = stringLists;
Objects[0] = intList;
String s = stringLists[0].get(0); // 컴파일러 에러가 없었다면, 여기서 runtime ClassCastException
```

### non-reifiable type

- 실체화되지 않아서 (erasure 메커니즘으로 인해) 런타임에는 컴파일 타입보다 타입 정보를 적게 가지는 타입
- 예시: E, List<E>, List<String>
- 제네릭 배열을 허용할 수 없는 이유 중 하나

### 배열로 형변환할 때 제네릭 오류가 뜬다면, 대신 List<E>를 사용하라

안좋은 예시 (배열 사용)

```java
public class Chooser {
	private final Object[] choiceArray;
	
	public Chooser(Collection choices) {
		choiceArray = choices.toArray();
	}

	// 호출할 때마다 형변환 필요 + 런타임 형변환 오류 발생 가능
	public Object choose() {
		Random rnd = ThreadLocalRandom.current();
		return choiceArray[rnd.nextInt(choiceArray.length)];
	}
}
```

좋은 예시 (리스트 사용)

```java
public class Chooser {
	private final List<T> choiceList;
	
	public Chooser(Collection<T> choices) {
		choiceList = new ArrayList<>(choices);
	}

	// 형변환 필요 없음 + 런타임 에러 없음
	public T choose() {
		Random rnd = ThreadLocalRandom.current();
		return choiceList.get(rnd.nextInt(choiceListsize()));
	}
}
```

### 요약

1. 배열 & 제네릭은 타입 규칙이 매우 다르므로, 둘을 섞어쓰기는 쉽지 않다
    1. 배열: 공변, 실체화 (런타임에는 type safe, 컴파일타임에는 type unsafe)
    2. 제네릭: 불공변, 타입 정보 소거 (런타임에는 type unsafe, 컴파일타임에는 type safe)
2. 섞어쓰다 오류나면 배열을 리스트로 대체해라

### 소감

너무 어렵다

reification도 모르던 개념이다. 공부할게 많다!