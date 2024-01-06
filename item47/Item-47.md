# 반환 타입으로는 스트림보다 컬렉션이 낫다
- [x] Stream 인터페이스는 Iterable 인터페이스가 정의한 추상 메서드를 전부 포함할 뿐만 아니라, Iterable 인터페이스가 정의한 방식대로 동작함
- [x] foreach로 스트림을 반복할 수 없는 까닭은 바로 Stream이 Iterable을 확장하지 않아서이다
~~~java
for (ProcessHandle ph : ProcessHandle.allProcesses()::iterator) {
    // 프로세스를 처리함
}
~~~
- [x] 컴파일 되지 않음
~~~java
for (ProcessHandle ph : (Iterable<ProcessHandle>) ProcessHandle.allProcesses()::iterator) {
    // 프로세스를 처리함
}
~~~
- [x] 작동은 하지만 실전에 쓰기에는 너무 난잡하고 직관성이 떨어짐

~~~java
public static <E> iterable<E> iterableOf(Stream<E> stream) {
    return stream::iterator;
}

for (ProcessHandle p : iterableOf(ProcessHandle.allProcesses())){
    // 프로세스를 처리함    
}
~~~
~~~java
public static <E> Stream<E> streamOf(Iterable<E> iterable) {
    return StreamSupport.stream(iterable.spliterator(), false);
}
~~~
- [x] 객체의 시퀀스를 반환하는 메서드를 작성하는데, 이 메서드가 오직 스트림 파이프라인에서만 쓰일 걸 안다면 마음 놓고 스트림을 반환하게 함
- [x] 반환된 객체들이 반복문에서만 쓰일 걸 안다면 Iterable을 반환
#### 원소 시퀀스를 반환하는 공개 API의 반환 타입에는 Collection이나 그 하위 타입을 쓰는 게 일반적으로 최선
#### 원소 시퀀스를 반환하는 메서드를 작성할 때는, 이를 스트림으로 처리하기를 원하는 사용자와 반복으로 처리하길 원하는 사용자가 모두 있을 수 있음을 떠올리고, 양쪽을 다 만족시키려 노력하자
