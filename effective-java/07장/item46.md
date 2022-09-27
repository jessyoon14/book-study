# 46. 스트림에서는 부작용 없는 함수를 사용하라

스트림은 함수형 프로그래밍에 패러다임이다!

- 핵심: 계산을 일련의 변환으로 재구성
- 각 단계는 순수 함수여야 한다
    - 순수 함수: input(이전 단계의 결과)에만 의존하며, 다른 가변 상태 참조 / 변경하지 않음
    - 이러려면 스트림 연산의 모든 함수는 부작용이 없어야 함

주의: 스트림 코드를 가장한 반복적 코드

안좋은 예시

```java
// forEach는 병렬화할 수 없으므로,
// 계산 결과를 보고할 때만 사용하고 계산할 때는 쓰지 말자

Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
    words.forEach(word -> {
        freq.merge(word.toLowerCase(), 1L, Long::sum);
    });
}
```

좋은 예시

```java
// collector: 스트림 원소들을 쉽게 컬렉션으로 모으게 해줌ㅊ념 (원소들을 컬렉션 객체 하나에 취합하는 블랙박스)
Map<String, Long> freq;
try (Stream<String> words = new Scanner(file).tokens()) {
    freq = words
            .collect(groupingBy(String::toLowerCase, counting()));
}

// 가장 자주 등장하는 10개 단어 추출
List<String> topTen = freq.keySet().stream()
	.sorted(comparing(freq::get).reversed())
	.limit(10)
	.collect(toList());
```

### Collector 메서드

주로 스트림 → 맵으로 취합하는 기능 제공

- toMap
    - 각 원소를 하나의 key value pair로 만들 수 있
        - `Stream.of(values()).collect(toMap(Object::toString, e->e));`
    - 같은 key를 가지는 여러개 value를 통합해서, 한 key value pair로 취합할 수 있다
        - `albums.collect(toMap(Album::artist, a→a, maxBy(comparing(Album::sales))));`
    - 맵 구현체를 따로 지정할 수 있다 (EnumMap, TreeMap 등)
    - 병렬 실행 후 ConcurrentHashMap로 만들 수도 있다
- groupingBy
    - 분류 함수를 받고, 이에 따라 원소를 키별로 묶어, 분류가 같은 원소들을 리스트로 매핑하는 맵을 만들 수 있다
    - downstream collector를 명시하면 리스트말고 다른 컬렉션으로 매핑할 수 있다 (ex. Set)
    - map factory도 지정할 수 있다 (map과 그 안의 collection의 타입까지 지정할 수 있다)
    - groupingByConcurrent도 사용할 수 있다
    - 사촌: partitionBy
- counting
    - 다운스트림 수집기 전용
    - 친구들: summing, averaging, summarizing …
- minBy, maxBy
- joining
    - CharSequence 인스턴스의 스트림에만 적용할 수 있다
    - 원소들을 concatenate할 수 있다
    - delimiter를 받아서 이걸 사이에 넣어서 concat할 수 있다 (ex. CSV)
    - prefix, suffix까지 지정할 수 있다