# 이왕이면 제네릭 메서드로 만들라
- [x] 매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭
  - [x] Collections의 알고리즘 메서드(binarySearch, sort 등)는 모두 제네릭
~~~java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
~~~
- [x] 경고를 없애려면 이 메서드를 타입 안전하게 만들어야 함
  - [x] 메서드 선언에서의 세 집합의 원소 타입을 타입 매개변수로 명시하고, 메서드 안에서도 이 타입 매개변수만 사용하게 수정하면 됨
  - [x] 타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 옴
  - [x] 타입 매개변수 목록은 <E>이고 반환 타입은 Set<E>
- [x] 타입 매개변수의 명명 규칙은 제네릭 메서드나 제네릭 타입이나 똑같음
~~~java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
~~~
~~~java
public static void main(String[] args) {
    Set<String> guys = Set.of("톰", "딕", "해리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guys, stooges);
    System.out.println(aflCio);
}
~~~
#### 제네릭 싱글턴 팩터리 패턴
~~~java
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
  return (UnaryOperator<T>) IDENTITY_FN;
}
~~~
- [x] 항등 함수란 입력 값을 수정 없이 그대로 반환하는 특별한 함수이므로, T가 어떤 타입이든 UnaryOperator<T>를 사용해도 타입 안전함
~~~java
public static void main(String[] args) {
  String[] strings = { "삼베", "대마", "나일론" };
  UnaryOperator<String> sameString = identityFunction();
  for (String s: strings)
    System.out.println(sameString.apply(s));

  Number[] numbers = {1, 2.0, 3L };
  UnaryOperator<Number> sameNumber = identityFunction();
  for (Number n : numbers)
    System.out.println(sameNumber.apply(n));
}
~~~
~~~java
public static <E extends Comparable<E>> E max(Collection<E> c);
~~~
- [x] 재귀적 한정 타입인 <E extends Comparable<E>>는 "모든 타입 E는 자신과 비교할 수 있다"가 된다
~~~java
public static <E extends Comparable<E>> E max(Collection<E> c) {
  if (c.isEmpty())
    throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
  
  E result = null;
  for (E e : c)
    if (result == null || e.compareTo(result) > 0)
      result = Objects.requireNonNull(e);

  return result;
}
~~~
#### 제네릭 타입과 마찬가지로, 클라이언트에서 입력 매개변수와 반환값을 명시적으로 형변환해야 하는 메서드보다 제네릭 메서드가 더 안전하며 사용하기도 쉬움
#### 타입과 마찬가지로, 메서드도 형변환 없이 사용할 수 있는 편이 좋으며, 많은 경우 그렇게 하려면 제네릭 메서드가 되어야 함