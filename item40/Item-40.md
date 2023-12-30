# @Override 애너테이션을 일관되게 사용하라
- [x] 자바가 기본으로 제공하는 에너테이션 중 보통의 프로그래머에게 가장 중요한 것은 @Override일 것
- [x] @Override는 메서드 선언에만 달 수 있으며, 이 애너테이션이 달렸다는 것은 상위 타입의 메서드를 재정의했음을 뜻함
~~~java
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first = first;
        this.second = second;
    }

    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++) {
            for (char ch = 'a'; ch <= 'z'; ch++) {
                s.add(new Bigram(ch, ch));
            }
            System.out.println(s.size());
        }
    }
}
~~~
- [x] 똑같은 소문자 2개로 구성된 바이그램 26개를 10번 반복해 집합에 추가한 다음, 그 집합의 크기를 출력함
- [x] Set은 중복을 허용하지 않으니 26이 출력될 것 같지만, 실제로는 260이 출력됨
~~~java
@Override public boolean equals(Bigram b) {
    return b.first == first && b.second == second;
}
~~~
~~~java
@Override public boolean equals(Object o) {
    if (!(o instanceof Bigram)){
        return false;
    }
    Bigram b = (Bigram) o;
    return b.first == first && b.second == second;
}
~~~
#### 상위 클래스의 메서드를 재정의하려는 모든 메서드에 @Override 애너테이션을 달자
- [x] 구체 클래스에서 상위 클래스의 추상 메서드를 재정의할 때는 굳이 @Override를 달지 않아도 됨
#### 재정의한 모든 메서드에 @Override 애너테이션을 의식적으로 달면 실수했을 때 컴파일러가 바로 알려줌
#### 구체 클래스에서 상위 클래스의 추상 메서드를 재정의한 경우엔 이 애너테이션을 달지 않아도 됨