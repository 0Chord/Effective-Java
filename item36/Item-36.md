# 비트 필드 대신 EnumSet을 사용하라

- [x] 열거한 값들이 주로 집합으로 사용될 경우, 예쩐에는 각 상수에 서로 다른 2의 거듭제곱 값을 할당한 정수 열거 패턴을 사용함

~~~java
public class Text {
    public static final int STYLE_BOLD = 1 << 0; //1
    public static final int STYLE_ITALIC = 1 << 1;  // 2
    public static final int STYLE_UNDERLINE = 1 << 2;  // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8

    public void applyStyles(int styles) { ...}
}
~~~

- [x] 비트별 OR을 사용해 여러 상수를 하나의 집합으로 모을 수 있으며, 이렇게 만들어진 집합을 비트 필드라고 함

~~~java
text.applyStyles(STYLE_BOLD|STYLE_ITALIC);
~~~
- [x] 비트 필드를 사용하면 비트별 연산을 사용해 합집합과 교집합 같은 집합 연산을 효율적으로 수행할 수 있음
- [x] 비트 필드는 정수 열거 상수의 단점을 그대로 지니며, 추가로 다음과 같은 문제까지 안고 있음
- [x] EnumSet 클래스는 열거 타입 상수의 값으로 구성된 집합을 효과적으로 표현해줌
- [x] Set 인터페이스를 완벽히 구현하며 타입 안전하고, 다른 어떤 Set 구현체와도 함께 사용할 수 있음
- [x] EnumSet의 내부는 비트 벡터로 구현됨
  - [x] 원소가 총 64개 이하라면, 즉 대부분의 경우에 EnumSet 전체를 long 변수 하나로 표현하여 비트 필드에 비견되는 성능을 보여줌
~~~java
public class Text { 
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH } 
    
    public void applyStyles(Set<Style> styles) { ... }
}
~~~
- [x] EnumSet은 집합 생성 등 다양한 기능의 정적 팩터리를 제공하는데, 다음 코드에서는 그중 of aptjemfmf tkdydgka
~~~text
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
~~~
#### 모든 클라이언트가 EnumSet을 건네리라 짐작되는 상황이라도 이왕이면 인터페이스로 받는 게 일반적으로 좋은 습관

#### 열거할 수 있는 타입을 한데 모아 집합 형태로 사용한다고 해도 비트 필드를 사용할 이유는 없음
#### EnumSet 클래스가 비트 필드 수준의 명료함과 성능을 제공하고 열거 타입의 장점까지 선사하기 때문