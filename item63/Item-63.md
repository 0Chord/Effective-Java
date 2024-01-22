# 문자열 연결은 느리니 주의하
- [x] 문자열 연결 연산자는 여러 문자열을 하나로 합쳐주는 편리한 수단
#### 문자열 연결 연산자로 문자열 n개를 잇는 시간은 n^2에 비례함
- [x] 문자열은 불변이라서 두 문자열을 연결할 경우 양쪽의 내용을 모두 복사해야 하므로 성능 저하는 피할 수 없는 결과
~~~java
public String statement(){
    String result = "";
    for (int i=0; i<numItems(); i++)
        result += lineForItem(i);
    return result;
}
~~~
#### 성능을 포기하고 싶지 않다면 String 대신 StringBuilder를 사용하자
#### 성능에 신경 써야 한다면 많은 문자열을 연결할 때는 무자열 연결 연산자를 피하자
#### StringBuffer의 append 메서드를 사용하라
