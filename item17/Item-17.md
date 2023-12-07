# 변경 가능성을 최소화하라
- [x] 불변 클래스란 간단히 말해 그 인스턴스의 내부 값을 수정할 수 없는 클래스
  - [x] 불변 인스턴스에 간직된 정보는 고정되어 객체가 파괴되는 순간까지 절대 달라지지 않음
  - [x] String, 기본 타입의 박싱된 클래스들, Biginteger, BigDecimal이 속함
- [x] 불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전함
#### 클래스 불변 다섯 가지 규칙
- [x] 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않음
  -[x] setter 등을 미제공
- [x] 클래스를 확장할 수 없도록 함
  - [x] 하위 클래스에서 부주의하게 혹은 나쁜 의도로 객체의 상태를 변하게 만드는 상태를 막아줌
  - [x] 상태를 막는 대표적인 방법은 클래스를 final로 선언하는 것
- [x] 모든 필드를 final로 선언
  - [x] 시스템이 강제하는 수단을 이용해 설계자의 의도를 명확히 드러내는 방법
  - [x] 새로 생성된 인스턴스를 동기화 없이 다른 스레드로 건네도 문제없이 동작하게끔 보장하는 데도 필요
- [x] 모든 필드를 private으로 선언
  - [x] 필드가 참조하는 가변 객체를 클라이언트에서 직접 접근해 수정하는 일을 막아줌
- [x] 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 함
  - [x] 클래스에 가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야 함
  - [x] 이런 필드는 절대 클라이언트가 제공한 객체 참조를 가리키게 해서는 안 되며, 접근자 메서드가 그 필드를 그대로 반환해서도 안됨
  - [x] 생성자, 접근자, readObject 메서드 모두에서 방어적 복사를 수행
~~~java
public final class Complex {
    private final double re;
    private final double im;

    public Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }

    public double readPart() {
        return re;
    }

    public double imaginaryPart() {
        return im;
    }

    public Complex plus(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }

    public Complex minus(Complex c) {
        return new Complex(re - c.re, im - c.im);
    }

    public Complex times(Complex c) {
        return new Complex(re * c.re - im * c.im, re * c.im + im * c.re);
    }

    public Complex dividedBy(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp, (im * c.re - re * c.im) / tmp);
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) {
            return true;
        }
        if (!(obj instanceof Complex)) {
            return false;
        }

        Complex c = (Complex) obj;

        return Double.compare(c.re, re) == 0 && Double.compare(c.im, im) == 0;
    }

    @Override
    public int hashCode() {
        return 31 * Double.hashCode(re) + Double.hashCode(im);
    }

    @Override
    public String toString() {
        return "(" + re + " + " + im + "i)";
    }
}
~~~
- [x] 피연산자에 함수를 적용해 그 결과를 반환하지만, 피연산자 자체는 그대로인 프로그래밍 패턴을 함수형 프로그래밍이라고 함
- [x] 절차적 혹은 명령형 프로그래밍에서는 메서드에서 피연산자인 자신을 수정해 자신의 상태를 변하게 됨
#### 불변 객체는 단순함, 불변 객체는 생성된 시점의 상태를 파괴될 때까지 그대로 간직
#### 불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없음
- [x] 불변 객체에 대해서는 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니 불변 객체는 안심하고 공유할 수 있음
  - [x] 불변 클래스라면 한번 만든 인스턴스를 최대한 재활용
  - [x] 가장 쉬운 재활용 방법은 자주 쓰이는 값들을 상수(public static final)로 제공하는 것
~~~java
public static final Complex ZERO = new Complex(0, 0);
public static final Complex ONE = new Complex(1, 0);
public static final Complex I = new Complex(0, 1);
~~~
- [x] 불변 클래스는 자주 사용되는 인스턴스를 캐상하여 같은 인스턴스를 중복 생성되지 않게 해주는 정적 팩터리를 제공할 수 있음
  - [x] 박싱된 기본 타입 클래스 전부와 BigInteger가 속함
- [x] 정적 팩터리를 사용하면 여러 클라이언트가 인스턴스를 고융하여 메모리 사용량과 가비지 컬렉션 비용이 줄어듬
- [x] 새로운 클래스를 설계할 때 public 생성자 대신 정적 팩터리를 만들어두면, 클라이언트를 수정하지 않고도 필요에 따라 캐시 기능을 덧붙일 수 있음
#### 불변 클래스는 clone 메서드나 복사 생성자를 제공하지 않는 게 좋음
#### 불변 객체는 자유롭게 공유할 수 있음을 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있음
#### 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많음
- [x] 값이 바뀌지 않는 구성요소들로 이뤄진 객체라면 그 구조가 아무리 복잡하더라도 불변식을 유지하기 훨씬 수월하기 때문
#### 불변 객체는 그 자체로 실패 원자성을 제공
- [x] 상태가 절대 변하지 않으니 잠간이라도 불일치 상태에 빠질 가능성이 없음
#### 불변 클래스는 값이 다르면 반드시 독립된 객체로 만들어야 함
- [x] 자신을 상속하지 못하게 하는 가장 쉬운 방법은 final 클래스로 선언하는 것이지만, 모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공하는 방법
~~~java
public final class Complex {
  private final double re;
  private final double im;

  private Complex(double re, double im) {
    this.re = re;
    this.im = im;
  }

  public static Complex valueOf(double re, double im) {
    return new Complex(re, im);
  }
}
~~~
- [x] 패키지 바깥의 클라이언트에서 바라본 이 불변 객체는 사실상 final임
- [x] public이나 protected 생성자가 없으니 다른 패키지에서는 이 클래스를 확장하는 게 불가능하기 때문
- [x] 정적 팩터리 방식은 다수의 구현 클래스를 활용한 유연성을 제공하고, 이에 더해 다음 릴리스에서 객체 캐싱 기능을 추가해 성능을 끌어올릴 수도 있음
#### 신뢰할 수 없는 하위 클래스의 인스턴스라고 확인되면, 이 인수들을 가변이라 가정하고 방어적으로 복사해 사용해야 함
~~~java
public static BigInteger safeInstance(BigInteger val) {
  return val.getClass() == BigInteger.class ? val : new BigInteger(val.toByteArray());
}
~~~
#### 클래스는 꼭 필요한 경우가 아니라면 불변이어야 함
- [x] 게터(getter)가 있다고 해서 무조건 세터(setter)를 만들지 말기
- [x] 단점은 특정 상황에서의 잠재적 성능 저하뿐
#### 단순한 값 객체는 항상 불변으로 만들기
#### 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분은 최소한으로 줄이기
- [x] 꼭 변경해야 할 필드를 뺸 나머지 모두를 final로 선언
### 정리
#### 다른 합당한 이유가 없다면 모든 필드는 private final이어야 함
#### 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 함
- [x] 확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공해서는 안 됨