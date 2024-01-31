# 표준 예외를 사용하라
- [x] 예외도 재사용하는 것이 좋으며, 자바 라이브러리는 대부분 API에서 쓰기에 충분한 수의 예외를 제공함
- [x] 가장 많이 재사용되는 예외는 IllegalArgumentException
- [x] 호출자가 인수로 부적절한 값을 넘길 때 던지는 예외로, 예를 들어 반복 횟수를 지정하는 매개변수에 음수를 건넬 때 쓸 수 있음
- [x] IllegalStateException도 자주 재사용됨 
  - [x] 대상 객체의 상태가 호출된 메서드를 수행하기에 적합하지 않을 때 주로 던짐
- [x] 메서드가 던지는 모든 예외를 잘못된 인수나 상태라고 뭉뚱그릴 수도 있겠지만, 그중 특수한 일부는 따로 구분해 쓰는 게 보통임
- [x] null 값을 허용하지 않는 메서드에 null을 건네면 관례상 IllegalArgumentException이 아닌 NullPointerException을 던짐
- [x] 어떤 시퀀스의 허용 범위를 넘는 값을 건넬 때도 IllegalArgumentException보다는 IndexOutOfBoundsException을 던짐
- [x] ConcurrentModificationException은 단일 스레드에서 사용하려고 설계한 객체를 여러 스레드가 동시에 수정하려 할 때 던짐
#### Exception, RuntimeException, Throwable, Error는 직접 재사용하지 말자
- [x] 이 클래스들은 추상 클래스라고 생각하길 바람
#### 인수 값이 무엇이었든 어차피 실패했을 거라면 IllegalStateException을, 그렇지 않으면 IllegalArgumentException을 던지자