# finalizer와 cleaner 사용을 피하라
#### finalizer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요
- [x] 오동작, 낮은 성능, 이식성 문제의 원인이 되기도 함
#### cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고 일반적으로 불필요
- [x] finalizer와 cleaner는 즉시 수행된다는 보장이 없음
  - [x] 객체에 접근할 수 없게 된 후 finalizer와 cleaner가 실행되기까지 얼마나 걸릴지 알 수 없음
#### finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없음
- [x] finalizer와 cleaner를 얼마나 신속히 수행할지는 전적으로 가비지 컬렉터 알고리즘에 달림
- [x] 클래스에 finalizer를 달아두면 그 인스턴스의 자원 회수가 제멋대로 지연될 수 있음
  - [x] finalizer를 사용하지 않는 방법뿐임
- [x] cleaner는 자신이 수행할 스레드를 제어할 수 있다는 면에서 더 나음
  - [x] 백그라운드에서 수행되며 가비지 컬렉터의 통제하에 있으니 즉각 수행되리라는 보장은 없음
- [x] 자바 언어 명세는 finalizer와 cleaner의 수행 시점뿐 아니라 수행 여부조차 보장하지 않음
#### 프로그램 생애주기와 상관없는, 상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안됨
- [x] System.gc나 System.runFinalization 메서드에 현혹되지 말자
  - [x] finalizer와 cleaner가 실행될 가능성을 높여줄 수는 있지만, 보장해주지는 않는다
- [x] finalizer 동작 중 발생한 예외는 무시되며, 처리할 작업이 남았더라도 그 순간 종료됨
  - [x] 잡지 못한 예외 때문에 해당 객체는 자치 마무리가 덜 된 상태로 남을 수 있음
#### finalizer와 cleaner는 심각한 성능 문제도 동반한다
- [x] 객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 그렇지도 않음
- [x] final class들은 그 누구도 하위 클래스를 만들 수 없으니 이 공격에서 안전하다
#### final이 아닌 클래스를 finalizer 공격으로부터 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언하기
#### AutoCloseable을 구현하고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 됨
- [x] 각 인스턴스는 자신이 닫혔는지를 추적하는 것이 좋음
  - [x] close 메서드에서 이 객체는 더 이상 유효하지 않음을 필드에 기록하고, 다른 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 IllegalStateException을 던지는 것
~~~java
public class Room implements AutoCloseable{

    private static final Cleaner cleaner = Cleaner.create();
    
    private static class State implements Runnable{
        int numJunkPiles;

        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }

        @Override
        public void run() {
            System.out.println("방 청소");
            numJunkPiles = 0;
        }
    }

    private final State state;
    private final Cleaner.Cleanable cleanable;

    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }

    @Override
    public void close() throws Exception {
        cleanable.clean();
    }
}
~~~
- [x] numJunkPiles 필드는 네이티브 피어를 가리키는 포인터를 담은 final long 변수여야 한
- [x] State는 Runnable을 구현하고, 그 안의 run 메서드는 cleanable에 의해 딱 한 번만 호출될 것이다
#### State 인스턴스는 '절대로' Room 인스턴스를 참조해서는 안 된다
- [x] Room 인스턴스를 참조할 경우 순환참조가 생겨 가비지 컬렉터가 Room 인스턴스를 회수해갈 기회가 오지 않음
### cleaner는 안전망 역할이나 중요하지 않은 네이티브 자원 회수 용으로만 사용하자. 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다