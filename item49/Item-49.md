# 매개변수가 유효한지 검사하라
#### 매개변수 검사를 제대로 하지 못하면 몇 가지 문제가 발생할 수 있음
- [x] 메서드가 수행되는 중간에 모호한 예외를 던지며 실패할 수 있음
  - [x] 더 나쁜 상황은 메서드가 잘 수행되지만 잘못된 결과를 반환할 때
  - [x] 한층 더 나쁜 상황은 메서드가 잘 수행되지만 잘못된 결과를 반환할 때
- [x] 매개변수 검사에 실패하면 실패 원자성을 어기는 결과를 낳을 수 있음
#### public과 protected 메서드는 매개변수 값이 잘못됐을 때 던지는 예외를 문서화해야 함
- [x] 매개변수의 제약을 문서화한다면 그 제약을 어겼을 때 발생하는 예외도 함께 기술해야 함
~~~java
public BigInteger mod(BigInteger m) {
    if (m.signum() <= 0)
        throw new ArithmeticException("계수(m)은 양수여야 합니다. " +m);
}
~~~
#### java.util.Objects.requireNonNull 메서드는 유연하고 사용하기도 편하니, 더 이상 null 검사를 수동으로 하지 않아도 됨
~~~java
this.strategy = Objects.requireNonNull(strategy, "전략");
~~~
~~~java
private static void sort(long a[], int offset, int length) {
    assert a != null;
    assert offset>= 0 && offset <= a.length;
    assert length >= 0 && length <= a.length - offset;
}
~~~
- [x] 이 단언문들은 자신이 단언한 조건이 무조건 참이라고 선언한다는 것
- [x] 실패하면 AssertionError을 던짐
- [x] 런타임에 아무런 효과도, 아무런 성능 저하도 없음
#### 메서드나 생성자를 작성할 때면 그 매개변수들에 어떤 제약이 있을지 생각해야 함
#### 그 제약들을 문서화하고 메서드 코드 시작 부분에서 명시적으로 검사해야 함