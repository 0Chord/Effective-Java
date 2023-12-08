# 상속보다는 컴포지션을 사용하라
- [x] 상속은 코드를 재사용하는 강력한 수단이지만, 항상 최선은 아니다
- [x] 상위 클래스와 하위 클래스를 모두 같은 프로그래머가 통제하는 패키지 안에서라면 상속도 안전한 방법
- [x] 확장할 목적으로 설계되었고 문서화도 잘 된 클래스도 마찬가지로 안전함
- [x] 하지만 일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구체 클래스를 상속하는 일은 위험
#### 메서드 호출과 달리 상속은 캡슐화를 깨뜨린다
- [x] 상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있다
- [x] 상위 클래스 설계자가 확장을 충분히 고려하고 문서화도 제대로 해두지 않으면 하위 클래스는 상위 클래스의 변화에 발맞춰 수정되어야만 함
~~~java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    public InstrumentedHashSet() {
    }

    public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}
~~~
- [x] 제대로 작동하지 않음
~~~java
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("틱","탁탁","펑"));
~~~
- [x] getAddCount()를 호출하면 3을 반환하리라 기대하지만, 실제로는 6을 반환
  - [x] HashSet의 addAll 메서드가 각 원소를 add 메서드를 호출해 추가하기 때문
- [x] 메서드를 작성할 때는 상위 클래스의 메서드는 존재하지도 않았으니, 상위 클래스의 메서드가 요구하는 규약을 만족하지 못할 가능성이 큼
#### 기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하기
- [x] 기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션이라고 함
- [x] 새 클래스의 인스턴스 메서드들은 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환
  - [x] 이 방식을 전달이라 하며, 새 클래스의 메서드들을 전달 메서드라 부름
- [x] 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않음
~~~java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}
~~~
#### 재사용할 수 있는 전달 클래스
~~~java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;

    public ForwardingSet(Set<E> s) {
        this.s = s;
    }

    @Override
    public int size() {
        return s.size();
    }

    @Override
    public boolean isEmpty() {
        return s.isEmpty();
    }

    @Override
    public boolean contains(Object o) {
        return s.contains(o);
    }

    @Override
    public Iterator<E> iterator() {
        return s.iterator();
    }

    @Override
    public Object[] toArray() {
        return s.toArray();
    }

    @Override
    public <T> T[] toArray(T[] a) {
        return s.toArray(a);
    }

    @Override
    public boolean add(E e) {
        return s.add(e);
    }

    @Override
    public boolean remove(Object o) {
        return s.remove(o);
    }

    @Override
    public boolean containsAll(Collection<?> c) {
        return s.containsAll(c);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        return s.addAll(c);
    }

    @Override
    public boolean retainAll(Collection<?> c) {
        return s.retainAll(c);
    }

    @Override
    public boolean removeAll(Collection<?> c) {
        return s.removeAll(c);
    }

    @Override
    public void clear() {
        s.clear();
    }
}
~~~
- [x] InstrumentedSet은 HashSet의 모든 기능을 정의한 Set 인터페이스를 활용해 설계되어 견고하고 아주 유연함
- [x] 임의의 Set에 계측 기능을 덧씌어 새로운 Set으로 만드는 것이 이 클래스의 핵심
~~~java
        Set<Instant> times = new InstrumentedSet<>(new TreeSet<>(cmp));
        Set<E> s = new InstrumentedSet<>(new HashSet<>(INIT_CAPACITY));
~~~
- [x] 컴포지션 방식은 한 번만 구현해두면 어떠한 Set 구현체라도 계측할 수 있으며, 기존 생성자들과도 함께 사용할 수 있음
~~~java
static void walk(Set<Dog> dogs) {
  InstrumentedSet<Dog> iDogs = new InstrumentedSet<>(dogs);
}
~~~
- [x] 다른 Set 인스턴스를 감싸고 있다는 뜻에서 InstrumentedSet 같은 클래스를 래퍼 클래스라 하며, 다른 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패턴이라고 함
- [x] 컴포지션과 전달의 조합은 넓은 의미로 위임이라고 함
- [x] 래퍼 객체가 내부 객체에 자기 자신의 참조를 넘기는 경우만 위임에 해당함
#### 래퍼 클래스는 단점이 거의 없음
- [x] 래퍼 클래스가 콜백 프레임워크와는 어울리지 않다는 점만 주의하면 됨
- [x] 콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출 때 사용하도록 함
- [x] 내부 객체는 자신이 감싸고 있는 래퍼의 존재를 모르니 대신 자신의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 됨
- [x] 상속은 반드시 하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황에서만 쓰여야 함
#### 상속화는 강력하지만 캡슐화를 해친다는 문제가 있음. 상속은 상위 클래스와 하위 클래스가 순수한 is-a 관계일 때만 사용해야 함
#### 하위 클래스의 패키지가 상위 클래스와 다르고, 상위 클래스가 확장을 고려해 설계되지 않았다면 여전히 문제될 수 있음
#### 상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용하자
#### 래퍼 클래스로 구현할 적당한 인터페이스가 있다면 더욱 그럼. 래퍼 클래스는 하위 클래스보다 견고하고 강력