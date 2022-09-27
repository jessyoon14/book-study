# 39. 명명 패턴보다 애너테이션을 사용하라

### 명명패턴이란?

메서드 이름에 prefix 같은 규칙을 통해 도구 / 프레임워크가 특별 취급해야하는 요소들을 구분

문제

- 오타가 나면 프레임워크가 메소드를 인식하지 못함
- 휴먼 에러 (메소드에만 적용되는 규칙을 클래스 네이밍에 적용하여 잘못된 동작을 기대)
- 프로그램 요소를 매개변수로 전달할 방법이 없음

→ 이걸 해결하기 위해 annotation 도입!

### annotation 예시 1

```java
// meta-annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)// 이 애노테이션은 메소드에만 사용
public @interface Test {
}

// @Test 애노테이션은 Sample class의 의미에 영향을 주지는 않지만,
// 이 애노테이션에 관심있는 프로그램에게 추가 정보를 제공
// (ex. TestRunner는 @Test 붙은 메소드만 실행)
public class Sample {
	@Test public static void m1() {}
}
```

### annotation 예시 2

```java

// 명시한 예외를 던져야만 성공하는 테스트 메서드용 애너테이션
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)// 이 애노테이션은 메소드에만 사용
public @interface ExceptionTest {
	// 애노테이션의 매개변수 타입은 Throwable을 확장한 클래스의 Class 객체
	Class<? extends Throwable> value(); 
}

public class Sample2 {
	@ExceptionTest(ArithmeticException.class)
	public static void m1() {
		int i = 0;
		i = i / i;
	}
}

// 그러면 TestRunner에서, 이 메서드에서 반환되는 exception이 매개변수로 들어온 값과 같음을 확인
```

### annotation 예시 3

매개변수를 여러개 받기 위해서 1) 배열, 2) @Repeatable meta-annotation을 사용할 수 있다

1. 배열

```java
// 명시한 예외 중 한 개를 던져야만 성공하는 테스트 메서드용 애너테이션
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
	// 애노테이션의 매개변수 타입은 Throwable을 확장한 클래스의 Class 객체의 배열
	Class<? extends Throwable>[] value(); 
}

public class Sample2 {
	@ExceptionTest({ArithmeticException.class, NullPointerException.class})
	public static void m1() {
		int i = 0;
		i = i / i;
	}
}

```

1. 반복 가능 애너테이션 (무조건 container annotation을 따로 정의해야함)
    1. container annotation: annotation을 한 개 vs 반복 달았을 경우를 구분해줌
    2. 주의: container annotation으로 인해 getAnnotationsByType, isAnnotationPresent 등 메서드를 사용할 때 주의 필요

```java
// 명시한 예외 중 한 개를 던져야만 성공하는 테스트 메서드용 애너테이션
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Repeatable(ExceptionTestContatiner.class)
public @interface ExceptionTest {
	Class<? extends Throwable> value(); 
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTestContainer {
	ExceptionTest[] value();
}

public class Sample2 {
	@ExceptionTest(ArithmeticException.class)
	@ExceptionTest(NullPointerException.class})
	public static void m1() {
		int i = 0;
		i = i / i;
	}
}

```

### 요약

- 애너테이션이 명명 패턴보다 훨씬 낫다
- 왠만해서 도구를 만들지 않는 한 일반 프로그래머가 애너테이션을 직접 정의할 일은 없다ㅎㅎ