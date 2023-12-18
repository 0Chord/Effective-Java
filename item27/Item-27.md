# 비검사 경고를 제거하라
- [x] 컴파일러가 알려준 대로 수정하면 경고가 사라짐
- [x] 컴파일러가 알려준 타입 매개변수를 명시하지 않고, 자바 7부터 지원하는 다이아몬드 연산자(<>)만으로 해결할 수 있음
~~~java
Set<Lark> exaltation = new HashSet<>();
~~~
#### 할 수 있는 한 모든 비검사 경고를 제거하라
- [x] 모두 제거한다면 그 코드는 타입 안전성이 보장됨
- [x] 런타임에 ClassCastException이 발생할 일이 없고, 의도한 대로 잘 동작하리라 확신할 수 있음
#### 경로를 제거할 수는 없지만 타입이 안전하다고 확신할 수 있다면 @SuppressWarnings("unchecked") 애너테이션을 달아 경고를 숨기자
- [x] 타입 안전함을 검증하지 않은 채 경고를 숨기면 스스로에게 잘못된 보안 인식을 심어주는 꼴
- [x] @SuppressWarnings 애너테이션은 개별 지역변수 선언부터 클래스 전체까지 어떤 선언에도 달 수 있음
#### @SuppressWarnings 애너테이션은 항상 가능한 한 좁은 범위에 적용하기
- [x] 한 줄이 넘는 메서드나 생성자에 달린 @SuppressWarnings 애너테이션을 발견하면 지역변수 선언 쪽으로 옮기기
~~~java
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        return (T[]) Arrays.copyOf(elements, size, a.getClass());
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
~~~
- [x] 애너테이션은 선언에만 달 수 있기 때문에 return 문에는 @SuppressWarnings를 다는 게 불가능함
~~~java
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        // 생성한 배열과 매개변수로 받은 배열의 타입이 모두 T[]로 같으므로 올바른 형변환이다
        @SuppressWarnings("unchecked")
        T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
        return result;
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
~~~
#### @SuppressWarnings("unchecked") 애너테이션을 사용할 때면 그 경고를 무시해도 안전한 이유를 항상 주석으로 남겨야 함
- [x] 다른 사람이 그 코드를 이해하는 데 도움이 되며, 더 중요하게는, 다른 사람이 그 코드를 잘못 수정하여 타입 안전성을 잃는 상황을 줄여줌
#### 비검사 경고는 중요하니 무시하지 말자. 모든 비검사 경고는 런타임에 ClassCastException을 일으킬 수 있는 잠재적 가능성을 뜻하니 최선을 다해 제거하라
#### 경고를 없앨 방법은 찾지 못하겠다면, 그 코드가 타입 안전함을 증명하고 가능한 한 범위를 좁혀 @SuppressWarnings("unchecked") 애너테이션으로 경고를 숨겨라. 그런 다음 경고를 숨기기로 한 증거를 주석으로 남겨라
