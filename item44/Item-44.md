# 표준 함수형 인터페이스를 사용하라
- [x] 함수 객체를 매개변수로 받는 생성자와 메서드를 더 많이 만들어야 함
- [x] LinkedHashMap에서 removeEldestEntry를 재정의하면 캐시로 사용할 수 있음
~~~java
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return size() > 100;
}
~~~
- [x] removeEldestEntry는 size()를 호출해 맵 안의 원소 수를 알아내는데, removeEldestEntry는 size()를 호출해 맵 안의 원소 수를 알아내는데, rmoveEldestEntry가 인스턴스 메서드라 가능한 방식
- [x] 생성자에 넘기는 함수 객체는 이 맵의 인스턴스 메서드가 아님
~~~java
@FunctionalInterface interface EldestEntryRemovalFunction<K,V>{
    boolean remove(Map<K,V> map, Map.Entry<K,V> eldest);
}
~~~
#### 필요한 용도에 맞는 게 있다면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하라
#### Operator 인터페이스는 인수가 1개인 UnaryOperator과 2개인 BinaryOperator로 나뉘며, 반환값과 인수의 타입이 같은 함수를 뜻함
- [x] Predicate 인터페이스는 인수 하나를 받아 boolean을 반환하는 함수를 뜻하며, Function 인터페이스는 인수와 반환 타입이 다른 함수를 뜻함
- [x] Supplier 인터페이스는 인수를 받지 않고 값을 반환하는 함수를, Consumer 인터페이스는 인수를 하나 받고 반환값은 없는 함수를 뜻함
- [x] 기본 인터페이스는 기본 타입인 int, long, double 용으로 각 3개씩 변형이 생겨남
#### LongFunction<int[]>은 long 인수를 받아 int[]을 반환함
#### ToLongFunction<int[]>은 int[] 인수를 받아 long을 반환함
#### 기본 함수형 인터페이스에 박싱도니 기본 타입을 넣어 사용하지는 말기
#### 직접 만든 함수형 인터페이스에는 항상 @FunctionalInterface 애너테이션 사용하기
- [x] 자주 쓰이며, 이름 자체가 용도를 명확히 설명해줌
- [x] 반드시 따라야 하는 규약이 있음
- [x] 유용한 디폴트 메서드를 제공할 수 있음
#### 서로 다른 함수형 인터페이스를 같은 위치의 인수로 받는 메서드들을 다중 정의해서는 안 됨
#### 입력값과 반환값에 함수형 인터페이스를 활용하라
#### java.util.function 패키지의 표준 함수형 인터페이스를 사용하는 것이 가장 좋은 선택
#### 흔치는 않지만, 직접 새로운 함수형 인터페이스를 만들어 쓰는 편이 나을 수도 있음