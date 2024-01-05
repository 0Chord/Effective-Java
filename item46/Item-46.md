# 스트림에서는 부작용 없는 함수를 사용하라
- [x] 스트림은 그저 또 하나의 API가 아닌, 함수형 프로그래밍에 기초한 패러다임이기 때문
- [x] 스트림 패러다임의 핵심은 계싼을 일련의 변환으로 재구성하는 부분
- [x] 각 변환 단계는 가능한 한 이전 단계의 결과를 받아 처리하는 순수 함수이어야 함
- [x] 순수 함수란 오직 입력만이 결과에 영향을 주는 함수를 의미함
~~~java
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
    words.forEach(word -> {
        freq.merge(word.toLowerCase(), 1L, Long::sum);
    });
}
~~~
- [x] 스트림 코드를 가장한 반복적 코드
- [x] 스트림 API의 이점을 살리지 못하여 같은 기능의 반복적 코드보다 길고, 읽기 어렵고, 유지보수에도 좋지 않음
~~~java
Map<String, Long> freq;
try (Stream<String> words = new Scanner(file).tokens()) {
    freq = words
            .collect(groupingBy(String::toLowerCase, counting()));
}
~~~
#### forEach 연산은 스트림 계산 결과를 보고할 때만 사용하고, 계산하는 데는 사용하지 말자
- [x] 수집기를 사용하면 스트림의 원소를 손쉽게 컬렉션으로 모을 수 있음
- [x] toList(), toSet(), toCollection(collectionFactory)가 그 주인공
~~~java
List<String> topTen = freq.keySet().stream()
    .sorted(comparing(freq::get).reversed())
    .limited(10)
    .collect(toList())
~~~
- [x] 가장 간단한 맵 수집기는 toMap(keyMapper, valueMapper)로, 스트림 원소를 키에 매핑하는 함수와 값에 매핑하는 함수를 인수도 받음
~~~java
private static final Map<String, Operation> stringToEnum = Stream.of(values()).collect(
    toMap(Object::toString, e -> e));
~~~
- [x] 인수 3개를 받는 toMap은 어떤 키와 그 키에 연관된 원소들 중 하나를 골라 연관 짓는 맵을 만들 때 유용함
~~~java
toMap(keyMapper, valueMapper, (oldVal, newVal) -> newVal)
~~~
#### 스트림 파이프라인 프로그래밍의 핵심은 부작용 없는 함수 객체에 있음
#### 스트림뿐 아니라 스트림 관련 객체에 건네지는 모든 함수 객체가 부작용이 없어야 함
#### 종단 연산 중 forEach는 스트림이 수행한 계산 결과를 보고할 때만 이용해야 함
#### 가장 중요한 수집기 팩터리는 toList, toSet, toMap, groupingBy, joining임