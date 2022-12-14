# 51. 메서드 시그니처를 신중히 설계하라

1. 메서드 이름은 표준 명명 규칙을 따르라
    1. 패키지 내 일관성 유지
    2. 개발자 커뮤니티에서 받아들여지는 이름
    3. 길지 않도록
    4. 자바 라이브러리의 API 가이드 참고

1. 편의 메서드를 너무 많이 만들지 말자
    1. 메서드가 너무 많으면 사용, 테스트, 유지보수가 어렵다
    2. 아주 많이 쓸때만 제공하고, 확신이 없으면 만들지 말라
2. 매개변수 목록은 4개 이하로 짧게 유지하라
    1. 같은 타입의 매개변수 여러개가 연달아 나오는 경우를 피하자(실수 유발)
    2. 매개변수 목록을 줄이는 방법
        1. 여러 메서드로 쪼개기
            1. orthogonality를 높이면 오히려 메서드 수가 줄어들 수도 있다 (10개 기능을 제공하는 메서드 10개 대신, 잘 쪼개서 조합가능한 메서드 3개로!)
            2. orthogonality: 공통점이 없는 기능들이 잘 분리되어 있다 → 구현 및 테스트가 쉬움
        2. 매개변수 여러개를 묶어주는 helper class 사용
            1. 주로 static member class
            2. 여러 매개변수를 한개의 개념으로 볼때 추천
        3. builder pattern (객체 생성)을 메서드 호출에 응용
            1. 모든 매개변수를 추상화한 객체 정의 후, 필요한 값들만 setter로 설정. 설정 완료 후 execute()로 유효성 검증
            2. 
3. 매개변수 타입은 클래스보다 인터페이스가 더 낫다
    1. 매개변수로 적합한 인터페이스가 있다면, 구현체 클래스보다 인터페이스를 사용하자
    2. 그러면 클라이언트 측의 구현체를 제한하지 않아서, 입력 데이터가 다른 형태일 때 특정 구현체로 옮기는 복사비용을 줄일 수 있다
4. Boolean보다 원소 2개짜리 enum이 낫다
    1. 예외: 메서드 이름 상 boolean이 더 자연스러울 때
    2. enum을 쓰면 가독성이 올라가고, 추후 다른 선택지로 확장하기 쉽고, enum method를 정의할 수도 있다