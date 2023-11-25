# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
### 정적 유틸리티를 잘못 사용한 예 - 유연하지 않고 테스트하기 어려움
~~~java
public class SpellChecker{
    private static final Lexicon dictionary = ...;

    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) {...}
    public static List<String> suggestions(String typo){...}
}
~~~
### 싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트하기 어려움
~~~java
public class SpellChecker{
    private final Lexicon dictionary = ...;

    private SpellChecker(...) {}
    public static SpellChecker INSTANCE = new SpellChecker(...);
    
    public boolean isValid(String word) {...}
    public List<String> suggestions(String typo) {...}
}
~~~
- [x] dictionary 필드에서 final 한정자를 제거하고 다른 사전으로 교체하는 메서드를 추가할 수 있지만, 이 방식은 어색하고 오류를 내기 쉬우며 멀티스레드 환경에서는 쓸 수 없음
- [x] 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않음
- [x] 클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야 함
  - [x] 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식
~~~java
public class SpellChecker{
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary){
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) {...}
    public List<String> suggestions(String typo) {...}
}
~~~
#### 불변을 보장하여 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있음
#### 이 패턴의 변형으로, 생성자에 자원 팩터리를 넘겨주는 방식이 있다
- [x] 팩터리란 호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체를 뜻함
  - [x] Supplier<T> 인터페이스가 팩터리를 표현한 완벽한 예
- [x] 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다
~~~java
Mosaic create(Supplier<? extends Tile> tileFactory) {...}
~~~
### 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다
#### 이 자원들을 클래스가 직접 만들게 해서도 안된다
- [x] 필요한 자원들을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘긴다
- [x] 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다