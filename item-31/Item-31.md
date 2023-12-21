# 한정적 와일드카드를 사용해 API 유연성을 높이라
- [x] 매개변수화 타입은 불공변
  - [x] 서로 다른 타입 Type1과 Type2가 있을 때 List<Type1>은 List<Type2>의 하위 타입도 상위 타입도 아님
  - [x] List<String>은 List<Object>의 하위 타입이 아니라는 뜻인데, List<Object>는 어떤 객체든 넣을 수 있지만 List<String>에는 문자열만 넣을 수 있음
    - [x] List<String>은 List<Object>가 하는 일을 제대로 수행하지 못하니 하위 타입이 될 수 없음
    - [x] 리스코프 치환 원칙에 어긋남
~~~java
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
}
~~~
~~~java
public void pushAll(Iterable<E> src) {
    for (E e : src)
        push(e);
}
~~~
- [x] 자바는 한정적 와일드카드 타입이라는 특별한 매개변수화 타입을 지원함
- [x] pushAll의 입력 매개변수 타입은 E의 Iterable이 아니라 E의 하위 타입의 Iterable이어야 하며 와일드 카드 타입 Iterable<? extends E>가 정확히 이런 뜻
~~~java
public void pushAll(Iterable<? extends E> src) {
    for (E e: src)
        push(e);
}
~~~
- [x] Stack은 물론 이를 사용하는 클라이언트 코드도 말끔히 컴파일됨
~~~java
public void popAll(Collection<E> dst) {
    while (!isEmpty())
        dst.add(pop());
}

Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = ...;
numberStack.popAll(objects);
~~~
- [x] Collection<Object>는 Collection<Number>의 하위 타입이 아니다
- [x] E의 Collection이 아니라 E의 상위 타입의 Collection이어야 함
~~~java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty())
        dst.add(pop());
}
~~~
#### 유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라
- [x] 팩스(PESC) : producer-extends, consumer-super
  - [x] 매개변수화 타입 T가 생산자라면 <? extends T>를 사용하고, 소비자라면 <? super T>를 사용하라
- [x] PESC 공식은 와일드카드 타입을 사용하는 기본 원칙
~~~java
public Chooser(Collection<T> choices);
public Chooser(Collection<? extends T> choices);
~~~
- [x] choices 컬렉션은 T 타입의 값을 생산하기만 하니 T를 확장하는 와일드카드 타입을 사용해 선언해야 함
~~~java
public static <E> Set<E> union(Set<E> s1, Set<E> s2)

public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)
~~~
- [x] s1과 s2 모두 생성자이니 PECS 공식에 따라 선언
#### 클래스 사용자가 와일드카드 타입을 신경써야 한다면 그 API에 무슨 문제가 있을 가능성이 큼
~~~java
public static <E extends Comparable<? super E>> E max(
  List<? extends E> list)
~~~
- [x] 입력 매개변수에서는 E 인스턴스를 생산하므로 원래의 List<E>를 List<? extends E>로 수정함
- [x] Comparable은 언제나 소비자이므로, 일반적으로 Comparable<E> 보다는 Comparable<? super E>를 사용하는 편이 나음
~~~java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
~~~
~~~java
public static void swap(List<?> list, int i, int j) {
  list.set(i, list.set(j, list.get(i)));
}
~~~
- [x] 리스트의 타입이 List<?>인데, List<?>에는 null 외에는 어떤 값도 넣을 수 없음
~~~java
public static void swap(List<?> list, int i, int j) {
  swapHelper(list, i, j);
}

private static <E> void swapHelper(List<E> list, int i, int j) {
  list.set(i, list.set(j, list.get(i)));
}
~~~
#### 메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라
#### 조금 복잡하더라도 와일드카드 타입을 적용하면 API가 훨씬 유연해짐
#### 널리 쓰일 라이브러리를 작성한다면 반드시 와일드카드 타입을 적절히 사용해줘야 함
#### 생산자는 extends를 소비자는 super를 사용
