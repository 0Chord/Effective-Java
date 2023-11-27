# 다 쓴 객체 참조를 해제하라
#### 자바의 가비지 컬렉터를 통해 메모리 관리에 신경을 쓰지 않아도 오해할 수 있는데, 사실이 아니다
~~~java
public class Stack {

    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    private Object[] elements;
    private int size = 0;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }

    /**
     * 원소를 위한 공간을 적어도 하나 이상 확보한다.
     * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
     */
    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
~~~
- [x] 스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다
  - [x] 프로그램에서 그 객체들을 더 이상 사용하지 않더라도 말이다
- [x] 스택이 그 객체들의 다 쓴 참조를 여전히 갖고 있기 때문이다
  - [x] 다 쓴 참조란 문자 그대로 앞으로 다시 쓰지 않을 참조를 뜻한다
  - [x] elements 배열의 '활성 영역' 밖의 참조들이 모두 여기에 해당됨
- [x] 해당 참조를 다 썼을 때 null 처리(참조 해제)하면 됨
  - [x] 스택 클래스에서 각 원소의 참조가 더 이상 필요 없어지는 시점은 스택에서 꺼내질 때
~~~java
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null;
        return result;
    }
~~~
- [x] 객체 참조를 null 처리하는 일은 예외적인 경우여야 함
  - [x] 코드를 필요 이상으로 지저분하게 만들 뿐임
- [x] 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것
### Stack처럼 자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 함
-[x] 원소를 다 사용한 즉시 그 원소가 참조한 객체들을 다 null 처리해줘야 함
#### 캐시 역시 메모리 누수를 일으키는 주범
- [x] 운 좋게 캐시 외부에서 키(key)를 참조하는 동안만 엔트리가 살아 있는 캐시가 필요한 상황이라면 WeakHashMap을 사용해 캐시를 만들기
  - [x] 다 쓴 엔트리는 그 즉시 자동으로 제거됨
- [x] ScheduledThreadPoolExecutor 같은 백그라운드 스레드를 활용하거나 캐시에 새 엔트리를 추가할 때 부수 작업으로 수행하는 방법
- [x] LinkedHashMap은 removeEldestEntry 메서드를 써서 처리함
#### 메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있음. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 함