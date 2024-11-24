# 아이템 9 : try-finally 보다는 try-with-resources를 사용하라

자바 라이브러리에는 `close` 메서드를 호출해 직접 닫아줘야만 하는 자원이 많다.

ex) `InputStream`, `OutputStream`, `java.sql.Connection` .. 

## try-finally의 문제점

### 1. 예외 처리 문제

```java
public class AppRunner {
	public static void main(String[] args) {
		MyResource myResource = new MyResource();
		try {
			myResource.doSomething();
		} finally {
			myResource.close();
		}
	}
}
```

: 위의 코드에서는 try 블럭과 finally 블럭에서 모두 예외가 발생할 수 있는데 **finally블럭에서 발생한 예외가 try블럭에서 발생한 예외를 집어 삼켜버린다.**

→  try 블록에서 터진 예외로 인해 finally 블록에서 예외가 발생했음에도 불구하고 **최초 원인인 예외를 체크하지 못하게 되는** 등 디버깅 단계에서 문제가 발생한다.

### 2. 가독성 문제

```java
public static void inputAndWriteString() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    try {
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        try {
             String line = br.readLine();
            bw.write(line);
        } finally {
            bw.close();
        }
    } finally {
        br.close();
    }
}
```

`close` 메서드를 여러 번 호출해야 하는 상황이 올 경우 위와 같이 코드의 가독성이 매우 떨어지게 된다.

## try-with-resources

이 구조를 사용하려면 해당 자원이 `AutoCloseable` 인터페이스를 구현해야한다.

### AutoCloseable 인터페이스

: 단순히 `void`를 반환하는 `close` 메서드 하나만 정의한 인터페이스
→ 닫야아 하는 자원이 있는 클래스의 경우  반드시 `AutoCloseable`를 구현해야 한다.

```java
public interface AutoCloseable {

    void close() throws Exception;
}
```

### 사용 예시

```java
public static String inputString() throws IOException {
    try (BufferedReader br = new BufferedReader(new InputStream(System.in))
			    Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb", "user", "password")
    ) {
        return br.readLine();
    }
}
```

finally에서 `close` 메서드를 따로 호출할 필요 없이 **자동으로 호출**되며 **try안에 여러 구문**이 들어가도 된다.

위와 같은 경우는 `readLine`과 `close` 모두에서 예외가 발생하는 경우, **`close` 호출 시 발생하는 예외는 숨겨지고 `readLine`의 예외가 기록된다.**

https://velog.io/@lychee/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-9.-try-finally-%EB%8C%80%EC%8B%A0-try-with-resources%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC

## 핵심 정리

close를 통해 회수해야 하는 자원을 다룰 때는 try-finally를 사용하는 대신 **반드시** **try-with-resources를 사용하자**. 보다 가독성 좋으며, 쉽고 정확하게 자원을 회수할 수 있다.

또한 커스텀 자원을 회수해야 하는 경우 AutoCloseable 인터페이스를 구현하는 것을 잊지 말도록 하자.