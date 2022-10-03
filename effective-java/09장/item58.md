# 58. 전통적인 for 문보다는 for-each 문을 사용하라

각 작업별로 스트림, 반복 중 더 적합한 것이 있다

### 일반 for문의 단점

- 원소만 필요한 경우에서는, iterator나 index 변수가 반복되며 코드가 지저분함
- 변수가 많아지니, 변수를 잘못 사용할 가능성 ⬆️
- collection (iterator) vs array (index)에 따라 코드가 많이 달라짐

### for-each문의 장점

위 모든 문제는 for-each로 해결된다! (enhanced for loop)

```java
for (Element e : elements) {...}
```

- 변수가 적어 오류 가능성 적음
- 코드 깔끔
- 컬렉션, 배열 모두 같은 코드로 처리
- 속도 최적화 (컬렉션, 배열 모두 속도가 같고 최적화됨)
- nested for loop에서 가독성 ⬆️

### for-each를 사용할 수 없는 상황

이 세 가지 경우에는 일만 for 문 사용

- destructive filtering
    - 컬렉션을 돌면서 현재 원소를 제거해야할 때
    - iterator.remove(), Collection.removeIf() 사용
- transforming
    - 리스트, 배열을 돌면서 원소의 값 일부 / 전체를 교체할 때는 iterator / index 사용
- parallel iteration
    - 여러 컬렉션을 병렬로 수뇌할 때는 각각 iterator / index 사용해서 명시적으로 제어

### 기타

for-each는 컬렉션, 배열 외에도 Iterable 인터페이스의 모든 구현체에 사용할 수 있다

(원소 묶음을 표현해야한다면, iterable 구현을 고민하자!)

### 소감

for-each가 for문보다 최적화 되어있고, 읽기 편하므로 가급적이면 for-each가 낫다는 것은 굉장히 유용한 지식이다.

앞으로 개발할 때 가능하면 for each를 써야겠다.

컬렉션 외에도 원소 묶음이 있을 때 Iterable을 구현하는 것도 신기한데, 지금까지는 원소 묶음을 표현할 때 컬렉션말고 다른 걸 써야할 일이 없어서 언제 써야하는지 잘 모르겠다.