# 전통적인 for 문보다는 for-each 문을 사용하라
~~~java
for (Iterator<Element> i = c.iterator(); i.hasNext(); ) {
    Element e = i.next();
    ...
}
~~~
- [x] while 문보다는 낫지만 가장 좋은 방법은 아님
- [x] for-each 문의 정식 이름은 "향상된 for 문"
- [x] 반복자와 인덱스 변수를 사용하지 않으니 코드가 깔끔해지고 오류가 날 일도 없음
### 전통적인 for 문과 비교했을 때 for-each문은 명료하고, 유연하고, 버그를 예방해준다, 성능 저하도 없다
#### 가능한 모든 곳에서 for 문이 아닌 foe-each 문을 사용하자