# 로 타입은 사용하지 말라

- [x] 클래스와 인터페이스 선언에 타입 매개변수가 쓰이면, 이를 제네릭 클래스 혹은 제네릭 인터페이스라 함
- [x] List 인터페이스는 원소의 타입을 나타내는 타입 매개변수 E를 받음
    - [x] 이 인터페이스의 완전한 이름은 List<E>지만, 짧게 그냥 List라고도 자주 씀
- [x] 제네릭 클래스와 제네릭 인터페이스를 통틀어 제네릭 타입이라 함

#### 각각의 제네릭 타입은 일련의 매개변수화 타입을 정의함

- [x] 먼저 클래스(혹은 인터페이스) 이름이 나오고, 꺽쇠괄호 안에 실제 타입 매개변수들을 나열함
    - [x] List<String>은 원소타입이 String인 리스트를 뜻하는 매개변수화 타입
    - [x] String이 정규타입 매개변수 E에 해당하는 실제 타입 매개변수
- [x] 제네릭 타입을 하나 정의하면 그에 딸린 로 타입도 함께 정의됨
- [x] 로 타입이란 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 떄를 의미함
    - [x] List<E>의 로 타입은 List
- [x] 로 타입은 타입 선언에서 제네릭 타입 정보가 전부 지워진 것처럼 동작하는데, 제네릭이 도래하기 전 코드와 호환되도록 하기 위한 궁여지책이라 할 수 있음

~~~java
private final Collection stamp=...;
// 실수로 동전을 넣는다.
        stamps.add(new Coin(...)); // unchecked call 경고를 내뱉음
~~~

~~~java
        for(Iterator i=stamps.iterator();i.hasNext();){
        Stamp stamp=(Stamp)i.next(); //ClassCastException을 던진다
        stamp.cancel();
        }
~~~

- [x] 이 예시에서는 오류가 발생하고 한참 뒤인 런타임에야 알아챌 수 있는데, 이렇게 되면 런타임에 문제를 겪는 코드와 원인을 제공한 코드가 물리적으로 상당히 떨어져 있을 가능성이 커짐
~~~java
private final Collection<Stamp> stamps = ...;
~~~
- [x] stamps에 엉뚱한 타입의 인스턴스를 넣으려 하면 컴파일 오류가 발생함
- [x] 컴파일러는 컬렉션에서 원소를 꺼내는 모든 곳에 보이지 않는 형변환을 추가하여 절대 실패하지 않음을 보장함
#### 로 타입을 쓰는 걸 언어 차원에서 막아 놓지는 않았지만 절대로 써서는 안됨
#### 로 타입을 쓰면 제네릭이 안겨주는 안정성과 표현력을 모두 잃게 됨
#### List<Object> 같은 매개변수화 타입을 사용할 때와 달리 List 같은 로 타입을 사용하면 타입 안전성을 잃게 됨
~~~java
public static void main(String[] args) {
  List<String> strings = new ArrayList<>();
  unsafeAdd(strings, Integer.valueOf(42));
  String s = strings.get(0);
}

private static void unsafeAdd(List list, Object o)  {
  list.add(o);
}
~~~
- [x] strings.get(0)의 결과를 형변환하려 할 때 ClassCastException을 던짐
  - [x] Integer를 String으로 변환하려 시도한 것
~~~java
static int numElementsInCommon(Set s1, Set s2) {
  int result = 0;
  for (Object o1 : s1)
    if (s2.contains(o1)
      result++;
  return result;
}
~~~
- [x] 동작은 하지만 로 타입을 사용해 안전하지 않음
- [x] 비한정적 와일드카드 타입을 대신 사용하는 게 좋음
- [x] 제네릭 타입을 쓰고 싶지만 실제 타입 매개변수가 무엇인지 신경 쓰고 싶지 않다면 물음표(?)를 사용하자
  - [x] 제네릭 타입인 Set<E>의 비한정적 와일드카드 타입은 Set<?>
- [x] 어떤 타입이라도 담을 수 있는 가장 범용적인 매개변수화 Set 타입
~~~java
static int numElementsInCommon(Set<?> s1, Set<?> s2) {...}
~~~
#### class 리터럴에는 로 타입을 써야 함
- [x] List.class, String[].class, int.class는 허용하고 List<String>class, List<?>.class는 허용하지 않음
- [x] instanceof 연산자는 비한정적 와일드카드 타입 이외의 매개변수화 타입에는 적용할 수 없음
- [x] 로 타입이든 비한정적 와일드카드 타입이든 instanceof는 완전히 똑같이 동작함
~~~java
if (o instanceof Set) {
  Set<?> s = (Set<?>) o;
  ...
}
~~~
#### 로 타입을 사용하면 런타임에 예외가 일어날 수 있으니 사용하면 안 됨
#### Set<Object>는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이고, Set<?>는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입
#### Set<Object>와 Set<?>는 안전하지만, 로 타입인 Set은 안전하지 않음