# private 생성자나 열거 타입으로 싱글턴임을 보증하라
### 싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 지칭
#### 싱글턴의 전형적인 예시는 함수와 같은 무상태 객체나 설계상 유일해야 하는 시스템 컴포넌트를 들 수 있음
~~~java
public class Elvis{
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {...}

    public void leaveTheBuilding() {...}
}
~~~
- [x] private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한번만 호출됨
- [x] 권한이 있는 클라이언트는 리플렉션 API인 AccessibleObject.setAccessible을 사용해 private 생성자를 호출할 수 있음
  - [x] 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 됨
~~~java
public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {...}
    public static Elvis getInstance() { return INSTANCE; }

    public void leaveTheBuilding() {...}
}
~~~
- [x] API를 바꾸지 않아도 싱글턴이 아니게 변경할 수 있음
~~~java
    private Object readResolve(){
        //진짜 Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.
        return INSTANCE; 
    }
~~~
~~~java
public enum Elvis{
    INSTANCE;

    public void leaveTheBuilding() {...}
}
~~~
- [x] 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법
- [x] 만들려는 싱클턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없음