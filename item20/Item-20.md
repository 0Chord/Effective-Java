# 추상 클래스보다는 인터페이스를 우선하라
- [x] 자바가 제공하는 다중 구현 메커니즘은 인터페이스와 추상 클래스, 이렇게 두가지
- [x] 자바 8부터 인터페이스도 디폴트 메서드(default method)를 제공할 수 있게 되어 두 메커니즘 모두 인스턴스 메서드를 구현 형태로 제공할 수 있게 됨
- [x] 인터페이스와 추상 클래스의 가장 큰 차이는 추상 클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다는 점
- [x] 자바는 단일 상속만 지원하니, 추상 클래스 방식은 새로운 타입을 정의하는 데 커다란 제약을 안게 되는 셈
- [x] 인터페이스가 선언한 메서드를 모두 정의하고 그 일반 규약을 잘 지킨 클래스라면 다른 어떤 클래스를 상속했든 같은 타입으로 취급됨
#### 기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다
- [x] 인터페이스가 요구하는 메서드를 (아직 없다면) 추가하고, 클래스 선언에 implements 구문만 추가하면 끝
- [x] 두 클래스가 같은 추상 클래스를 확장하길 원한다면, 그 추상 클래스는 계층구조상 두 클래스의 공통 조상이어야 함
  - [x] 이 방식은 클래스 계층구조에 커다란 혼란을 일으킴
#### 인터페이스는 믹스인(maxin) 정의에 안성맞춤
- [x] 믹스인이란 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 주된 타입 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 줌
  - [x] 예를 들어, Comparable은 자신을 구현한 클래스의 인스턴스들끼리는 순서를 정할 수 있다고 선언하는 믹스인 인터페이스
#### 인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있음
~~~java
public interface Singer {
    AudioClip sing(Song s);
}

public interface Songwriter {
    Song compose(int chartPosition);
}
~~~
- [x] 작곡도 하는 가수가 있는데, 타입을 인터페이스로 정의하면 가수 클래스가 Singer와 Songwriter를 모두 구현해도 전혀 문제되지 않음
- [x] Singer와 Songwriter 모두를 확장하고 새로운 메서드까지 추가한 제3의 인터페이스를 정의할 수도 있음
~~~java
public interface SingerSongwriter extends Singer, Songwriter {
    AudioClip strum();
    void actSensitive();
}
~~~
#### 래퍼 클래스 관용구와 함께 사용하면 인터페이스는 기능을 향상시키는 안전하고 강력한 수단이 됨
- [x] 타입을 추상 클래스로 정의해두면 그 타입에 기능을 추가하는 방법은 상속뿐
- [x] 디폴트 메서드를 제공할 때는 상속하려ㅁ는 사람을 위한 설명을 문서화해야 함
- [x] 인터페이스는 인스턴스 필드를 가질 수 없고 public이 아닌 다른 정적 멤버도 가질 수 없음
~~~java
    static List<Integer> intArrayAsList(int[] a) {
        Objects.requireNonNull(a);

        return new AbstractList<>() {
            @Override
            public Integer get(int index) {
                return null;
            }

            @Override
            public Integer set(int i, Integer val) {
                int oldVal = a[i];
                a[i] = val;
                return oldVal;
            }

            @Override
            public int size() {
                return a.length;
            }
        };
    }
~~~
#### 일반적으로 다중 구현용 타입으로는 인터페이스가 가장 적합함
#### 복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 꼭 고려해보기
#### 골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공하여 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋음