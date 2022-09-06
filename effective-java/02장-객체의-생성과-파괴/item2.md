# 2. 생성자에 매개변수가 많다면 빌더를 고려하라

### 문제

자바는 default parameter를 지정할 수가 없다

그래서 정적 팩터리, 생성자 둘다 선택적 매개변수가 많을 때는 지저분함

### 해결책 1: telescoping constructor pattern

- 변수 1개, 2개, … n개 생성자를 일일히 만듦
- ex. `new NutritionFacts(250, 8, 100, 0, 35, 27)`
- 사용할 수는 있으나, 변수 개수가 많아지면 코드 작성 어렵고 가독성 떨어짐

### 해결책 2: JavaBeans pattern

- 예시
    
    ```java
    public class NutritionFacts {
        // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.
        private int servingSize  = -1; // 필수; 기본값 없음
        private int servings     = -1; // 필수; 기본값 없음
        private int calories     = 0;
        private int fat          = 0;
        private int sodium       = 0;
        private int carbohydrate = 0;
    
        public NutritionFacts() { }
        // Setters
        public void setServingSize(int val)  { servingSize = val; }
        public void setServings(int val)     { servings = val; }
        public void setCalories(int val)     { calories = val; }
        public void setFat(int val)          { fat = val; }
        public void setSodium(int val)       { sodium = val; }
        public void setCarbohydrate(int val) { carbohydrate = val; }
    
        public static void main(String[] args) {
            NutritionFacts cocaCola = new NutritionFacts();
            cocaCola.setServingSize(240);
            cocaCola.setServings(8);
            cocaCola.setCalories(100);
            cocaCola.setSodium(35);
            cocaCola.setCarbohydrate(27);
        }
    }
    ```
    
- 매개변수가 없는 생성자로 객체를 만든 후, setter 메서드를 호출해 값 설정
- 코드가 길어지긴 했지만 인스턴스를 만들기 쉽고, 가독성이 높다
- 단점
    - 객체 하나를 만들기 위해 메서드 여러개 호출
    - 객체 완성 전까지 consistency가 무너진 상태 (이전과 달리 생성자에서 유효한 값인지 확인할 수가 없음) → 디버깅 어려움
        - 이 문제 때문에 자바 빈즈 패턴에서는 클래스를 불변으로 만들 수 없고, thread safety 위해 개발자가 추가 작업해야함
    

### 해결책 3: Builder pattern

- 점층적 생성자 패턴의 안전성 + 자바빈즈 패턴의 가독성
- 사용 순서
    - 필수 매개 변수만으로 생성자를 호출해서 빌더 객체 얻기
    - 빌더에서 제공하는 setter 메서드로 원하는 선택 argument 설정
    - build()를 호출해서 객체 생성
- Builder는 주로 생성할 클래스의 static member class
- method chaining
- Python, Scala에 있는 named optional parameters를 흉내낸 것
- 유효성 검사는 build
- 계층적으로 설계된 클래스와 궁합이 좋음

### 한줄 요약

생성자나 정적 팩터리가 처리해야하는 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다

(간결 + 안전)

### 소감

AWS 개발 책에서 봤던 방식이라 반갑다! 

근데 코틀린 코드는 이렇게 하는 코드를 본 적이 없다.

코틀린은 파이썬처럼 default parameter를 지정할 수 있으니까 이 걱정이 없는 것 같다