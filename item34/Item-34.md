# int 상수 대신 열거 타입을 사용하라

- [x] 열거 타입은 일정 개수의 상수 값을 정의한 다음, 그 외의 값은 허용하지 않는 타입

~~~java
public static final int APPLE_FUJI=0;
public static final int APPLE_PIPPIN=1;
public static final int APPLE_GRANNY_SMITH=2;
public static final int ORANGE_NAVEL=0;
public static final int ORANGE_TEMPLE=1;
public static final int ORANGE_BLOOD=2;
~~~
- [x] 정수 열거 패턴 기법에는 단점이 많음
- [x] 타입 안전을 보장할 방법이 없으며 표현력도 좋지 않음
~~~java
int i = (APPLE_FUJI - ORANGE_TEMPLE) / APPLE_PIPPIN;
~~~
- [x] 오렌지를 건네야 할 메서드에 사과를 보내고 동등 연산자로 비교하더라도 컴파일러는 아무런 경고 메시지를 출력하지 않는다
- [x] 정수 열거 패턴을 사용한 프로그램은 깨지기 쉬움
- [x] 평범한 상수를 나열한 것뿐이라 컴파일하면 그 값이 클라이언트 파일에 그대로 새겨진다
- [x] 자바는 열거 패턴의 단점을 말끔히 씻어주는 동시에 여러 장점을 안겨주는 대안을 제시함
~~~java
public enum Apple {FUJI, PIPPIN, GRANNY_SMITH}
public enum Orange {NAVEL, TEMPLE, BLOOD}
~~~
- [x] 자바의 열거 타입은 오나전한 형태의 클래스라서 다른 언어의 열거 타입보다 훨씬 강력함
- [x] 열거 타입 자체는 클래스이며, 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개함
- [x] 열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final
#### 클라이언트가 인스턴스를 직접 생성하거나 확장할 수 없으니 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장됨
- [x] 열거 타입은 싱글턴을 일반화한 형태라고 볼 수 있음
- [x] 열거 타입에는 어떤 메서드도 추가할 수 있음

~~~java
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.879e+24, 6.052e6),
    EARTH(5.975e+24, 6.378e6),
    MARS(6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN(5.685e+26, 6.027e7),
    URANUS(8.683e+25, 2.556e7),
    NEPTUNE(1.024e+26, 2.477e7);

    private static final double G = 6.67300E-11;
    private final double mass;
    private final double radius;
    private final double surfaceGravity;

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        this.surfaceGravity = G * mass / (radius * radius);
    }

    public double mass() {
        return mass;
    }

    public double radius() {
        return radius;
    }

    public double surfaceGravity() {
        return surfaceGravity;
    }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity; //F = ma
    }
}
~~~
#### 열거 타입 상수 각각을 특정 데이터와 연결지으려면 생성자에서 데이터를 받아 인스턴스에 저장하면 됨
- [x] 열거 타입은 근본적으로 불변이라 모든 필드는 final이어야 함
- [x] 필드는 public으로 선언해도 되지만, private으로 두고 별도의 public 접근자 메서드를 두는게 나음
~~~java
public class WeightTable {
    public static void main(String[] args) {
        double earthWeight = Double.parseDouble(args[0]);
        double mass = earthWeight / Planet.EARTH.surfaceGravity();
        for (Planet p : Planet.values()) {
            System.out.printf("%s에서의 무게는 %f이다.$n",p,p.surfaceWeight(mass));
        }
    }
}
~~~
- [x] 열거 타입을 선언한 클래스 혹은 패키지에서만 유용한 기능은 private이나 package-private 메서드를 구현함
- [x] 클라이언트에 노출해야 할 합당한 이유가 없다면 private으로, 혹은 package-private으로 선언함
~~~java
public enum Operation {
    PLUS, MINUS, TIMES, DIVIDE;

    public double apply(double x, double y) {
        switch (this) {
            case PLUS:
                return x + y;
            case MINUS:
                return x - y;
            case TIMES:
                return x * y;
            case DIVIDE:
                return x / y;
        }
        throw new AssertionError("알 수 없는 연산: " + this);
    }
}
~~~
- [x] 동작을 하지만 그리 예쁘지는 않음
#### 열거 타입에 apply라는 추상 메서드를 선언하고 각 상수별 클래스 몸체, 즉 각 상수에서 자신에 맞게 재정의하는 방법 - 상수별 메서드 구현
~~~java
public enum Operation {
    PLUS {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE{
        public double apply(double x, double y) {
            return x / y;
        }
    };

    public abstract double apply(double x, double y);
}
~~~
- [x] 상수별 메서드 구현을 상수별 데이터와 결합할 수 있음
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
~~~java
    public static void main(String[] args) {
        double x = Double.parseDouble(args[0]);
        double y = Double.parseDouble(args[1]);
        for (Operation op : Operation.values()) {
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
        }
    }
~~~
- [x] 열거 타입의 정적 필드 중 열거 타입의 생성자에서 접근할 수 있는 것은 상수 변수뿐
#### 열거 타입 생성자가 실행되는 시점에는 정적 필드들이 아직 초기화되기 전이라, 자기 자신을 추가하지 못하게 하는 제약이 꼭 필요함
- [x] 상수별 메서드 구현에는 열거 타입 상수끼리 코드를 공유하기 어렵다는 단점이 있음
~~~java
enum PayrollDay {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;

    private static final int MINS_PER_SHIFT = 8 * 60;

    int pay(int minutesWorked, int payRate) {
        int basePay = minutesWorked * payRate;

        int overtimePay;
        switch (this) {
            case SATURDAY:
            case SUNDAY:
                overtimePay = basePay / 2;
                break;
            default:
                overtimePay = minutesWorked <= MINS_PER_SHIFT ?
                        0 : (minutesWorked - MINS_PER_SHIFT) * payRate / 2;
        }

        return basePay + overtimePay;
    }
}
~~~
- [x] 간결하지만, 관리 관점에서는 위험한 코드
- [x] 휴가와 같은 새로운 값을 열거 타입에 추가하려면 그 값을 처리하는 case 문을 잊지 말고 쌍으로 넣어줘야 함
#### 가장 깔끔한 방법은 새로운 상수를 추가할 때 잔업수당 "전략"을 선택하도록 하는 것
~~~java
enum PayrollDay {
    MONDAY(WEEKDAY), TUESDAY(WEEKDAY), WEDNESDAY(WEEKDAY),
    THURSDAY(WEEKDAY), FRIDAY(WEEKDAY), SATURDAY(WEEKEND), SUNDAY(WEEKEND);

    private final PayType payType;


    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    int pay(int minutesWorked, int payRate) {
        return payType.pay(minutesWorked, payRate);
    }

    enum PayType {
        WEEKDAY {
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked <= MINS_PER_SHIFT ? 0 : (minsWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int mins, int payRate);

        private static final int MINS_PER_SHIFT = 8 * 60;
        
        int pay(int minsWorked, int payRate) {
            int basePay = minsWorked * payRate;
            return basePay + overtimePay(minsWorked, payRate);
        }
    }
}
~~~
#### 기존 열거 타입에 상수별 동작을 혼합해 넣을 때는 switch 문이 좋은 선택이 될 수 있음
~~~java
public static Operation inverse(Operation op) {
    swtich(op) {
        case PLUS: return Operation.MINUS;
        case MINUS: return Operation.PLUS;
        case TIMES: return Operation.DIVIDE;
        case DIVIDE: return Operation.TIMES;

        default: throw new AssertionError("알 수 없는 연산: ":+op)
    }
}
~~~
#### 필요한 원소를 컴파일타임에 다 알 수 있는 상수 집합이라면 항상 열거 타입을 사용하자
#### 열거 타입에 정의된 상수 개수가 영원히 고정불변일 필요는 없음
#### 열거 타입은 확실히 정수 상수보다 뛰어남
#### 대다수 열거 타입이 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작하게 할 때는 필요함
#### 이런 열거 타입에서는 switch 문 대신 상수별 메서드 구현을 사용하자
#### 열거 타입 상수 일부가 같은 동작을 공유한다면 전략 열거 타입 패턴을 사용하자!