# 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

- [x] 열거 타입은 거의 모든 상황에서 이 책 초판에서 소개한 타입 안전 열거 타입보다 우수함
- [x] 타입 안전 열거 패턴은 확장할 수 있으나 열거 타입은 그럴 수 없다는 점
- [x] 타입 안전 열거 패턴은 열거한 값들을 그대로 가져온 다음 값을 더 추가하여 다른 목적으로 쓸 수 있는 반면, 열거 타입은 그렇게 할 수 없다는 뜻

~~~java
public interface Operation {
    double apply(double x, double y);
}

public enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    }

    MINUS("-") {
        public double apply ( double x, double y){
            return x - y;
        }
    }

    TIMES("*") {
        public double apply ( double x, double y){
            return x * y;
        }
    }

    DIVIDE("/") {
        public double apply ( double x, double y){
            return x / y;
        }
    }

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
~~~
- [x] 열거 타입인 BasicOperation은 확장할 수 없지만 인터페이스인 Operation은 확장할 수 있고, 이 인터페이스를 연산의 타입으로 사용하면 됨
~~~java
public enum BasicOperation implements Operation {
    PLUS("^") {
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    }

    MINUS("%") {
        public double apply (double x, double y){
            return x % y;
        }
    }


    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
~~~
- [x] Operation 인터페이스를 사용하도록 작성되어 있기만 하면 됨
#### 열거 타입 자체는 확장할 수 없지만, 인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있음