# 명명 패턴보다 애너테이션을 사용하라
#### 명명 패턴의 단점
- [x] 오타가 나면 안됨
  - [x] testSafety 대신 tsetSafety로 명명시 개발자는 이 테스트가 통과했다고 오해할 수 있음
- [x] 올바른 프로그램 요소에서만 사용되리라 보증할 방법이 없음
- [x] 프로그램 요소를 매개변수로 전달할 마땅한 방법이 없다는 것
#### 애너테이션은 이 모든 문제를 해결해주는 멋진 개념
~~~java
import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
}
~~~
- [x] 애너테이션 선언에 다는 애너테이션을 메타애너테이션이라고 함
- [x] @Retention(RetentionPolicy.RUNTIME) 메타애너테이션은 @Test가 런타임에도 유지되어야 한다는 표시
  - [x] 이 메타애너테이션을 생략하면 테스트 도구는 @Test를 인식할 수 없음
- [x] @Target(ElementType.METHOD) 메타애너테이션은 @Test가 반드시 메서드 선언에서만 사용돼야 한다고 알려줌
  - [x] 클래스 선언, 필드 선언 등 다른 프로그램 요소에는 달 수 없음
~~~java
public class Sample {
    @Test
    public static void m1() {
        
    }

    public static void m2() {
        
    }

    @Test
    public static void m3() {
        throw new RuntimeException("실패");
    }

    public static void m4() {
        
    }

    @Test
    public void m5() {
        //잘못 사용한 예 : 정적 메서드가 아니다
    }

    public static void m6() {
        
    }

    @Test
    public static void m7() {
        throw new RuntimeException("실패");
    }

    public static void m8() {
        
    }
}
~~~
#### 매개변수를 하나를 받는 애너테이션 타입
~~~java
import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ExceptionTest {

    Class<? extends Throwable> value();
}
~~~
- [x] Class<? extends Throwable>은 Throwable을 확장한 클래스의 Class 객체라는 뜻며, 모든 예외와 오류 타입을 다 수용함
~~~java
public class Sample2 {

    @ExceptionTest(ArithmeticException.class)
    public static void m1() {
        int i = 0;
        i = i / i;
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m2() {
        int[] a = new int[0];
        int i = a[1];
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m3() {
        
    }
}
~~~
#### 배열 매개변수를 받는 애너테이션 타입
~~~java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ExceptionTest {

    Class<? extends Throwable>[] value();
}
~~~
~~~java
    @ExceptionTest({IndexOutOfBoundsException.class, NullPointerException.class})
    public static void doublyBad() {
        List<String> list = new ArrayList<>();
        list.addAll(5, null);
    }
~~~
- [x] 자바 8에서는 여러 개의 값을 받는 애너테이션을 다른 방식으로도 만들 수 있음
- [x] 배열 매개변수를 사용하는 대신 애너테이션에 @Repeatable 메타애너테이션을 다는 방식
- [x] @Repeatable을 단 애너테이션은 하나의 프로그램 요소에 여러 번 달 수 있음
- [x] @Repeatable을 단 애너테이션을 반환하는 '컨테이너 애너테이션'을 하나 더 정의하고, @Repeatable에 이 컨테이너 애너테이션의 class 객체를 매개변수로 전달해야 함
- [x] 컨테이너 애너테이션은 내부 애너테이션 타입의 배열을 반환하는 value 메서드를 정의해야 함
- [x] 컨테이너 애너테이션 타입에는 적절한 보존 정책(@Retention)과 적용 대상(@Target)을 명시해야 함
~~~java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Repeatable(ExceptionTestContainer.class)
public @interface ExceptionTest {

    Class<? extends Throwable> value();
}

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ExceptionTestContainer {
    ExceptionTest[] value();
}
~~~
~~~java
    @ExceptionTest(IndexOutOfBoundsException.class)
    @ExceptionTest(NullPointerException.class)
    public static void doublyBad() {
        List<String> list = new ArrayList<>();
        list.addAll(5, null);
    }
~~~
#### 애너테이션으로 할 수 있는 일을 명명 패턴으로 처리할 이유는 없음
#### 자바 프로그래머라면 예외 없이 자바가 제공하는 애너테이션 타입들은 사용해야 함