# 배열보다는 리스트를 사용하라
- [x] 배열은 공변
  - [x] Sub가 Super의 하위 타입이라면 배열 Sub[]는 배열 Super[]의 하위 타입이 됨
- [x] 제네릭은 불공변
  - [x] 즉 서로 댜른 타입 Type1과 Type2가 있을 때, List<Type1>은 List<Type2>의 하위 타입도 아니고 상위 타입도 아님
~~~java
Object[] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다."; // ArrayStoreException을 던짐
~~~
- [x] 런타임시 실패함
~~~java
List<Object> ol = new ArrayList<Long>(); // 호환되지 않는 타입
ol.add("타입이 달라 넣을 수 없다");
~~~
- [x] 컴파일 시 실패함
#### 배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없음
- [x] 코드를 new List<E>[], new List<String>[], new E[] 식으로 작성하면 컴파일할 때 제네릭 배열 생성 오류를 일으킴
  - [x] 타입이 안전하지 않기 때문 -> 컴파일러가 자동 생성한 형변환 코드에서 런타임에 ClassCastException이 발생할 수 있음
~~~java
List<String>[] stringLists = new List<String>[1];
List<Integer> intList = List.of(42);
Object[] objects = stringLists;
objects[0] = intList;
String s = stringLists[0].get(0);
~~~
#### E, List<E>, List<String> 같은 타입을 실체화 불가 타입이라 함
- [x] 실체화되지 않아서 런타임에는 컴파일타임보다 타입 정보를 적게 가지는 타입
~~~java
public class Chooser{
    private final Object[] choiceArray;

    public Chooser(Collection choices) {
        choiceArray = choices.toArray();
    }

    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
~~~
- [x] 이 클래스를 사용하려면 choose 메서드를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환해야 함
- [x] 타입이 다른 원소가 들어 잇었다면 런타임에 형변환 오류가 날 것
~~~java
public class Chooser<T> {
    private final T[] choiceArray;

    public Chooser(Collection<T> choices) {
        choiceArray = (T[]) choices.toArray();
    }
}
~~~
#### T가 무슨 타입이닞 알 수 없으니 컴파일러는 이 형변환이 런타임에도 안전한지 보장할 수 없다
#### 제네릭에서는 원소의 타입 정보가 소거되어 런타임에는 무슨 타입인지 알 수 없음을 기억!
~~~java
public class Chooser<T> {
    private final List<T> choiceList;

    public Chooser(Collection<T> choices) {
        choiceList = new ArrayList<>(choices);
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
~~~
- [x] 코드양이 족므 늘었고 조금 더 느릴 테지만, 런타임에 ClassCastException을 만날 일은 없으니 그만한 가치가 있음
#### 배열과 제네릭에는 매우 다른 타입 규칙이 적용됨. 배열은 공변이고 실체화되는 반면, 제네릭은 불공변이고 타입 정보가 소거됨