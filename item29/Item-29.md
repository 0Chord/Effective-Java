# 이왕이면 제네릭 타입으로 만들라
- [x] JDK가 제공하는 제네릭 타입과 메서드를 사용하는 일은 일반적으로 쉬운 편이지만, 제네릭 타입을 새로 만드는 일은 조금 더 어려움
~~~java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = hull;
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
~~~
- [x] 일반 클래스를 제네릭 클래스로 만드는 첫 단계는 클래스 선언에 타입 매개변수를 추가하는 일
  - [x] 스택이 담을 원소를 타입 하나만 추가하면 됨
  - [x] 타입 이름으로는 보통 E를 사용함
~~~java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new E[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public E pop() {
        if (size == 0)
            throw new EmptyStackException();
        E result = elements[--size];
        elements[size] = hull;
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
~~~
- [x] E와 같은 실체화 불가 타입으로는 배열을 만들 수 없음
- [x] 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하는 방법
~~~java
    (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
~~~
~~~java
@SuppressWarnings("unchecked")
public Stack() {
    elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
}
~~~
- [x] Stack은 깔끔히 컴파이로디고, 명시적으로 형변환하지 않아도 ClassCastException 걱정 없이 사용할 수 있게 됨
#### elements 필드의 타입을 E[]에서 Object[]로 바꾸는 법
#### 클라이언트에서 직접 형변환해야 하는 타입보다 제네릭 타입이 더 안전하고 쓰기 편함
#### 새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하라
#### 기존 타입 중 제네릭이었어야 하는 게 있다면 제네릭 타입으로 변경
#### 기존 클라이언트에는 아무 영향을 주지 않으면서, 새로운 사용자를 훨씬 편하게 해주는 길