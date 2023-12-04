# Comparable을 구현할지 고려하라
- [x] Comparable 인터페이스의 유일무이한 메서드인 compareTo는 Object의 메서드가 아님
- [x] 성격은 두 가지만 빼면 Object의 equals와 같음
- [x] compareTo는 단순 동치성 비교에 더해 순서까지 비교할 수 있으며 제네릭함
- [x] Comparable을 구현했다는 것은 그 클래스의 인스턴스들에는 자연적인 순서가 있음을 뜻함
- [x] Comparable을 구현한 객체들의 배열은 손쉽게 정렬할 수 있음
~~~java
Arrays.sort(a);
~~~
- [x] 검색, 극단값 계산, 자동 정렬되는 컬렉션 관리도 역시 쉽게 할 수 있음
#### 명령줄 인수들을 (중복은 제거하고) 알파벳순으로 출력
- [x] String이 Comparable을 구현한 덕분
~~~java
public class WordList {
    public static void main(String[] args) {
        Set<String> s = new TreeSet<>();
        Collections.addAll(s, args);
        System.out.println(s);
    }
}
~~~
- [x] Comparable을 구현하여 이 인터페이스를 활용하는 수많은 제네릭 알고리즘과 컬렉션의 힘을 누릴 수 있음
- [x] 자바 플랫폼 라이브러리의 모든 값 클래스와 열거 타입이 Comparable을 구현
- [x] 알파벳, 슷자, 연대 같이 순서가 명확한 값 ㅋ르래스를 작성한다면 반드시 Comparable 인터페이스를 구현
~~~java
public interface Comparable<T> {
    int compareTo(T t);
}
~~~
- [x] compareTo는 타입이 다른 객체를 신경쓰지 않아도 됨 -> 타입이 다른 객체가 주어지면 ClassCastException을 던져도 되며, 대부분 그렇게 함
#### compareTo 규약
1. 두 객체 참조의 순서를 바꿔 비교해도 예상한 결과가 나와야 함
   - [x] 첫 번째 객체가 두 번째 객체보다 작으면, 두 번째가 첫 번째보다 커야 함
2. 첫 번째가 두번째보다 크고 두 번째가 세 번째보다 크면, 첫 번째는 세 번째보다 커야 함
3. 크기가 같은 객체들끼리는 어던 객체와 비교하더라도 항상 같아야 함
#### 마지막 규약은 간단히 말하면 compareTo 메서드로 수행한 동치성 테스트의 결과가 equals와 같아야 한다는 것
~~~java
public final class CaseInsensitiveString implements Comparable<CaseInsentiveString> {
    public int compareTo(CaseInsensitiveString cis) {
        return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
    }
}
~~~
~~~java
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode);
    if (result == 0) {
        result = Short.compare(prefix, pn.prefix);
        if (result == 0)
            result = Short.compare(lineNum, pn.lineNum);
    }
    return result;
}
~~~
#### 비교자 생성 메서드를 활용한 비교자
~~~java
private static final Comparator<PhoneNumber> COMPARATOR = 
    comparingInt((PhoneNumber pn) -> pn.areaCode)
        .thenComparingInt(pn -> pn.prefix)
        .thenComparingInt(pn -> pn.lineNum);

public int compareTo(PhoneNumber pn) {
    return COMPARATOR.compare(this, pn);
}
~~~
#### 순서를 고려해야 하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현하여, 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야 함
#### compareTo 메서드에서 필드의 값을 비교할 때 <와 > 연산자는 쓰지 말아야 함
#### 그 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용