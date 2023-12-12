# 인터페이스는 타입을 정의하는 용도로만 사용하라
- [x] 인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입 역할을 함
- [x] 클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 얘기해주는 것
- [x] 상수 인터페이스란 메서드 없이, 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스를 말함
~~~java
public interface PhysicalConstants {

    static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    static final double ELECTRON_MASS = 9.109_383_56e-31;
}
~~~
- [x] 상수 인터페이스 안티패턴은 인터페이스를 잘못 사용한 예시
- [x] 클래스에서 사용하는 상수는 외부 인터페이스가 아니라 내부 구현에 해당됨
- [x] 상수 인터페이스를 구현하는 것은 이 내부 구현을 클래스의 API로 노출하는 행위
- [x] 클래스가 어떤 상수 인터페이스를 사용하든 사용자에게는 아무런 의미가 없음
- [x] 오히려 사용자에게 혼란을 주기도 하며, 더 심하게는 클라이언트 코드가 내부 구현에 해당하는 이 상수들에 종속되게 됨
#### 특정 클래스나 인터페이스와 강하게 연관된 상수라면 그 클래스나 인터페이스 자체에 추가해야 함
- [x] 모든 숫자 기본 타입의 박싱 클래스가 대표적으로, Integer와 Double에 선언된 MIN_VALUE와 MAX_VALUE 상수가 예시
- [x] 열거 타입으로 나타내기 적합한 상수라면 열거 타입으로 만들어 공개하면 됨
~~~java
public class PhysicalConstants {

    private PhysicalConstants() {
    }

    static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    static final double ELECTRON_MASS = 9.109_383_56e-31;
}
~~~
#### 유틸리티 클래스에 정의된 상수를 클라이언트에서 사용하려면 클래스 이름까지 명시해야 함
- [x] PhysicalConstants.AVOGADROS_NUMBER
- [x] 유틸리티 클래스의 상수를 빈번히 사용한다면 정적 임포트(static import)하여 클래스 이름은 생략할 수 있음
### 인터페이스는 타입을 정의하는 용도로만 사용. 상수 공개용 수단으로 사용하지 말자 