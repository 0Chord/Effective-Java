# clone 재정의는 주의해서 진행하라

#### Cloneable은 복제해도 되는 클래스임을 명시하는 용도의 믹스인 인터페이스지만, 의도한 목적을 제대로 이루지 못했음

- [x] 가장 큰 문제는 clone 메서드가 선언된 곳이 Cloneable이 아닌 Object이고, protected라는 데 있음
- [x] Cloneable 인터페이스는 Object의 protected 메서드인 clone의 동작 방식을 결정
- [x] Cloneable을 구현한 클래스의 인스턴스에서 clone을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환하며, 그렇지 않은 클래스의 인스턴스에서 호출하면
  CloneNotSupportedException을 던짐
- [x] 생성자를 호출하지 않고도 객체를 생성할 수 있게 되는 것

#### 제대로 동작하는 clone 메서드를 가진 상위 클래스를 상속해 Cloneable을 구현하고자 한다면

- [x] super.clone() 호출 -> 얻은 객체는 원본의 완벽한 복제본
    - [x] 클래스에 정의된 모든 필드는 원본 필드와 똑같은 값
- [x] 모든 필드가 기본 타입이거나 불변 객체를 참조한다면 이 객체는 완벽히 우리가 원하는 상태라 더 손볼 것이 없음
- [x] 쓸데없는 복사를 지양한다는 관점에서 불변 클래스는 굳이 clone 메서드를 제공하지 않는게 좋음

~~~java
@Override public PhoneNumber clone(){
        try{
        return(PhoneNumber)super.clone();
        }catch(CloneNotSupportedException e){
        throw new AssertionError();
        }
        }
~~~

- [x] 메서드가 동작하게 하려면 PhoneNumber의 클래스 선언에 Cloneable을 구현한다고 추가해야 함
- [x] Object의 clone 메서드는 Object를 반환하지만 PhoneNumber의 clone 메서드는 PhoneNumber를 반환하게 함

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
        Object result = elements[--size];
        elements[size] = null;
        return result;
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
- [x] clone 메서드가 단순히 super.clone 결과를 그대로 반환한다면 반환된 Stack 인스턴스의 size 필드는 올바른 값을 갖겠지만, elements 필드는 원본 Stack 인스턴스와 똑같은 배열을 참조할 것이다
  - [x] 원본이나 복제본 중 하나를 수정하면 다른 하나도 수정되어 불변식을 해친다는 이야기
- [x] Stack 클래스의 하나뿐인 생성자를 호출한다면 이러한 상황은 절대 일어나지 않음
#### clone 메서드는 사실상 생성자와 같은 효과를 냄. clone은 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야 함
- [x] 스택 내부 정보를 복사해야 하는데, 가장 쉬운 방법은 elements 배열의 clone을 재귀적으로 호출해주는 것
~~~java
@Override public Stack clone() {
  try{
    Stack result = (Stack) super.clone();
    result.elements = elements.clone();
    return result;
  } catch (CloneNotSupportedException e) {
    throw new AssertionError();
  }
}
~~~
- [x] elements.clone의 결과를 Object[]로 형변환할 필요는 없음 -> 배열의 clone은 런타임 타입과 컴파일타임 타입 모두가 원본 배열과 똑같은 배열을 반환
  - [x] 배열을 복제할 때는 배열의 clone 메서드를 사용하라고 권장
  - [x] 배열은 clone 기능을 제대로 사용하는 유일한 예시
- [x] elements 필드가 final이었다면 앞서의 방식은 작동하지 않음
  - [x] final 필드에는 새로운 값을 할당할 수 없긴 때문
#### Cloneable 아키텍처는 '가변 객체를 참조하는 필드는 final로 선언하라'는 일반 용법과 충돌
~~~java
public class HashTable implements Cloneable {
  private Entry[] buckets = ...;
  
  private static class Entry {
    final Object key;
    Object value;
    Entry next;

    Entry(Object key, Object value, Entry next) {
      this.key = key;
      this.value = value;
      this.next = ntext;
    }
  }
}
~~~
~~~java
@Override public HashTable clone() {
  try {
    HashTable result = (HashTable) super.clone();
    result.buckets = buckets.clone();
    return result;
  } catch (CloneNotSupportedException e) {
    throw new AssertionError();
  }
}
~~~
- [x] 복제본은 자신만의 버킷 배열을 갖지만, 이 배열은 원본과 같은 연결 리스트를 참조하여 원본과 복제본 모두 예기치 않게 동작할 가능성이 생김
- [x] 각 버킷을 구성하는 연결 리스트를 복사해야 함
~~~java
public class HashTable implements Cloneable {
  private Entry[] buckets = ...;
  
  private static class Entry {
    final Object key;
    Object value;
    Entry next;

    Entry(Object key, Object value, Entry next) {
      this.key = key;
      this.value = value;
      this.next = ntext;
    }
    
    Entry deepCopy() {
        return new Entry(key, value, next == null ? null : next.deepCopy());
    }
  }
  
  @Override public HashTable clone() {
    try {
        HashTable result = (HashTable) super.clone();
        result.buckets = new Entry[buckets.length];
        for (int i=0; i<buckets.length; i++)
            if (buckets[i] != null)
                result.buckets[i] = buckets[i].deepCopy();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
  }   
}
~~~
- [x] 연결 리스트를 복제하는 방법은 재귀 호출 ㅂ때문에 리스트의 원소 수만큼 스택 프로에미을 소비하여, 리스트가 길면 스택 오버플로를 일으킬 위험이 있음
- [x] deepCopy를 재귀 호출 대신 반복자를 써서 순회하는 방향으로 수정해야 함
~~~java
Entry deepCopy() {
  Entry result = new Entry(key, value, next);
  for (Entry p = result; p.next != null; p = p.next)
    p.next = new Entry(p.next.key, p.next.value, p.next.next);
  return result;
}
~~~
#### 복잡한 가변 객체를 복제하는 마지막 방법
- [x] super.clone을 호출하여 얻은 객체의 모든 필드를 초기 상태로 설정 -> 원본 객체의 상태를 다시 생성하는 고수준 메서드들을 호출
- [x] HashTable 예에서라면, buckets 필드를 새로운 버킷 배열로 초기화한 다음 원본 테이블에 담긴 모든 키-값 쌍 각각에 대해 복제본 테이블의 put(key, value) 메서드를 호출해 둘의 내욜이 똑같게 해주면 됨
- [x] 느리고, Cloneable 아키텍처의 기초가 되는 필드 단위 객체 복사를 우회하기 때문에 전체 Cloneable 아키텍처와는 어울리지 않는 방식
#### 생성자에서는 재정의될 수 있는 메서드를 호출하지 않아야 하는데 clone 메서드도 마찬가지
- [x] clone이 하위 클래스에서 재정의한 메서드를 호출하면, 하위 클래스는 복제 과정에서 자신의 상태를 교정할 기회를 잃게 되어 원본과 복제본의 상태가 달라질 가능성이 큼
- [x] put(key, value) 메서드는 final이거나 private이어야 함
#### public인 clone 메서드에서는 throws 절을 없애야 함
- [x] 상속용 클래스는 Cloneable을 구현해서는 안됨
- [x] Cloneable을 구현한 스레드 안전 클래스를 작성할 때는 clone 메서드 역시 적절히 동기화해줘야 함
#### 요약하자면, Cloneable을 구현하는 모든 클래스는 clone을 재정의해야 함
- [x] 접근 제한자는 public으로, 반환 타입은 클래스 자신으로 변경
- [x] 이 메서드는 가장 먼저 super.clone()을 호출한 후 필요한 필드를 전부 적절히 수정한다
  - [x] 객체의 내부 '깊은 구조'에 숨어 있는 모든 가변 객체를 복사하고, 복제본이 가진 객체 참조 모두가 복사된 객체들을 가리키게 함을 뜻함
  - [x] 내부 복사는 주로 clone을 재귀적으로 호출해 구현하지만, 이 방식이 최선은 아님
- [x] 기본 타입 필드와 불변 객체 참조만 갖는 클래스라면 아무 필드도 수정할 필요가 없음
#### 복사 생성자와 복사 팩터리라는 더 나은 객체 복사 방식을 제공할 수 있음
### 기본 원칙은 '복제 기능은 생성자와 팩터리를 이용하는게 최고' 배열만은 clone 메서드 방삭이 가장 깔끔한, 이 규칙의 합당한 예외