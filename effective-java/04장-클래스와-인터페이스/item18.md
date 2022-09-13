# 18. 상속보다는 컴포지션을 사용하라

<aside>
💡 상속은 캡슐화를 깨뜨린다

</aside>

> 다른 패키지의 구체 클래스를 상속하지 말라 (상위 클래스가 언제 변할지 모르니까!)
> 

문제: 메서드 호출과 달리, 상속은 캡슐화를 깨뜨린다 (⇒ 하위 클래스는 깨지기 쉽다)

1. 상위 클래스의 구현에 따라 하위 클래스의 동작이 이상해질 수 있음
2. 상위 클래스 version-up할 때 취약
    1. 상위 클래스 메서드의 구현이 예상치 못한 방향으로 바뀌면..
    2. 하위 클래스에만 있던 메소드가 새롭게 상위 클래스에도 정의되는데 서로 스펙이 다르면..

해결책: composition (새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스 참조)

1. 새로운 클래스의 메소드는 기존 클래스의 메소드들을 호출 (forwarding)
2. 효과: 기존 클래스의 내부 구현 / 새로운 메소드 추가에 영향을 받지 않음!

### Decorator pattern

composition을 사용해서 한 클래스에 특정 기능을 덧댄 새로운 클래스 생성

p.117 예시가 잘 이해 안됨

```java

// composition을 사용한 집합 클래스 -> wrapper class
public class InstrumentedSet<E> extends ForwardingSet<E> {
	private int addcount = 0;
	public InstrumentedSet(Set<E> s) {
		super(s);
	}
	@Override public boolean add(E e) {
		addCount++;
		return super.add(e);
	}
	@Override public boolean addAll(Collection<? extends E> c) {
		addCount += c.size();
		return super.addAll(c);
	}
}

// 재사용 가능한 전달 클래스 -> 상속
// 이게 있으면 원하는 기능을 덧씌운 클래스들을 쉽게 만들 수 있다
public class ForwardingSet<E> implements Set<E> {
	private final Set<E> s; // 여기서 composition
	public boolean add(E e) { return s.add(e); }
	public boolean addAll(Collection<? extends E> c) { return s.addAll(c); }
}
```

음.. 논리는 이해가 된다. 이제 상위 클래스의 메소드를 호출할 때 override된 메소드가 호출될 위험이 없으니 분리가 더 잘 되고, 상위 클래스의 메소드가 바뀌어도 이 하위 클래스의 동작은 영향을 받지 않는다. 

즉 상속의 이점을 남기고, 벽을 하나 쳐서 분리했다.

근데 (내가 처음봐서 그런지) 가독성은 훨씬 떨어지는 것 같다. 지금은 이렇게 하는 게 왜 안전한지 알겠는데, 아직은 이질적이다..

### wrapper class의 단점

- 단점이 거의 없다
- wrapper class가 callback framework랑 어울리지 않는다
    - callback: 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출 때 사용하도록 함
    - wrapper class의 내부 객체: wrapper의 존재를 몰라서 자기 자신의 참조를 넘기게 되면, 콜백 함수에서는 래퍼가 아니라 내부 객체 호출하게 됨

<aside>
💡 상속은 반드시 하위 클래스가 상위 클래스의 진짜 하위 타입일때만(is-a 관계) 사용하라. is-a 관계여도 다른 패키지의 클래스 / 확장을 고려하지 않고 설계된 클래스는 상속했을 때 문제가 많이 생길 수 있다.

</aside>

이건 너무 당연한 소리지만, 지금까지는 몰랐다.

지금까지 짠 코드를 살펴보면, 원래는 Animal > Dog > Poodle 이런 상속을 해야하는데, 기능이 미묘하게 추가된 Poodle > Corgie > Retriever 이런 식의 상속을 의식하지 못한 채 썼던 것 같다. 특히 존재하는 패키지 코드가 내 필요랑 다를 때!

지금까지는 하위호환성이 중요한, 유지보수가 계속 필요한 장기 프로젝트 경험이 별로 없어서 그랬던 것 같다.

이제는 조심해야겠다~

그리고 이 책을 읽으면서, 내가 지금까지 하던 웹 개발과 라이브러리 개발이 얼마나 다른지 보여서 신기하다.

재사용가능하고 하휘호환되는 코드.. 메모..!