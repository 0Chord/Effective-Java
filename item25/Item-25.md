# 톱레벨 클래스는 한 파일에 하나만 담으라
- [x] 소스 파일 하나에 톱레벨 클래스를 여러 개 선언하더라도 자바 컴파일러는 불평하지 않음
  - [x] 아무런 득이 없고 심각한 위험을 감수해야 하는 행위
~~~java
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}
~~~
- [x] Utensil과 Dessert 클래스가 한 파일에 정의되어 있다고 가정
~~~java
class Utensil {
    static final String NAME = "pan";
}
class Dessert {
    static final String NAME = "cake";
}
~~~
- [x] Main 실행 시 pancake를 출력
- [x] 똑같은 두 클래스를 담은 파일을 만들었다고 가정
~~~java
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}
~~~
#### 컴파일러에 어느 소스 파일을 먼저 건네느냐에 따라 동작이 달라지므로 반드시 바로 잡아야 할 문제
- [x] 해결책은 단순하게 톱레벨 클래스들을 서로 다른 소스 파일로 분리하면 그만
- [x] 여러 톱레벨 클래스를 한 파일에 담고 싶다면 정적 멤버 클래스를 사용하는 방법을 고민해볼 수 있음
- [x] 읽기도 좋고, private으로 선언하면 접근 범위도 최소로 관리할 수 있기 때문
~~~java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }

    private static class Utensil {
        static final String NAME = "pan";
    }

    private static class Dessert {
        static final String NAME = "cake";
    }
}
~~~
#### 소스 파일 하나에는 반드시 톱레벨 클래스를 하나만 담기
#### 컴파일러가 한 클래스에 대한 정의를 여러 개 만들어내는 일은 사라짐
