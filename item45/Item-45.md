# 스트림은 주의해서 사용하라
- [x] 스트림 API는 다량의 데이터 처리 작업을 돕고자 자바 8에 추가됨
- [x] 스트림은 데이터 원소의 유한 혹은 무한 시퀀스를 뜻함
- [x] 스트림 파이프라인은 이 원소들로 수행하는 연산 단계를 표현하는 개념
- [x] 스트림의 원소들은 어디로부터든 올 수 있음
  - [x] 스트림 안의 데이터 원소들은 객체 참조나 기본 타입 값임
- [x] 스트림 파이프라인은 소스 스트림에서 시작해 종단 연산으로 끝나며, 그 사이에 하나 이상의 중간 연산이 있을 수 있음
- [x] 각 중간 연산은 스트림을 어떠한 방식으로 변환함
- [x] 중간 연산들은 모두 한 스트림을 다른 스트림으로 변환하는데, 변환된 스트림의 원소 타입은 변환 전 스트림의 원소 타입과 같을 수도 있고 다를 수도 있음
- [x] 종단 연산은 마지막 중간 연산이 내놓은 스트림에 최후의 연산을 가함
#### 스트림을 과용하면 프로그램이 읽거나 유지보수하기 어려워짐
#### 람다에서는 타입 이름을 자주 생략하므로 매개변수 이름을 잘 지어야 스트림 파이프라인의 가독성이 유지됨
#### 기존 코드는 스트림을 사용하도록 리팩터링하되, 새 코드가 더 나아 보일 때만 반영하자
- [x] 스트림 파이프라인은 되풀이되는 계산을 함수 객체로 표현함
#### 다음과 같은 상황에 스트림이 안성맞춤
- [x] 원소들의 시퀀스를 일관되게 변환한다
- [x] 원소들의 시퀀스를 필터링한다
- [x] 원소들의 시퀀스를 하나의 연산을 사용해 결합한다
- [x] 원소들의 시퀀스를 컬렉션에 모은다
- [x] 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는다
#### 스트림과 반복 중 어느 쪽이 나은지 확신하기 어렵다면 둘 다 해보고 더 나은 쪽을 택하라