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
  
- [x] 파생 필드와 equals 비교에 사용되지 않은 필드는 '반드시' 제외해야 함
- [x] 단계 2.b의 곱셉 31 * result는 필드를 곱하는 순서에 따라 result 값이 달라지게 한다
  - [x] 클래스에 비슷한 필드가 여러 개일 때 해시 효과를 크게 높여줌
~~~java
@Override public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
~~~
- [x] Objects 클래스는 임의의 개수만큼 객체를 받아 해시코드를 계산해주는 정적 메서드인 hash를 제공
  - [x] 이 메서드를 활용하면 앞서의 요령대로 구현한 코드와 비슷한 수준의 hashCode 함수를 단 한 줄로 작성할 수 있음 -> 속도는 더 느림
    - [x] 입력 인수를 담기 위한 배열이 만들어지고, 입력 중 기본 타입이 있다면 박싱과 언박싱도 해야하기 때문
    - [x] hash 메서드는 성능에 민감하지 않은 상황에서만 사용
~~~java
@Override public int hashCode() {
    return Objects.hash(lineNum, prefix, areaCode);
}
~~~
- [x] 클래스가 불변이고 해시코드를 계산하는 비용이 크다면, 매번 새로 계산하기 보다는 캐싱하는 방식을 고려해야 함
- [x] 이 타입의 객체가 주로 해시의 키로 사용될 것 같다면 인스턴스가 만들어질 때 해시코드를 계산해둬야 함
- [x] 해시의 키로 사용되지 않는 경우라면 hashCode가 처음 불릴 때 계산하는 계산하는 지연 초기화 전략 -> 필드를 지연 초기화하려면 그 클래스의 스레드를 안전하게 만들도록 신경써야 함
~~~java
private int hashCode;

@Override public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNum);
        hashCode = result;
    }
    return result;
}
~~~
#### 성능을 높인다고 해시코드를 계산할 때 핵심 필드를 생략해서는 안됨
#### hashCode가 반환하는 값의 생성 규칙을 API 사용자에게 자세히 공표하지 말자. 그래야 클라이언트가 이 값에 의지하지 않게 되고, 추후에 계산 방식을 바꿀 수도 있음
### equals를 재정의할 때는 hashCode도 반드시 재정의해야 함