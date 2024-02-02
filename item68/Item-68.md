# 일반적으로 통용되는 명명 규칙을 따르라
- [x] 자바의 명명 규칙은 크게 철자와 문법, 두 범주로 나뉨
- [x] 철자 규칙은 패키지, 클래스, 인터페이스, 메서드, 필드 타입 변수의 이름을 다룸
- [x] 이 규칙을 어긴 API는 사용하기 어렵고, 유지보수하기 어려움
- [x] 철자 규칙이나 문법 규칙을 어기면 다른 프로그래머들이 그 코드를 읽기 번거로울 뿐 아니라 다른 뜻으로 오해할 수도 잇고 그로 인해 오류까지 발생할 수 있음
#### 패키지와 모듈 이름은 각 요소를 점으로 구분하여 계층적으로 짓는다
#### 클래스와 인터페이스의 이름은 하나 이상의 단어로 이뤄지며, 각 단어는 대문자로 시작함
- [x] 여러 단어의 첫 글자만 딴 약자나 max, min처럼 널리 통용되는 줄임말을 제외하고는 단어를 줄여 쓰지 않도록 함
#### 상수 필드를 구성하는 단어는 모두 대문자로 쓰며 단어 사이는 밑줄로 구분함
- [x] 상수 필드는 값이 불변인 static final 필드를 말함
#### 타입 매개변수 이름은 보통 한 문자로 표현함
- [x] 임의의 타입엔 T를, 컬렉션 원소의 타입은 E를, 맵의 키와 값에는 K와 V를, 예외에는 X를, 메서드의 반환 타입에는 R을 사용함
#### 문법 규칙은 철자 규칙과 비교하면 더 유연하고 논란도 많음
- [x] 객체를 생성할 수 있는 클래스의 이름은 보통 단수 명사나 명사구를 사용함
- [x] 객체를 생성할 수 없는 클래스의 이름은 보통 복수형 명사로 지음
#### 철자 규칙은 직관적이라 모호한 부분이 적은 데 반해, 문법 규칙은 더 복잡하고 느슨함