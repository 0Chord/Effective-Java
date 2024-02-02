# 예외는 진짜 예외 상황에만 사용하라
~~~java
try {
    int i = 0;
    while(true)
        range[i++].climb();
} catch (ArrayIndexOutOfBoundsException e) {
}
~~~
- [x] 전혀 직관적이지 않다는 사실 하나만으로도 코드를 이렇게 작성하면 안 되는 이유는 충분함
#### 예외는 오직 예외 상황에서만 써야 함. 절대로 일상적인 제어 흐름용으로 쓰여선 안 됨
- [x] 더 일반화해 이야기하면 표준적이고 쉽게 이해되는 관용구를 사용하고, 성능 개선을 목적으로 과하게 머리를 쓴 기법은 자제해야 함
#### 잘 설계된 API라면 클라이언트가 정상적인 제어 흐름에서 예외를 사용할 일이 없게 해야 함
#### 상태 검사 메서드 , 옵셔널, 특정 값 중 하나를 선택하는 지침
- [x] 외부 동기화 없이 여러 스레드가 동시에 접근할 수 있거나 외부 요인으로 상태가 변할 수 있다면 옵셔널이나 특정 값을 사용함
- [x] 성능이 중요한 상황에서 상태 검사 메서드가 상태 의존적 메서드의 작업 일부를 중복 수행한다면 옵셔널이나 특정 값을 선택함
- [x] 다른 모든 경우엔 상태 검사 메서드 방식이 조금 더 낫다고 할 수 있음
#### 예외는 예외 상황에서 쓸 의도로 설계되었음
#### 정상적인 제어 흐름에서 사용해서는 안 되며, 이를 프로그래머에게 강요하는 API를 만들어서도 안 됨