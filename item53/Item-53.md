# 가변인수는 신중히 사용하라
#### 가변인수 메서드는 명시한 타입의 인수를 0개 이상 받을 수 있음
- [x] 가변인수 메서드는 명시한 타입의 인수를 0개 이상 받을 수 있음
- [x] 가변인수 메서드를 호출하면, 가장 먼저 인수의 개수와 길이가 같은 배열을 만들고 인수들을 이 배열에 저장하여 가변인수 메서드에 건네줌
~~~java
static int sum(int... args) {
    int sum = 0;
    for ( int arg : args)
        sum += args;
    return sum;
}
~~~
- [x] 인수가 1개 이상이어야 할 때도 있음
- [x] 최솟값을 찾는 메서드인데 인수를 0개만 받을 수도 있도록 설계하는 건 좋지 않음
~~~java
static int min(int... args) {
    if (args.length == 0)
        throw new IllegalArgumentException("인수가 1개 이상 필요합니다.");
    int min = args[0];
    for (int i=1; i<args.length; i++)
        if (args[i] < min)
            min = args[i];
    return min;
}
~~~
- [x] 가장 심각한 문제는 인수를 0개만 넣어 호출하면 (컴파일타임이 아닌) 런타임에 실패한다는 점
- [x] args 유효성 검사를 명시적으로 해야 하고, min의 초기값은 Integer.MAX_VALUE로 설정하지 않고는 (더 명료한) for-each 문도 사용할 수 없음
- [x] 평범한 매개변수를 받고, 가변인수는 두 번째로 받으면 앞서의 문제가 말끔히 사라짐
~~~java
static int min(int firstArg, int... remainingArgs) {
    int min = firstArg;
    for (int arg : remainingArgs)
        if (arg < min)
            min = arg;
    return min;
}
~~~
- [x] 가변인수는 인수 개수가 정해지지 않았을 때 아주 유용함
- [x] 가변인수 메서드는 호출될 때마다 배열을 새로 하나 할당하고 초기화함
#### 가변인수의 유연성이 필요할 때 선택할 수 있는 멋진 패턴
~~~java
public void foo() { }
public void foo(int a1) { }
public void foo(int a1, int a2) { }
public void foo(int a1, int a2, int a3) { }
public void foo(int a1, int a2, int a3, int... rest) { }
~~~
#### 성능 최적화와 마찬가지로 이 기법도 보통 때는 별 이득이 없지만, 꼭 필요한 특수 상황에서는 사막의 오아시스가 되어줄 것
#### EnumSet의 정적 팩터리도 이 기법을 사용해 열거 타입 집합 생성 비용을 최소화함
#### EnumSet의 비트 필드를 대체하면서 성능까지 유지해야 하므로 아주 적절하게 활용한 예라 할 수 있음
### 정리
#### 인수 개수가 일정하지 않은 메서드를 정의해야 한다면 가변인수가 반드시 필요함
#### 메서드를 정의할 때 필수 매개변수는 가변인수 앞에 두고, 가변인수를 사용할 때는 성능 문제까지 고려하기