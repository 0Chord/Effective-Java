# equals를 재정의하려거든 hashCode도 재정의하라
#### equals를 재정의한 클래스 모두에서 hashCode도 재정의해야 함
- [x] hashCode 일반 규약을 어기게 되어 해당 클래스의 인스턴스를 HashMap이나 HashSet 같은 컬렉션의 원소로 사용할 때 문제를 일으킴
- [x] 논리적으로 같은 객체는 같은 해시코드를 반환해야 함
- [x] equals는 물리적으로 다른 두 객체를 논리적으로 같다고 할 수 있지만, 기본 hashCode 메서드는 이 둘을 전혀 다르다고 판단하여 규약과 달리 서로 다른 값을 반환
~~~java
Map<PhoneNumber, String> m = new HashMap<>();
m.put( new PhoneNumber(707, 867, 5309), "제니");
~~~
- [x] m.get(new PhoneNumber(707 ,867, 5309))를 실행하면 "제니"가 나와야 할 것 같지만, 실제로는 null을 반환
  - [x] PhoneNumber class는 hashCode를 재정의하지 않았기 때문에 논리적 동치인 두 객체가 서로 다른 해시코드를 반환하여 두 번째 규약을 지키지 못함
- [x] PhoneNumber에 적절한 hashCode 메서드만 작성해주면 해결됨
### 좋은 HashCode를 작성하는 간단한 요령
1. int 변수 result를 선언한 후 값 c로 초기화 -> c는 해당 객체의 첫번째 핵심 필드를 단계 2.a 방식으로 계산한 해시코드 (핵심 필드란 equals 비교에 사용되는 필드를 뜻함)
2. 해당 객체의 나머지 핵심 필드 f 각각에 대해 작업을 수행  
    a. 해당 필드의 해시코드 c를 계산
   1. 기본 타입 필드라면, Type.hashCode(f)를 수행 -> Type은 해당 기본 타입의 박싱 클래스
   2. 참조 타입 필드면서 이 클래스의 equals 메서드가 이 필드의 equals를 재귀적으로 호출해 비교한다면, 이 필드의 hashCode를 재귀적으로 호출 -> 이 필드의 표준형을 만들어 그 표준형의 hashCode를 호출. 필드 값이 null이면 0을 사용
   3. 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룸. 이상의 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드를 계산한 다음, 단계 2.b 방식으로 갱신. 배열에 핵심 원소가 하나도 없다면 단순히 상수를 사용. 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용  
  
   b. 단계 2.a에서 계산한 해시코드 c로 result를 갱신 -> result = 31 * result + c;
3. result를 반환
    