# 최적화는 신중히 하라
- [x] 성능 때문에 견고한 구조를 희생하지 말자
- [x] 빠른 프로그램보다는 좋은 프로그램을 작성하라
- [x] 좋은 프로그램은 정보 은닉 원칙을 따르므로 개별 구성요소의 내부를 독립적으로 설계할 수 있음
#### 성능을 제한하는 설계를 피하라
- [x] 완성 후 변경하기가 가장 어려운 설계 요소는 바로 컴포넌트끼리, 혹은 외부 시스템과의 소통 방식
#### API를 설계할 때 성능에 주는 영향을 고려하라
- [x] public 타입을 가변으로 만들면, 즉 내부 데이터를 변경할 수 있게 만들면 불필요한 방어적 복사를 수없이 유발할 수 있음
- [x] 인터페이스가 있는데 굳이 구현 타입을 사용하는 것 역시 좋지 않음
#### 성능을 위해 API를 왜곡하는 건 매우 안좋은 생각
#### 각각의 최적화 시도 전후로 성능을 측정하라
- [x] 일반적으로 90%의 시간을 단 10%의 코드에서 사용한다는 사실을 기억하기
#### 빠른 프로그램을 작성하려 안달하지 말자
#### 시스템을 설계할 때, 특히 API, 네트워크 프로토콜, 영구 저장용 데이터 포맷을 설계할 때는 성능을 염두에 두어야 함
#### 시스템 구현을 완료했다면 이제 성능을 측정하기