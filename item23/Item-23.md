# 태그 달린 클래스보다는 클래스 계층구조를 활용하라
~~~java
public class Figure {
    enum Shape {RECTANGLE, CIRCLE};

    final Shape shape;
    
    double length;
    double width;
    
    double radius;

    public Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    public Figure( double length, double width) {
        this.shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
~~~
- [x] 태그 달린 클래스에는 단점이 한가득
  - [x] 열거 타입 선언, 태그 필드, switch 문 등 쓸데없는 코드가 많음
  - [x] 필드들을 final로 선언하려면 해당 의미에 쓰이지 않는 필드들까지 생성자에서 초기화해야 함
#### 태그가 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적
- [x] 자바와 같은 객체 지향 언어는 타입 하나로 다양한 의미의 객체를 표현하는 훨씬 나은 수단을 제공
#### 태그 달린 클래스는 클래스 계층구조를 어설프게 흉내낸 아류일 뿐
- [x] 계층 구조의 루트가 될 추상 클래스를 정의하고, 태그 값에 따라 동작이 달라지는 메서드들을 루트 클래스의 추상 메서드로 선언
  - [x] figure class에서는 area가 이러한 메서드에 해당됨
- [x] 태그 값에 상관없이 동작이 일정한 메서드들을 루트 클래스에 일반 메서드로 추가
- [x] 모든 하위 클래스에서 공통으로 사용하는 데이터 필드들도 전부 루트 클래스로 올림
~~~java
abstract class Figure {
    abstract double area();
}
~~~
~~~java
class Circle extends Figure{
    final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * (radius * radius);
    }
}
~~~
~~~java
public class Rectangle extends Figure{
    
    final double length;
    final double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    double area() {
        return length*width;
    }
}
~~~
- [x] Figure class를 클래스 계층구조 방식으로 구현한 코드
- [x] 루트 클래스의 코드를 건드리지 않고도 다른 프로그래머들이 독립적으로 계층구조를 확장하고 함께 사용할 수 있음
- [x] 타입이 의미별로 따로 존재하니 변수의 의미를 명시하거나 제한할 수 있고, 또 특정 의미만 매개변수로 받을 수 있음
#### 태그 달린 클래스를 써야 하는 상황은 거의 없음
#### 새로운 클래스를 작성하는 데 태그 필드가 등장한다면 태그를 없애고 계층구조로 대체하는 방법을 생각해보자
#### 기존 클래스가 태그 필드를 사용하고 있다면 계층구조로 리팩터링하는 걸 고민해보자