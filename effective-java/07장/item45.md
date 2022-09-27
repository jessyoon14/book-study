# 45. 스트림은 주의해서 사용하라

### 스트림이란?

목적: (순차적 or 병렬적 둘다) 다량의 데이터 처리를 돕기 위함 

### 핵심 추상 개념

1. 스트림: 데이터 원소의 유한 혹은 무한 시퀀스
    1. 원소는 객체 참조나 기본 타입 값이며 (int, double, long), 어디서든 올 수 있다 (컬렉션, 배열, 파일, regex pattern matcher, etc.)
2. 스트림 파이프라인: 이 원소들로 수행하는 연산 단계
    1. 소스 스트림 → n개의 intermediate operation → terminal operation
        1. intermediate operation: 각 스트림을 다른 스트림으로 변환 (타입 변환 가능)
            1. ex) 각 원소에 함수 적용, 특정 조건으로 원소 filtering
        2. terminal operation: 최후의 연산 
            1. ex) 정렬해서 컬렉션 생성, 특정 원소 선택, 원소들 출력
    2. lazy evaluation
        1. terminal operation 때 evaluation됨 
        2. terminal op. 에서 쓰이지 않는 원소는 계산 되지 않음 → 그래서 무한 스트림을 다룰 수 있음
        3. terminal op이 없으면 아무것도 실행되지 않음!

### 특징

1. stream API는 fluent API다
    1. 메서드 연쇄 지원
2. 기본적으로는 순차적으로 수행 (병렬로 설정 가능하나, 효과를 보는 일은 드물다)

### 주의: 언제 스트림을 사용할지 잘 판단해야 한다

스트림의 장점

- 다양한 연산이 가능하다
- 제대로 사용하면 프로그램이 짧고 깔끔해진다

스트림의 단점

- 잘못 사용하면 읽기 어렵고 유지보수 힘들다

### 예시

```java
public static void main(String[] args) throws IOException {
    Path dictionary = Paths.get(args[0]);
    int minGroupSize = Integer.parseInt(args[1]);

    try (Stream<String> words = Files.lines(dictionary)) {
        words.collect(groupingBy(word -> alphabetize(word)))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .forEach(g -> System.out.println(g.size() + ": " + g));
    }
}
```

### 주의사항

- char를 지원하지 않으므로, char 값을 처리할 떄는 스트림을 사용하지 말자
- 모든 반복문을 스트림으로 대체하면 가독성/유지 보수성 떨어질 수 있으니, 반복문과 스트림을 함께 조합하는 게 좋다

### 스트림보다 반복이 적합할 때

스트림은 함수 객체를 사용하지만 반복은 코드블록을 사용하므로, 아래를 지원함

1. 범위 안의 지역 변수 읽고 수정 (lambda는 final인 변수만 읽을 수 있고, 지역 변수 수정 불가)
2. return, break, continue, exception 등으로 흐름 조절 가능 (lambda는 다 못함)

### 스트림을 쓰기 좋을 때

- 원소 시퀀스를 일관되게 변환
- 원소 시퀀스를 필터링
- 원소 시퀀스를 결합 (sum, concat, min, max etc.)
- 원소 시퀀스를 컬렉션에 모으기 (GROUP BY처럼 속성별로 묶기)
- 원소 시퀀스에서 특정 조건 만족하는 원소 찾기

### 스트림을 쓰기 안좋을 때

- 한 데이터가 파이프라인의 여러 단계를 거치는데, 각 단계에서의 값들을 동시에 접근해야할 때
    - 스트림 파이프라인은 한 값을 처리하면 원래 값을 잃음
    - 굳이 필요하다면 연산을 거꾸로 수행해서 계산할 수는 있음