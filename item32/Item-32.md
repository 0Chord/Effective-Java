# 제네릭과 가변인수를 함께 쓸 때는 신중하라

- [x] 가변인수는 메서드에 넘기는 인수의 개수를 클라이언트가 조절할 수 있게 해주는데, 구현 방식에 허점이 있음
    - [x] 가변인수 메서드를 호출하면 가변인수를 담기 위한 배열이 자동으로 하나 만들어짐

~~~java
static void dangerous(List<String> ...stringLists){
        List<Integer> intList=List.of(42);
        Object[]objects=stringLists;
        objects[0]=intList; //힙오염 발생
        String s=stringLists[0].get(0); //ClassCastException
        }
~~~

#### 제네릭 varargs 배열 매개변수에 값을 저장하는 것은 안전하지 않음

~~~java
Arrays.asList(T...a)
        Collections.addAll(Collection<? super T>c,T...elements)
        EnumSet.of(E first,E...rest)
~~~

- [x] 타입 안전한 메서드

#### @SafeVarargs 애너테이션은 메서드 작성자가 그 메서드가 타입 안전함을 보장하는 장치

- [x] 컴파일러는 이 약속을 믿고 그 메서드가 안전하지 않을 수 잇다는 경고를 더 이상 하지 않음
- [x] 메서드가 안전한 게 확실하지 않다면 절대 @SafeVarargs 애너테이션을 달아서는 안 됨
- [x] 가변 인수 메서드를 호출할 때 varargs 매개변수를 담는 제네릭 배열이 만들어 짐
    - [x] 메서드가 이 배열에 아무것도 저장하지 않고 그 배열의 참조가 밖으로 노출되지 않는다면 타입 안전함
- [x] varargs 매개변수 배열이 호출자로부터 그 메서드로 순수하게 인수들을 전달하는 일만 한다면 그 메서드는 안전함
- [x] varargs 매개변수 배열에 아무것도 저장하지 않고도 타입 안전성을 깰 수도 있으니 주의해야 함

~~~java
static<T> T[]toArray(T...args){
        return args;
        }
~~~

- [x] 이 메서드가 반환하는 배열의 타입은 이 메서드에 인수를 넘기는 컴파일타임에 결정되는데, 그 시점에는 컴파일러에게 충분한 정보가 주어지지 않아 타입을 잘못 판단할 수 있음
- [x] 자신의 varargs 매개변수 배열을 그대로 반환하면 힙 오염을 이 메서드를 호출한 쪽의 콜스택으로까지 전이하는 결과를 낳을 수 있음

~~~java
static<T> T[]pickTwo(T a,T b,T c){
        switch(ThreadLocalRandom.current().nextInt(3)){
        case 0:return toArray(a,b);
        case 1:return toArray(a,c);
        case 2:return toArray(b,c);
        }
        throw new AssertionError(); //도달할 수 없음
        }
~~~

- [x] 이 메서드를 본 컴파일러는 toArray에 넘길 T 인스턴스 2개를 담을 varargs 매개변수 배열을 만드는 코드를 생성함
- [x] 이 코드가 만드는 배열의 타입은 Object[]인데, pickTwo에 어떤 타입의 객체를 넘기더라도 담을 수 잇는 가장 구체적인 타입이기 떄문

~~~java
public static void main(String[]args){
        String[]attributes=pickTwo("좋은","빠른","저렴한");
        }
~~~

#### 제네릭 varargs 매개변수 배열에 다른 메서드가 접근하도록 허용하면 안전하지 않다는 점을 다시 한번 상기

#### @SafeVarargs 애너테이션을 사용해야 할 때를 정하는 규칙은 간단

- [x] 제네릭이나 매개변수화 타입의 varargs 매개변수를 받는 모든 메서드에 @SafeVarargs를 달음

#### varargs 매개변수 배열에 아무것도 저장하지 않는다

#### 그 배열(혹은 복제본)을 신뢰할 수 없는 코드에 노출하지 않는다

~~~java
static<T> T[]pickTwo(T a,T b,T c){
        switch(ThreadLocalRandom.current().nextInt(3)){
        case 0:return List.of(a,b);
        case 1:return List.of(a,c);
        case 2:return List.of(b,c);
        }
        throw new AssertionError(); //도달할 수 없음
        }
~~~

~~~java
public static void main(String[]args){
        List<String> attributes=pickTwo("좋은","빠른","저렴한");
        }
~~~

- [x] 결과 코드는 배열 없이 제네릭만 사용하므로 타입 안전함

#### 가변인수와 제네릭은 궁합이 좋지 않음. 가변인수의 기능은 배열을 노출하여 추상화가 완벽하지 못하고, 배열과 제네릭의 타입 규칙이 서로 다르기 때문

#### 제네릭 varargs 매개변수는 타입 안전하지 않지만, 허용됨

#### 메서드에 제네릭 varargs 매개변수를 사용하고자 한다면, 먼저 그 메서드가 타입 안전한지 확인한 다음 @SafeVarargs 애너테이션을 달아 사용하는 데 불편함이 없게끔 하자!!
