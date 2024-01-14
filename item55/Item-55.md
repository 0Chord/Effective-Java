# 옵셔널 반환은 신중히 하라
- [x] 예외는 진짜 예외적인 상황에서만 사용해야 하며 예외를 생성할 때 스택 추적 전체를 캡처하므로 비용도 만만치 않음
- [x] null을 반환하면 이런 문제가 생기지 않지만, 그 나름의 문제가 있음
- [x] null을 반환할 수 있는 메서드를 호출할 때는, 별도의 null 처리 코드를 추가해야 함
- [x] null 처리를 무시하고 반환된 null 값을 어딘가에 저장해두면 언젠가 NullPointerException이 발생할 수 있음
- [x] Optioanl<T>는 null이 아닌 T 타입 참조를 하나 담거나, 혹은 아무것도 담지 않을 수 있음
- [x] 보통은 T를 반환해야 하지만 특정 조건에서는 아무것도 반환하지 않아야 할 때 T 대신 Optional<T>를 반환하도록 선언하면 됨
- [x] 옵셔널을 반환하는 메서드는 예외를 던지는 메서드보다 유연하고 사용하기 쉬우며, null을 반환하는 메서드보다 오류 가능성이 작음
~~~java
public static <E extends Comparable<E>> E max(Collection<E> c) {
    if (c.isEmpty())
        throw new IllegalArgumentException("빈 컬렉션");
    
    E result = null;
    for (E e : c)
        if (result == null || e.compareTo(result) > 0)
            result = Objects.requireNonNull(e);

    return result;
}
~~~
- [x] 빈 컬렉션을 건네면 IllegalArgumentException을 던짐
~~~java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    if (c.isEmpty())
        return Optional.empty();
    
    E result = null;
    for (E e : c)
        if (result == null || e.compareTo(result) > 0)
            result = Objects.requireNonNull(e);

    return Optional.of(result);
}
~~~
- [x] 빈 옵셔널은 Optional.empty()로 만들고, 값이 든 옵셔널은 Optional.of(value)로 생성함
#### 옵셔널을 반환하는 메서드에서든 절대 null을 반환하지 말자
#### 옵셔널은 검사 예외와 취지가 비슷함, 반환 값이 없을 수도 있음을 API 사용자에게 명확히 알려줌
~~~java
String lastWordInLexicon = max(words).orElse("단어 없음...");
~~~
- [x] 기본값을 설정하는 방법
~~~java
Toy myToy = max(toys).orElseThrow(TemperTantrumException::new);
~~~
~~~java
Element lastNobleGas = max(Elements.NOBLE_GASES).get();
~~~
- [x] 기본값을 설정하는 비용이 아주 커서 부담이 될 때가 있음
- [x] 그럴 때는 Supplier<T>를 인수로 받는 orElseGet을 사용하면, 값이 처음 필요할 때 Supplier<T>를 사용해 생성하므로 초기 설정 비용을 낮출 수 있음
#### 컬렉션, 스트림, 배열, 옵셔널 같은 컨테이너 타입은 옵셔널로 감싸면 안 됨
- [x] 빈 Optional<List<T>>를 반환하기 보다는 빈 List<T>를 반환하는 게 좋음
#### 결과가 없을 수 있으며, 클라이언트가 이 상황을 특별하게 처리해야 한다면 Optional<T>를 반환함
- [x] 박싱된 기본 타입을 담는 옵셔널은 기본 타입 자체보다 무거울 수밖에 없음 -> 값을 두 겹이나 감싸기 때문
#### 박싱된 기본 타입을 담은 옵셔널을 반환하는 일은 없도록 하자!
#### 옵셔널을 컬렉션의 키, 값, 원소나 배열의 원소로 사용하는 게 적절한 상황은 거의 없음
### 값을 반환하지 못할 가능성이 있고, 호출할 때마다 반환값이 없을 가능성을 염두에 둬야 하는 메서드라면 옵셔널을 반환해야 할 상황일 수 있음
### 옵셔널 반환에는 성능 저하가 뒤따르니, 성능에 민감한 메서드라면 null을 반환하거나 예외를 던지는 편이 나을 수 있음