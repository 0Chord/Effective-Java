# equals는 일반 규약을 지켜 재정의하라

#### 다음에서 열거한 상황 중 하나에 해당한다면 재정의하지 않는 것이 최선

- [x] 각 인스턴스가 본질적으로 고유함
    - [x] 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 여기 해당(Reference Object)
- [x] 인스턴스의 '논리적 동치성(logical equality)'을 검사할 일이 없음
    - [x] java.util.regex.Pattern은 equals를 재정의해서 두 Pattern의 인스턴스가 같은 정규표현식을 나타내는지를 검사하는, 즉 논리적 동치성을 검사하는 방법도 있음
    - [x] 설계를 후자로 판단했다면 Object의 기본 equals만으로 해결됨
- [x] 상의 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞음
    - [x] 대부분의 Set 구현체는 AbstractSet이 구현한 equals를 상속받아 쓰고, List 구현체는 AbstractList로부터, Map구현체들은 AbstractMap으로부터 상속받아 그대로 씀
- [x] 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없음
~~~java
@Override 
public boolean equals(Object o){
  throw new AssertionError();
}
~~~
#### equals를 재정의해야 할 때는 객체 식별성이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때
- [x] 주로 값 클래스들이 여기 해당 - Integer, String
- [x] 논리적 동치성을 확인하도록 재정의해두면, 그 인스턴스는 값을 비교하길 원하는 프로그래머의 기대에 부응함은 물론 Map의 키와 Set의 원소로 사용할 수 있게 됨
- [x] 값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만드러이지지 않음을 보장하는 인스턴스 통제 클래스(아이템1)라면 equals를 재정의하지 않아도 됨
### equals 메서드를 재정의할 때는 반드시 일반 규약을 따라야 함
- [x] 반사성: null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.
- [x] 대칭성: null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)가 true면 y.equals(x)도 true다
- [x] 추이성: hull이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y)가 true이고 y.equals(z)도 true면 x.equals(z)도 true다
- [x] 일관성: null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환해야 함
- [x] null-아님: null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다
#### Object 명세에서 말하는 동치관계란 집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연
- [x] 반사성은 객체는 자기 자신과 같아야 한다는 뜻
- [x] 대칭성은 두 객체는 서로에 대한 동치 여부를 똑같이 답해야 한다는 뜻
  - [x] toString 메서드는 원본 문자열의 대소문자를 그대로 돌려주지만 equals에서는 대소문자를 무시
~~~java
public final class CaseInsensitiveString {
  private final String s;

  public CaseInsensitiveString(String s) {
    this.s = Objects.requireNonNull(s);
  }
  @Override public boolean equals(Object o) {
      if (o instanceof CaseInsensitiveString)
          return s.equalsIgnoreCase(
                  ((CaseInsensitiveString) o).s);
      if (o instanceof String)
          return s.equalsIgnoreCase((String) o);
      return false;
  }
}
~~~
~~~java
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
~~~
- [x] cis.equals(s)는 true를 반환하지만, s.equals(cis)는 false를 반환하여, 대칭성을 위반
- [x] equals 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알 수 없음
~~~java
@Override public boolean equals(Object o) {
  return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
}
~~~
- [x] 추이성은 첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다는 뜻
~~~java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point)) {
            return false;
        }
        Point p = (Point) o;
        return p.x == x && p.y == y;
    }
}
~~~
~~~java
public class ColorPoint extends Point{
    private final Color color;
    
    public ColorPoint(int x, int y,Color color) {
        super(x, y);
        this.color = color;
    }
}
~~~
#### equals 메서드를 그대로 둔다면 상속되어 색상 정보는 무시한 채 비교를 수행
~~~java
@Override public boolean equals(Object o) {
  if (!(o instanceof ColorPoint))
    return false;
  return super.equals(o) && ((ColorPoint) o).color == color;
}
~~~
- [x] 일반 Point를 ColorPoint에 비교한 결과와 그 둘을 바꿔 비교한 결과가 다를 수 있음
#### 구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않음
~~~java
    @Override
    public boolean equals(Object o) {
        if (o == null || o.getClass() != getClass()) {
            return false;
        }
        Point p = (Point) o;
        return p.x == x && p.y == y;
    }
~~~
- [x] Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 
~~~java
    private static final Set<Point> unitCircle = Set.of(new Point(-1, 0), new Point(0, 1), 
        new Point(1, 0), new Point(0, -1));
    
    public static boolean onUnitCircle(Point p) {
        return unitCircle.contains(p);
    }
~~~
~~~java
public class CounterPoint extends Point{
    private static final AtomicInteger counter = new AtomicInteger();
    public CounterPoint(int x, int y) {
        super(x, y);
        counter.incrementAndGet();
    }
    public static int numberCreated() {
        return counter.get();
    }
}
~~~
#### 리스코프 치환 원칙에 따르면, 어떤 타입에 있는 중요한 속성이라면 그 하위 타입에서도 마찬가지로 중요함. 따라서 그 타입의 모든 메서드가 하위 타입에서도 똑같이 잘 작동해야 함
- [x] 일관성은 두 객체가 같다면 앞으로도 영원히 같아야 한다는 뜻
  - [x] 클래스를 작성할 때는 불변 클래스로 만드는 게 나을지 심사숙고
  - [x] 불변 클래스로 만들기로 했다면 equals가 한번 같다고 한 객체와는 영원히 같다고 답하고, 다르다고 한 객체와는 영원히 다르다고 답하도록 해야 함
  - [x] 클래스가 불변이든 가변이든 equals의 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안 됨
  - [x] equals는 항시 메모리에 존재하는 객체만을 사용한 결정적 계산만 수행해야 함
- [x] null-아님은 이름처럼 모든 객체가 null과 같지 않아야 한다는 뜻
#### equals 메서드 구현 방법 단계별 정리
- [x] == 연산자를 사용해 입력이 자기 자신의 참조인지 확인
  - [x] 자기 자신이면 true를 반환, 단순한 성능 최적화용으로 비교 작업이 복잡한 상황일 때 값어치 함
- [x] instanceof 연산자로 입력이 올바른 타입인지 확인
- [x] 입력을 올바른 타입으로 형변환
- [x] 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사함
  - [x] 모든 필드가 일치하면 true를, 하나라도 다르면 false를 반환
#### equals를 다 구현했다면 세 가지만 자문해보자. 대칭적인가? 추이성이 있는가? 일관적인가?
~~~java
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(short areaCode, short prefix, short lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix = rangeCheck(prefix, 999, "프리픽스");
        this.lineNum = rangeCheck(lineNum, 9999, "가입자 번호");
    }

    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max) {
            throw new IllegalArgumentException(arg + ": " + val);
        }
        return (short) val;
    }

    @Override
    public boolean equals(Object o) {
        if (o == this) {
            return true;
        }

        if (!(o instanceof PhoneNumber)) {
            return false;
        }

        PhoneNumber pn = (PhoneNumber) o;
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }
}
~~~
- [x] equals를 재정의할 땐 hashCode도 반드시 재정의하자
- [x] 너무 복잡하게 해결하려 들지 말자!
  - [x] 필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있음
- [x] Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자
#### 구글의 AutoValue 프레임워크로 애너테이션을 추가하면 AutoValue가 이 메서드들을 알아서 작성해준다