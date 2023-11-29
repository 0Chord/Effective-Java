# try-finally보다는 try-with-resources를 사용하라
- [x] 전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 try-finally가 쓰임
  - [x] 예외가 발생하거나 메서드에서 반환되는 경우를 포함
~~~java
static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader (path));
    try{
        return br.readLine();
    } finally {
        br.close();
    }
}
~~~
~~~java
static void copy(String src, String dst) throws IOException {
   InputStream in = new FileInputStream(src);
   try{
      OutputStream out = new FileOutputStream(dst);
      try{
          byte[] buf = new byte[BUFFER_SIZE];
          int n;
          while ((n = in.read(buf)) >= 0)
              out.write(buf, 0, n);
      } finally {
          out.close();
      }
   } finally {
      in.close();
   }
}
~~~
- [x] try 블록과 finally 블록 모두에서 예외가 발생할 수 있는데, finally 속 예외가 try 속 예외를 집어삼켜 디버깅이 매우 어려워진다
- [x] 이러한 문제들은 try-with-resource 덕에 모두 해결됨
- [x] 이 구조를 사용하려면 해당 자원이 AutoCloseable 인터페이스를 구현해야 함
  - [x] 단순히 void를 반환하는 close 메서드 하나만 덩그러니 정의한 인터페이스
- [x] 닫아야 하는 자원을 뜻하는 클래스를 작성한다면 AutoCloseable을 반드시 구현
~~~java
static String firstLineOfFile(String path) throws IOException {
  try (BufferedReader br = new BufferedReader( new FileReader (path))){
    return br.readLine();
  }
}
~~~
~~~java
static void copy(String src, String dst) throws IOException {
  try (InputStream in = new FileInputStream(src);
      OutputStream out = new FileOutputStream(dst)){
    byte[] buf = new byte[BUFFER_SIZE];
    int n;
    while ((n=in.read(buf)) >= 0
      out.write(buf, 0, n);
  }
}
~~~
#### try-with-resources 버전이 짧고 읽기 수월할 뿐 아니라 문제를 진단하기도 훨씬 좋음
- [x] 보통의 try-finally에서처럼 try-with-resources에서도 catch 절을 쓸 수 있다
- [x] catch 절 덕분에 try 문을 더 중첩하지 않고도 다수의 예외를 처리할 수 있음
~~~java
static String firstLineOfFile(String path, String defaultVal) {
  try (BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readLine();
  } catch (IOException e) {
    return defaultVal;
  }
}
~~~
### 꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용하자! 예외는 없다
#### 코드가 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다