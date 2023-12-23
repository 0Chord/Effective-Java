# 타입 안전 이종 컨테이너를 고려하라

- [x] 제네릭은 Set\<E>, Map<K,V> 등의 컬렉션과 ThreadLocal\<T>, AtomicReference\<T> 등의 단일원소 컨테이너에도 흔히 쓰임
- [x] 하나의 컨테이너에서 매개변수화할 수 있는 타입의 수가 제한됨
    - [x] Set에는 원소의 타입을 뜻하는 단 하나의 타입 매개변수만 있으면 되며, Map에는 키와 값의 타입을 뜻하는 2개만 필요한 식
- [x] 컨테이너 대신 키를 배개면수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공하면 됨
    - [x] 타입 안전 이종 컨테이너 패턴
- [x] 간단한 예로 타입별로 즐겨 찾는 인스턴스를 저장하고 검색할 수 있는 Favorites 클래스를 생각해보기
    - [x] 각 타입에 Class 객체를 매개변수화한 키 역할로 사용하면 되는데, 이 방식이 동작하는 이유는 class의 클래스가 제네릭이기 때문
    - [x] 이 방식이 동작하는 이유는 class의 클래스가 제네릭이기 때문
    - [x] Class\<String>은 String.class이고, Integer.class의 타입은 Class\<Integer>이기 때문

~~~java
public class Favorites {
    public <T> void putFavorite(Class<T> type, T instance);

    public <T> T getFavorite(Class<T> type);
}
~~~

~~~java
public static void main(String[]args){
        Favorites f=new Favorites();

        f.putFavorite(String.class,"Java");
        f.putFavorite(Integer.class,0xcafebabe);
        f.putFavorite(Class.class,Favorites.class);

        String favoriteString=f.getFavorite(String.class);
        int favoriteInteger=f.getFavorite(Integer.class);
        Class<?> favoriteClass=f,getFavorite(Class.class);

        System.out.printf("%s %x %s%n",favoriteString,favoriteInteger,favoriteClass.getName());
        }
~~~

- [x] Favorites 인스턴스는 타입 안전함
- [x] String을 요청했는데 Integer를 반환하는 일이 절대 없음

~~~java
public class Favorites {
    private Map<Class<?>, Object> favorites = new HashMap<>();

    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Object.requireNonNull(type), instance);
    }

    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
}
~~~

- [x] cast 메서드는 형변환 연산자의 동적 버전
- [x] 이 메서드는 단순히 주어진 인수가 Class 객체가 알려주는 타입의 인스턴스인지를 검사한 다음, 맞다면 그 인수를 그대로 반환하고, 아니면 ClassCastException을 던짐

#### Favorite 클래스에는 두 가지 제약이 있음

- [x] 악의적인 클라이언트가 Class 객체를 (제네릭이 아닌) 로 타입으로 넘기면 Favorites 인스턴스의 타입 안전성이 쉽게 깨짐
    - [x] Favorites가 타입 불변식을 어기는 일이 없도록 보장하려면 putFavorite 메서드에서 인수로 주어진 instance의 타입이 type으로 명시한 타입과 같은지 확인하면 됨
~~~java
public <T> void putFavorites(Class<T> type, T instance) {
  favorites.put(Objects.requireNonNull(type), type.cast(instance));
}
~~~
- [x] checkedSet, checkedList, checkedMap 같은 메서드 모두 이 방식을 적용한 컬렉션 래퍼들
- [x] 실체화 불가 팅ㅂ에는 사용할 수 없음
  - [x] String, String[]은 저장할 수 있어도 즐쳐 찾는 List\<String>은 저장할 수 없음
  - [x] List\<String>을 저장하려는 코드는 컴파일되지 않음
#### 컬렉션 API로 대표되는 일반적인 제네릭 형태에서는 한 컨테이너가 다룰 수 잇는 타입 매개변수의 수가 고정되어 있음
#### 컨테이너 자체가 아닌 키를 타입 매개변수로 바꾸면 이런 제약이 없는 타입 안전 이종 컨테이너를 만들 수 있음
#### 타입 안전 이종 컨테이너는 Class를 키로 쓰며, 이런 식으로 쓰이는 CLass 객체를 타입 토큰이라고 함
