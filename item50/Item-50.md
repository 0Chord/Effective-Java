# 적시에 방어적 복사본을 만들라

#### 클라이언트가 불변식을 깨뜨리려 혈안이 되어 있다고 가정하고 방어적으로 프로그래밍해야함

~~~java
public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0) {
            throw new IllegalArgumentException(start + "가" + end + "보다 늦다.");
        }
        this.start = start;
        this.end = end;
    }

    public Date start() {
        return start;
    }

    public Date end() {
        return end;
    }

    ... //나머지 코드 생략
}
~~~

- [x] 클래스는 불변처럼 보이고, 시작 시각이 종료 시각보다 늦을 수 없다는 불변식이 무리 없이 지켜질 것 같지만, Date가 가변이라는 사실을 이용하면 어렵지 않게 불변식을 깨뜨릴 수 있음

~~~java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
end.setYear(78);
~~~
#### Date는 낡은 API이니 새로운 코드를 작성할 때는 더 이상 사용하면 안 됨
#### 외부 공격으로부터 Period 인스턴스의 내부를 보호하려면 생성자에서 받은 가변 매개변수 각각을 방어적으로 복사해야 함
~~~java
public Period(Date start, Date end) {
    this.start = new Date(start.getTime());
    this.end = new Date(end.getTime());

    if(this.start.compareTo(this.end) > 0){
        throw new IllegalArgumentException(
            this.start = "가 "+this.end + "보다 늦다.");
        )
}
~~~
- [x] 새로 작성한 생성자를 사용하면 앞서의 공격은 더 이상 Period에 위협이 되지 않음
#### 매개변수의 유효성을 검사하기 전에 방어적 복사본을 만들고, 이 복사본으로 유효성을 검사한 점에 주목하기
#### 매개변수가 제3자에 의해 확장될 수 있는 타입이라면 방어적 복사본을 만들 때 clone을 사용해서는 안 됨
- [x] 생성자를 수정하면 앞서의 공격은 막아낼 수 있지만, Period 인스턴스ㅇ는 아직도 변경 가능함
~~~java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
p.end.setYear(78); //p의 내부를 변경했다!
~~~
#### 두 번째 공격을 막아내려면 단순히 접근자가 가변 필드의 방어적 복사본을 반환하면 됨
~~~java
public Date start() {
    return new Date(start.getTime());
}

public Date end(){
    return new Date(end.getTime());
}
~~~
- [x] 새로운 접근자까지 갖추면 Period는 완전한 불변으로 거듭남
#### 클래스가 불변이든 가변이든, 가변인 내부 객체를 클라이언트에 반환할 때는 반드시 심사숙고해야 함
#### 길이가 1 이상인 배열은 무조건 가변임을 잊지 말자
#### 클래스가 클라이언트로부터 받는 혹은 클라이언트로 반환하는 구성요소가 가변이라면 그 요소는 반드시 방어적으로 복사해야 함
#### 복사 비용이 너무 크거나 클라이언트가 그 요소를 잘못 수정할 일이 없음을 신뢰한다면 방어적 복사를 수행하는 대신 해당 구성요소를 수정했을 때의 책임이 클라이언트에 있음을 문서에 명시하도록 하자