# 59. 라이브러리를 익히고 사용해라

### 표준 라이브러리의 장점

1. 전문가의 지식에 기댈 수 있다
    - 예시: Random 라이브러리
        
        **Random 라이브러리의 단점**
        
        `rand.nextInt() % n`
        
        - n이 크지 않은 2의 제곱 수라면 같은 수열이 반복
        - n이 2의 제곱수가 아니라면 몇몇 숫자가 더 자주 반복되거나 값이 한쪽으로 쏠림 (특히 n이 클때)
        - 원하는 범위 밖의 숫자가 나올 수 있다 (INTMIN, %n은 음수 반환될 수 있음)
        
        **해결방법 1: Random.nextInt(int)**
        
        - 전문가들이 이미 위의 문제점들을 해결했다
        - 표준 라이브러리를 사용하라!
        
        **해결방법 2: 자바 7부터는 Random말고 ThreadLocalRandom을 사용하라**
        
        ThreadLocalRandom: randomness 👍 + 품질 👍
        
        Fork-join pool, 병렬 스트림은 SplittableRandom 사용
        
2. 핵심 일과 관련없는 문제에 시간을 낭비하지 않는다
3. 노력하지 않아도 성능이 지속적으로 개선된다
    1. 라이브러리 제작자들이 성능을 꾸준히 올린다!
4. 기능이 점점 많아진다
5. 내가 쓴 코드가 많은 사람에게 낯익은 코드가 된다 (가독성, 유지보수, 재활용 👍)

### 그러면 왜 많은 프로그래머들이 표준 라이브러리에 있는 기능을 직접 구현해서 쓸까?

라이브러리에 그런 기능이 있다는 걸 모른다

라이브러리를 잘 숙지하기 위해서:

- 메이저 릴리스마다 수많은 기능이 라이브러리에 추가되니, 새 기능 설명 페이지를 읽어라 (Java8-feat, Java9-feat)
- 자바개발자라면 적어도 java.lang, java.util, java.io와 그 하위 패키지에는 꼭 익숙해져라
- 그외 알아야할 라이브러리
    - Collections 프레임워크
    - stream 라이브러리
    - java.util.concurrent: 멀티스레드 작업을 위한 편의 기능
    
- 다른 라이브러리는 너무 방대하고 빠르게 발전하니 필요할 때마다 익혀라

### 원하는 기능이 없으면?

- 우선 표준 라이브러리로 시도하고, 기능 알아보기
- 필요한 것이 없으면 고품질 서드파티 라이브러리 보기 (ex. 구글의 구아바)
- 서드파티도 없으면 직접 구현..

### 소감

지금까지 라이브러리 자체를 익히기보다는, 필요한 기능이 있으면 그때그때 stackoverflow 참고하면서 라이브러리 함수들을 사용했는데, 이제는 표준 라이브러리 docs 자체를 훑어봐야겠다.

그리고 어차피 실력을 기른다는 측면에서 라이브러리를 쓰는 것보다 직접 구현하는 것이 이점이 있지 않나 싶었는데, 실제 프로덕션 코드는 무조건! 똑똑한 사람들이 개발/유지보수하는 표준 라이브러리를 사용해야겠다.