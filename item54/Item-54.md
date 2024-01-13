# null이 아닌, 빈 컬렉션이나 배열을 반환하라
~~~java
private final Lis<Cheese> cheesesInStock = ...;

/**
* @return 매장 안의 모든 치즈 목록을 반환한다.
*   단, 재고가 하나도 없다면 null을 반환한다.
*/

public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
}
~~~
- [x] 이 코드처럼 null을 반환한다면, 클라이언트는 이 null 상황을 처리하는 코드를 추가로 작성해야 함
~~~java
List<Cheese> cheeses = shop.getCheeses();
if (cheeses != null && cheeses.contains(Cheese.STILTON))
    System.out.println("좋았어, 바로 그거야.");
~~~
- [x] 컬렉션이나 배열 같은 컨테이너(container)가 비었을 때 null을 반환하는 메서드를 사용할 때면 항시 이와 같은 방어 코드를 넣어줘야 함
- [x] 클라이언트에서 방어 코드를 빼먹으면 오류가 발생할 수 있음
#### 빈 컨테이너를 할당하는 데도 비용이 드니 null을 반환하는 쪽이 낫다는 주장도 있음
- [x] 성능 분석 결과 이 할당이 성능 저하의 주범이라고 확인되지 않는 한, 이 정도의 성능 차이는 신경 쓸 수준이 못됨
- [x] 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환할 수 있음
~~~java
public List<Cheese> getCheeses() {
    return new ArrayList<>(cheesesInStock);
}
~~~
- [x] 매번 똑같은 빈 '불변' 컬렉션을 반환하면 성능 차이를 해결가능함
~~~java
public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? Collections.emptyList() : new ArrayList<>(cheesesInStock);
}
~~~
- [x] 배열을 쓸 때에도 마찬가지로 절대 null을 반환하지 말고 길이가 0인 배열을 반환하라
~~~java
public Cheese[] getCheeses() {
    return cheesesInStock.toArray(new Cheese[0]);
}
~~~
- [x] 이 방식이 성능을 떨어뜨릴 것 같다면 길이 0짜리 배열을 미리 선언해두고 매번 그 배열을 반환하면 됨. 길이 0인 배열은 모두 불변이기 때문
~~~java
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
~~~
- [x] getCheeses는 항상 EMPTY_CHEESE_ARRAY를 인수로 넘겨 toArray를 호출함
#### null이 아닌, 빈 배열이나 컬렉션을 반환하라
#### null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어남