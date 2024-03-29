# 박싱된 기본 타입보다는 기본 타입을 사용하라
- [x] 자바의 데이터 타입은 크게 두 가지로 나눌 수 있음
- [x] int, double, boolean 같은 기본 타입과 String, List 같은 참조 타입
- [x] 각각의 기본 타입에는 대응하는 참조 타입이 하나씩 있으며, 이를 박싱된 기본 타입이라고 함
  - [x] Integer, Double, Boolean
#### 기본 타입과 박싱된 기본 타입의 주된 차이는 크게 세 가지
- [x] 기본 타입은 값만 가지고 있으나, 박싱된 기본 타입은 값에 더해 식별성이란 속성을 가짐
  - [x] 박싱된 기본 타입의 두 인스턴스는 값이 같아도 서로 다르다고 식별될 수 있음
- [x] 기본 타입의 값은 언제나 유효하나, 박싱된 기본 타입은 유효하지 않은 값, null을 가질 수 있음
- [x] 기본 타입이 박싱된 기본 타입보다 시간과 메모리 사용면에서 더 효율적임
~~~java
Comparator<Integer> naturalOrder = 
    (i, j) -> (i < j) ? -1 : (i == j ? 0 : 1);
~~~
#### 박싱된 기본 타입에 == 연산자를 사용하면 오류가 발생함
#### 기본 타입을 다루는 비교자가 필요하다면 Comparator.naturalOrder()를 사용하자
~~~java
Comparator<Integer> naturalOrder = (iBoxed, jBoxed) -> {
    int i = iBoxed, j = jBoxed;
    return i < j ? -1 : ( i == j ? 0 : 1);
};
~~~
#### 기본 타입과 박싱된 기본 타입을 혼용한 연산에서는 박싱된 기본 타입의 박싱이 자동으로 풀림
#### 박싱된 기본 타입은 언제 사용해야 하는가?
- [x] 컬렉션의 원소, 키, 값으로 씀
  - [x] 컬렉션은 기본 타입을 담을 수 없으므로 어쩔 수 없이 박싱된 기본 타입을 써야만 함
- [x] 리플렉션을 통해 메서드를 호출할 때도 박싱된 기본 타입을 사용해야 함
#### 기본 타입과 박싱된 기본 타입 중 하나를 선택해야 한다면 가능하면 기본 타입을 사용하라
#### 오토박싱이 박싱된 기본 타입을 사용할 때의 번거로움을 줄여주지만, 그 위험가지 없애주지는 않음
#### 같은 연산에서 기본 타입과 박싱된 기본 타입을 혼용하면 언박싱이 이뤄지며, 언박싱 과정에서 NullPointerException을 던질 수 있음
#### 기본 타입을 박싱하는 작업은 필요 없는 객체를 생성하는 부작용이 나올 수 있음