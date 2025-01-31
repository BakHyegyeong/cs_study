# RESTful API란 무엇인가요?
- HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고, HTTP Method(POST, GET, PUT, DELETE, PATCH 등)를 통해 CRUD를 하는 것
- RESTful API는 REST 아키텍처 스타일을 따르는 API

### REST 아키텍처 스타일
- 무상태성(Stateless) : 서버는 클라이언트의 상태를 저장하지 않고 요청에 대한 응답만을 전달(요청마다 필요한 정보를 같이 보내는 방식 ex) JWT)
- 캐시 가능(Cacheable) : HTTP의 캐싱 기능을 사용해 응답을 캐싱할 수 있음
- 계층화(Layered System) : 서버와 클라이언트 사이에 프록시 서버, 캐시 서버 등을 둠
- 인터페이스 일관성(Uniform Interface) : URI로 자원을 표현하고, HTTP Method로 자원에 대한 행위를 정의
- 서버 - 클라이언트 구조(Server-Client) : 서버와 클라이언트가 각각 독립적으로 개발되어야 함

### RESTful API 설계 원칙
1. URI는 정보의 자원을 표현해야 함
2. 자원에 대한 행위는 HTTP Method로 표현
3. 메시지는 자원에 대한 행위와 행위에 필요한 정보를 포함해야 함 
4. API 버전을 관리해야 함
5. URI는 소문자로 작성하고, 하이픈(-)으로 단어를 구분해야 함
6. URI 마지막에는 슬래시(/)를 사용하지 않아야 함

### RESTful API의 장단점
#### 장점
- HTTP 프로토콜을 사용하므로 별도의 인프라가 필요하지 않음
- HTTP Method를 통해 자원에 대한 행위를 명확하게 표현할 수 있음
- 서버와 클라이언트의 역할을 명확하게 분리할 수 있음

#### 단점
- 표준이 없어서 설계 방법이 자유롭기 때문에 일관성이 떨어질 수 있음
---
##### 캐싱 서버
클라이언트의 요청을 대신 받아 서버에 요청을 보내고, 서버로부터 받은 응답을 클라이언트에게 전달하는 역할을 하는 서버 ex) CDN - 콘텐츠를 빠르게 전달하기 위해 사용

##### 프록시 서버
클라이언트와 서버 사이에서 중계 역할을 하는 서버 ex) 보안, 로드 밸런싱, 캐싱
---
## RESTful API를 설계할 때 HTTP 메소드와 상태 코드를 어떻게 활용하나요?
- HTTP Method
    - GET : 자원을 조회
    - POST : 자원을 생성
    - PUT : 자원을 수정
    - DELETE : 자원을 삭제
    - PATCH : 자원을 일부 수정
- 상태 코드
    - 2XX : 성공
    - 3XX : 리다이렉션(요청을 완료하기 위해 추가 동작이 필요)
    - 4XX : 클라이언트 오류(잘못된 문법 등)
      - 401 : 인증이 필요함
      - 403 : 인증된 사용자이지만 접근 권한이 없음
    - 5XX : 서버 오류(서버가 요청을 수행할 수 없음)
---
# Session과 Cookie의 차이는 무엇인가요?
- Session
    - 서버에 인증을 하고나면 서버는 클라이언트에게 세션 ID를 발급. 해당 세션 ID는 클라이언트의 쿠키에 저장.
      - 세션 ID를 사용해 클라이언트와 서버 간의 상태를 유지
      - 세션 ID는 쿠키에 저장되며, 브라우저가 종료되면 세션 ID도 삭제
    - 서버에 저장되기 때문에 보안성이 높음
      - 쿠키는 클라이언트 정보가 다 담겨있지만, 세션은 클라이언트 정보는 서버가 가지고 있음
    
    - 세션 ID를 탈취당하면 사용자의 정보가 노출될 수 있음
- Cookie
  - 서버에 인증을 하고나면 서버는 클라이언트에게 쿠키를 발급
      - 클라이언트의 상태를 유지하기 위해 사용
      - 브라우저가 종료되어도 쿠키는 삭제되지 않음
  - 클라이언트에 저장되기 때문에 보안성이 낮음
      - 쿠키를 탈취당하면 사용자의 정보가 노출될 수 있음
      
## 쿠키의 Secure 속성과 HttpOnly 속성은 각각 어떤 역할을 하나요?
- Secure
    - 쿠키가 HTTPS 프로토콜을 사용할 때만 전송하도록 설정(암호화된 통신에서만 쿠키를 전송)
    - 중간자 공격을 방지하기 위해 사용
- HttpOnly
    - JavaScript를 통해 쿠키에 접근하는 것을 방지
    - XSS 공격을 방지하기 위해 사용
###### 중간자 공격 : 공격자가 통신 사이에 끼어들어 정보를 탈취하는 공격 기법
###### XSS : 공격자가 악성 스크립트를 다른 사용자의 브라우저에서 실행하도록 만드는 공격 기법
```java
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletResponse;

public class CookieExample {
    public static void main(String[] args) {
        Cookie cookie = new Cookie("sessionId", "123456");

        cookie.setSecure(true);

        cookie.setHttpOnly(true);
        
        cookie.setMaxAge(60 * 60); // 1시간 후 만료

        
        HttpServletResponse response = // response 객체를 받아옴
        response.addCookie(cookie); // 쿠키를 추가
    }
}
```
