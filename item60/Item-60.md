# 정확한 답이 필요하다면 float와 double은 피하라
- [x] float와 double 타입은 과학과 공학 계산용으로 설계됨
- [x] 넓은 범위의 수를 빠르게 정밀한 근사치로 계산하도록 세심하게 설계되었음
#### float와 double 타입은 특히 금융 관련 계산과는 맞지 않음
- [x] 1달러에서 10센트짜리 사탕 9개를 샀다고 가정하면 0.1이 출력되어야 하지만 0.999999999998이 출력된다
~~~java
public static void main(String[] args) {
    double funds = 1.00;
    int itemsBought = 0;
    for (double price = 0.10; funds >= price; price += 0.10) {
        funds -= price;
        itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러):" + funds);
}
~~~
#### 금융 계산에는 BigDecimal, int 혹은 long을 사용해야 함
~~~java
public static void main(String[] args) {
    final BigDecimal TEN_CENTS = new BigDecimal(".10");
    
    int itemsBought = 0;
    BigDecimal funds = new BigDecimal("1.00");
    for (double price = 0.10; funds >= price; price += 0.10) {
        funds -= price;
        itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러):" + funds);
}
~~~
- [x] double을 BigDecimal로 교체함
- [x] 계산시 부정확한 값이 사용되는 걸 막기 위해 생성자 중 문자열을 받는 생성자를 사용함
#### BigDecimal의 단점
- [x] 기본 타입보다 쓰기가 훨씬 불편하고, 훨씬 느림
- [x] 단발성 계산이라면 느리다는 문제는 무시할 수 있지만, 쓰기 불편하다는 점은 못내 아쉬움
#### 정확한 답이 필요한 계산에는 float나 double을 피하라
#### 소수점 추적은 시스템에 맡기고, 코딩 시의 불편함이나 성능 저하를 신경 쓰지 않겠다면 BigDecimal을 사용하라
#### BigDecimal이 제공하는 여덟 가지 반올림 모드를 이용하여 반올림을 완벽히 제어할 수 있음
#### 성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크기 않다면 int나 long을 사용하라
#### 숫자를 아홉 자리 십진수로 표현할 수 있다면 int를 사용하고, 열여덟 자리 십진수로 표현할 수 있다면 long을 사용하라
#### 열여덟 자리를 넘어가면 BigDecimal을 사용해야 함