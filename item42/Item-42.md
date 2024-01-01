# 익명 클래스보다는 람다를 사용하라
- [x] 자바에서 함수 타입을 표현할 때 추상 메서드를 하나만 담은 인터페이스를 사용
- [x] 인터페이스의 인스턴스를 함수 객체라고 하여, 특정 함수나 동작을 나타내는 데 사용함
~~~java
Collections.sort(words, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return Integer.compare(s1.length(), s2.length());
    }
});
~~~
- [x] 지금은 함수형 인터페이스라 부르는 이 인터페이스들의 인스턴스를 람다식을 사용해 만들 수 있게 된 것
- [x] 람다는 함수나 익명 클래스와 개념은 비슷하지만 코드는 훨씬 간결함
~~~java
Collections.sort(words, (s1, s2) -> Integer.compare(s1.length(), s2.length()));
~~~
#### 타입을 명시해야 코드가 더 명확할 때만 제외하고는, 람다의 모든 매개변수 타입은 생략하자
~~~java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/"){
        public double apply(double x, double y) {
            return x / y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y);

    @Override
    public String toString() {
        return symbol;
    }
}
~~~
- [x] 람다를 이용하면 열거 타입의 인스턴스 필드를 이용하는 방식으로 상수별로 다르게 동작하는 코드를 쉽게 구현할 수 있음
- [x] 단순히 각 열거 타입 상수의 동작을 람다로 구현해 생성자에 넘기고 생성자는 이 람다를 인스턴스에 저장하고 필드에 저장된 람다를 호출하기만 하면 됨
~~~java
public enum Operation {
    PLUS("+", (x, y) -> x + y),
    MINUS("-", (x, y) -> x - y),
    TIMES("*", (x, y) -> x * y),
    DIVIDE("/", (x, y) -> x / y);

    private final String symbol;
    private final DoubleBinaryOperator op;

    Operation(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    public double apply(double x, double y) {
        return op.applyAsDouble(x, y);
    }

    @Override
    public String toString() {
        return symbol;
    }
}
~~~
#### 람다는 이름이 없고 문서화도 못함, 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 함
- [x] 람다는 한 줄일 때 가장 좋고 길어야 세 줄 안에 끝내는 게 좋음
- [x] 람다는 함수형 인터페이스에서만 쓰임
- [x] 추상 클래스의 인스턴스를 만들 때 람다를 쓸 수 없으니, 익명 클래스를 써야 함
#### 람다를 직렬화하는 일은 극히 삼가야 함
#### 익명 클래스는 함수형 인터페이스가 아닌 타입의 인스턴스를 만들 대만 사용하라