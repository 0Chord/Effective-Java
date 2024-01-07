# 스트림 병렬화는 주의해서 적용하라
- [x] parallel 메서드만 한 번 호출하면 파이프라인을 병렬 실행할 수 있는 스트림을 지원함
- [x] 동시성 프로그래밍을 할 때는 안전성과 응답 가능 상태를 유지하기 위해 애써야 하는데, 병렬 스트림 파이프라인 프로그래밍에서도 다를 바 없음
~~~java
public static void main(String[] args) {
    primes().map(p -> TWO.pow(p.intValueExact()).subtract(ONE))
        .filter(mersenne -> mersenne.isProbablePrime(50))
        .limit(20)
        .forEach(System.out::println);
}

static Stream<BigInteger> primes() {
    return Stream.iterable(TWO, BigInteger::nextProbablePrime);
}
~~~
- [x] 속도를 높이고 싶어 스트림 파이프라인의 parallel()을 호출하면 성능이 오를까?
- [x] 데이터 소스가 Stream.iterate거나 중간 연산으로 limit을 쓰면 파이프라인 병렬화로는 성능 개선을 기대할 수 없다
#### 스트림의 소스가 ArrayList, HashMap, HashSet, ConcurrentHaspMap의 인스턴스거나 배열, int 범위, long 범위일 때 병렬화의 효과가 가장 좋음
- [x] 이 자료구조들은 모두 데이터를 원하는 크기로 정확하고 손쉽게 나눌 수 있어서 일을 다수의 스레드에 분배하기에 좋다는 특징이 있음
#### 스트림을 잘못 병렬화하면 (응답 불가를 포함해) 성능이 나빠질 뿐만 아니라 결과 자체가 잘못되거나 예상 못한 동작이 발생할 수 있음
#### 조건이 잘 갖춰지면 parallel 메서드 호출 하나로 거의 프로세서 코어 수에 비례하는 성능 향상을 만끽할 수 있음
~~~java
static long pi(long n) {
    return LongStream.rangeClosed(2, n)
        .mapToObj(BigInteger::valueOf)
        .filter(i -> i.isProbablePrime(50))
        .count();
}
~~~
- [x] n보다 작거나 같은 소수의 개수를 계산하는 함수
- [x] 성능이 좋지 않음
~~~java
static long pi(long n) {
    return LongStream.rangeClosed(2, n) 
        .parallel()
        .mapToObj(BigInteger::valueOf)
        .filter(i -> i.isProbablePrime(50))
        .count();
}
~~~
#### 계산도 올바로 수행하고 성능도 빨리질 거라는 확신 없이는 스트림 파이프라인 병렬화는 시도조차 하지 말라
#### 스트림을 잘못 병렬화하면 프로그램을 오동작하게 하거나 성능을 급격히 떨어뜨림
#### 병렬화하는 편이 낫다고 믿더라도, 수정 후의 코드가 여전히 정확한지 확인하고 운영 환경과 유사한 조건에서 수행해보며 성능지표를 유심히 관찰