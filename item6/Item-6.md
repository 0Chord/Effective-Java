# 불필요한 객체 생성을 피하라

#### 똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많다

### 하지 말아야 할 극단적인 예시

~~~java
String s = new String("bikini");
~~~

- [x] 실행될 때마다 String 인스턴스를 새로 만듬
- [x] 빈번히 호출되는 메서드 안에 있다면 String 인스턴스가 수백만 개 만들어질 수 있음

### 개선된 버전

~~~java
String s = "bikini";
~~~

- [x] 하나의 String 인스턴스를 사용

#### 생성자 대신 정적 팩터리 메서드를 제공하는 불변 클래스에서는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있음

~~~java
Boolean(String) -> Boolean.valueOf(String);
~~~

- [x] 생성자는 호출할 때마다 새로운 객체를 만들지만, 팩터리 메서드는 전혀 그렇지 않음

### 주어진 문자열이 유효한 로마 숫자인지를 확인하는 메서드

~~~ java
static boolean isRomanNumberal (String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + 
        "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
~~~

- [x] String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않음
- [x] 정규표현식용 Pattern 인스턴스는, 한 번 쓰고 버려져서 곧바로 가비지 컬렉션 대상이 됨
- [x] Pattern은 입력받은 정규표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 높음
- [x] 성능을 개선하려면 피룡한 정규표현식을 표현하는 Pattern 인스턴스를 클래스 초기화 과정에서 직접 생성해 캐싱해두고, 나중에 메서드가 호출될 때마다 이 인스턴스를 재사용한다
### 개선 예시
~~~java
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile(
        "^(?=.)M*(C[MD]|D?C{0,3})" + 
        "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
~~~
- [x] 객체가 불변이라면 재사용해도 안전함이 명백하다
#### 불필요한 객체를 만들어내는 또 다른 예로 오토박싱
- [x] 프로그래머가 기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 상호 변환해주는 기술
- [x] 오토박싱은 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만, 완전히 없애주는 것은 아님
~~~java
        long startTime = System.currentTimeMillis();
        Long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++) {
            sum += i;
        }
        long endTime = System.currentTimeMillis();
        long diffTimeSec = (endTime - startTime);
~~~
- [x] sum 변수를 long이 아닌 Long으로 선언해서 불필요한 Long 인스턴스가 약 2^31개가 만들어졌다
- [x] 단순히 Long -> long으로 바꿔주면 성능 향상이 월등히 이뤄진다
#### 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의
- [x] 이번 아이템을 "객체 생성은 비싸니 피해야 한다"로 오해하면 안됨
- [x] 프로그래밍의 명확성, 간결성, 기능을 위해서 객체를 추가로 생성하는 것이라면 일반적으로 좋은 일
- [x] 아주 무거운 객체가 아닌 단순히 객체 생성을 피하고자 객체 풀을 만들지는 말기
#### 방어적 복사가 필요한 상황에서 객체를 재사용했을 때의 피해가, 필요 없는 객체를 반복 생성했을 때의 피해보다 훨씬 크다는 사실을 기억
- [x] 방어적 복사에 실패하면 언제 터져 나올지 모르는 버그와 보안 구멍으로 이어지지만, 불필요한 객체 생성은 그저 코드 형태와 성능에만 영향을 줌