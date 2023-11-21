# 생성자 대신 정적 팩터리 메서드를 고려하라
#### 클래스는 생성자와 별도로 정적 팩터리 메서드(static factory method)를 제공할 수 있음
- 클래스의 인스턴스를 반환하는 단순한 정적 메서드
~~~java
public static Boolean valueOf(boolean b){
    return b ? Boolean.TRUE : Boolean.FALSE;
}
~~~
#### 클래스는 클라이언트에 public 생성자 대신 (혹은 생성자와 함께) 정적 팩터리 메서드를 제공할 수 있음
### 장점
- [x] 이름을 가질 수 있다
  - [x] 생성자에 넘기는 매개변수와 생성자 자체만으로는 반환될 객체의 특성을 제대로 설명하지 못한다
  - [x] 정적 팩터리는 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사 가능
  - [x] 한 클래스에 시그니처가 같은 생성자가 여러 개 필요하면, 생성자를 정적 팩터리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주자
- [x] 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다
  - [x] 이 덕분에 불변 클래스는 인스턴스를 미리 만들거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다
- [x] 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다
  - [x] API를 만들 때 이 유연성을 응용하면 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있다
  - [x] 인터페이스를 정적 팩터리 메서드의 반환 타입으로 사용하는 인터페이스 기반 프레임워크를 만드는 핵심 기술이기도 하다
- [x] 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다
  - [x] 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다
- [x] 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다
### 단점
- [x] 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다
- [x] 정적 팩터리 메서드는 프로그래머가 찾기 어렵다
  - [x] 생성자처럼 API 설명에 명확히 드러나지 않으니 사용자는 정적 팩터리 메서드 방식 클래스를 인스턴스화할 방법을 찾아야 한다
### 정적 팩터리 메서드 사용하는 명명 방식
- from : 매개 변수를 하나받아서 해당 타입의 인서턴스를 반환하는 형변환 메서드
~~~java
Date d = Date.from(instant);
~~~
- of : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
~~~java
Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
~~~
- valueOf : from과 of의 더 자세한 버전
~~~java
BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
~~~
- instance 혹은 getInstance : (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않음
~~~java
StackWalker luke = StackWalker.getInstance(options);
~~~
- create 혹은 newInstance : instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다
~~~java
Object newArray = Array.newInstance(classObject, arrayLen);
~~~
- getType : getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. "Type"은 팩터리 메서드가 반환할 객체의 타입이다
~~~java
FileStore fs = Files.getFileStore(path);
~~~
- newType : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. "Type"은 팩터리 메서드가 반환할 객체의 타입이다
~~~java
BufferedReader br = Files.newBufferedReader(path);
~~~
- type : getType과 newType의 간결한 버전
~~~java
List<Complaint> litany = Collections.list(legacyLitany);
~~~