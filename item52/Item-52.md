# 다중정의는 신중히 사용하라
~~~java
public class CollectionClassifier {
    public static String classify(Set<?> s)  {
        return "집합";
    }

    public static String classify(List<?> lst) {
        return "리스트";
    }

    public static String classify(Collection<?> c) {
        return "그 외";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
            new HashSet<String>(),
            new ArrayList<BigInteger>(),
            new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c));
    }
}
~~~
- [x] "그 외"만 세 번 연달아 출력함
#### 다중정의된 세 classify 중 어느 메서드를 호출할지가 컴파일타임에 정해지기 때문
- [x] 컴파일타임에는 for 문 안의 c는 항상 Collection<?> 타입임
- [x] 런타임에는 타입이 매번 달라지지만, 호출할 메서드를 선택하는 데는 영향을 주지 못함
#### 재정의한 메서드는 동적으로 선택되고, 다중정의한 메서드는 정적으로 선택되기 때문
- [x] 다중정의된 메서드 사이에서는 객체의 런타임 타입은 전혀 중요치 않음
- [x] 선택은 컴파일타임에, 오직 매개변수의 컴파일타임 타입에 의해 이뤄짐
- [x] 프로그래머에게는 재정의가 정상적인 동작 방식이고, 다중정의가 예외적인 동작으로 보일 것
#### 다중정의가 혼동을 일으키는 상황을 피해야 함
#### 안전하고 보수적으로 가려면 매개변수 수가 같은 다중정의는 만들지 말자
- [x] 가변인수를 사용하는 메서드라면 다중정의를 아예 하지 말아야 함
#### 다중정의하는 대신 메서드 이름을 다르게 지어주는 길도 항상 열려 있음
- [x] 생성자는 이름을 다르게 지을 수 없으니 두 번째 생성자부터는 무조건 다중정의가 됨
- [x] 정적 팩터리라는 대안을 활용할 수 있는 경우가 많음
- [x] 생성자는 재정의할 수 없으니 다중정의와 재정의가 혼용될 걱정은 넣어둬도 됨
- [x] 여러 생성자가 같은 수의 매개변수를 받아야 하는 경우를 완전히 피해갈 수 없을 테니, 그럴 때를 대비해 안전 대책을 배워두면 도움이 될 것
#### 메서드를 다중정의할 때, 서로 다른 함수형 인터페이스라도 같은 위치의 인수로 받아서는 안 됨
#### 프로그래밍 언어가 다중정의를 허용한다고 해서 다중정의를 꼭 활용하란 뜻은 아님
#### 일반적으로 매개변수 수가 같을 때는 다중정의를 피하는 게 좋음
#### 생성자라면 이 조언을 따르기가 불가능할 수 있음
#### 그럴 때는 헷갈릴 만한 매개변수는 형변환하여 정확한 다중정의 메서드가 선택되도록 해야 함