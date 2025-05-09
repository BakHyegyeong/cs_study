# RESTful API

소프트웨어 아키텍쳐의 한 방식으로 **HTTP URI를 통해 자원을 명시**하고 **HTTP 메서드를 통해 자원에 대한 CRUD Operation을 적용하는 것**

→ **REST API**란 **REST 기반으로 서비스 API를 구현한 것**을 의미하며**RESTful API**란 **REST 원리를 따르는 시스템**


> ⭐ API : **데이터의 기능과 집합을 제공**하여 **컴퓨터 프로그램간** 상호작용을 촉진하며 **서로 정보를 교환할 수 있게 하는 것**


- 각 요청은 **무상태성**을 가지며, 서버가 클라이언트의 상태를 저장하지 않는다.
- REST API는 주로 **JSON 형식**을 통해 데이터를 주고받으며, 웹 기반 애플리케이션에서 **효율적이고 확장성 높은 데이터 통신**을 가능하게 한다.
- **이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것을 목적**으로 한다.
    
    → 성능 향상보다는 **API의 이해도 및 호환성을 높이는 것**이 우선
    

## 구성요소

- 자원 : URI
    
    → Client는 URI를 이용해 자원을 지정하고 해당 자원에 대한 조작을 Server에 요청한다.
    
- 행위 : HTTP Method (GET, POST, PUT, PATCH, DELETE)
- 표현 : json, xml, text

## HTTP Method

- GET : 데이터 조회
    - query string : `/user?id=123`
    - path value : `/users/123`
- POST : 데이터 추가, 등록
    - 데이터를 **BODY안의 쿼리 파라미터 형식(key-value)으로 전달**
- PUT : 데이터 대체, **전체 변경(수정)**
- PATCH : 데이터 **부분 변경(수정)**
- DELETE : 데이터 삭제

## HTTP 상태 코드

- **1XX: Informational(정보 제공)**
    - 임시 응답으로 현재 클라이언트의 요청까지는 처리되었으니 계속 진행하라는 의미
    - HTTP 1.1 버전부터 추가되었다.
- **2XX: Success(성공)**
    - 클라이언트의 요청이 서버에서 성공적으로 처리되었다는 의미
- **3XX: Redirection(리다이렉션)**
    - 주로 서버의 주소 또는 요청한 URI의 웹 문서가 이동되었으니 그 주소로 다시 시도하라는 의미
    - 완전한 처리를 위해서 추가 동작이 필요한 경우에 사용
- **4XX: Client Error(클라이언트 에러)**
    - 없는 페이지를 요청하는 등 클라이언트의 요청 메시지 내용이 잘못된 경우를 의미
- **5XX: Server Error(서버 에러)**
    - 서버의 부하, DB 처리 과정 오류, 서버에서 익셉션이 발생하는 경우를 의미
    - 서버 사정으로 메시지 처리에 문제가 발생한 경우에 사용

### 1XX 정보 제공

| 코드 | 메시지 | 의미 | 설명 |
| --- | --- | --- | --- |
| 1XX | Informational | 정보 제공 | 클라이언트의 요청을 받았으며 작업을 계속 진행하고 있다. |
| 100 | Continue | 계속 | 클라이언트로부터 일부 요청을 받았으니 나머지 요청 정보를 계속 보내주길 바란다. |
| 101 | Switching Protocols | 프로토콜 전환 | 서버는 클라이언트의 요청대로 Upgrade 헤더를 따라 다른 프로토콜로 바꿀 것이다. <br> → 현재는 HTTP 1.1이 최신이므로 사용할 일이 없다. |
| 102 | Processing | 처리중 | 서버가 처리하는 데 오랜 시간이 예상되어 클라이언트에서 타임 아웃이 발생하지 않도록 응답 코드를 보낸다. |

### **2XX 성공**

| 코드 | 메시지 | 의미 | 설명 |
| --- | --- | --- | --- |
| 2XX | Success | 성공 | 데이터 전송이 성공적으로 이루어졌거나, 이해되었거나, 수락되었다. |
| **200** | OK | 성공 | 오류 없이 전송이 성공적으로 처리되었다. |
| **201** | Created | 생성됨 | 요청이 처리되어서 새로운 리소스가 생성되었다. |
| **202** | Accepted | 허용됨 | 서버가 클라이언트의 요청을 수락하였지만 처리가 완료되지 않았다. |
| 203 | Non-authorized Information | 신뢰할 수 없는 정보 | 응답 헤더가 본 서버가 아닌 프록시 서버로부터 제공된 것이다. |
| 204 | Non Content | 콘텐츠 없음 | 클라이언트의 요구를 처리했으나 전송할 데이터가 없다. |
| 205 | Reset Content | 콘텐츠 재설정 | 처리를 성공하였고 브라우저의 화면을 리셋하라. |
| 206 | Partial Content | 일부 콘텐츠 | 콘텐츠의 일부만 보낸다. |

### 3XX 리다이렉션

| **상태 코드** | **상태 텍스트** | **한국어 뜻** | **서버 측면에서의 의미** |
| --- | --- | --- | --- |
| 3XX | Redirection | 리다이렉션 | 클라이언트는 요청을 마치기 위해 추가 동작을 취해야 한다. |
| 300 | Multiple Choices | 여러 선택항목 | 선택 항목이 여러 개 있다. |
| **301** | Moved Permanently | 영구 이동 | 지정한 리소스가 새로운 URI로 이동하였다. |
| 302 | Found | 다른 위치 찾음 | 요청한 리소스를 다른 URI에서 찾았다. <br> → 301과 비슷하지만 새 URL은 임시 저장 장소로 해석 |
| **303** | See Other | 다른 위치 보기 | 다른 위치로 요청하라.  |
| 304 | Not Modified | 수정되지 않음 | 마지막 요청 이후 요청한 페이지는 수정되지 않았다. |
| 305 | Use Proxy | 프록시 사용 | 지정한 리소스에 액세스하려면 프록시를 통해야 한다. |
| **307** | Temporary Redirect | 임시 리다이렉션 | 임시로 리다이렉션 요청이 필요하다. |

[상세 코드 정리](
https://hongong.hanbit.co.kr/http-%EC%83%81%ED%83%9C-%EC%BD%94%EB%93%9C-%ED%91%9C-1xx-5xx-%EC%A0%84%EC%B2%B4-%EC%9A%94%EC%95%BD-%EC%A0%95%EB%A6%AC/)

<br>

# Session과 Cookie

## 쿠키와 세션을 사용하는 목적

HTTP는 상태 비저장 프로토콜이기 때문에 요청을 보낼 때마다 클라이언트가 누구인지 매번 확인해야한다는 단점이 있다.

→ 이를 보완하기 위해 쿠키와 세션을 사용한다.

## 쿠키

: **클라이언트(브라우저/개인 컴퓨터)의 로컬**에 저장되는 **키와 값이 들어있는 작은 데이터 파일**

- HTTP에서 클라이언트의 상태 정보를 클라이언트의 PC에 저장하였다가 **브라우저가 Request 시 Request Header**를 넣어서 사용자가 따로 설정할 필요 없이  **자동으로 서버에 전송**한다.
- 쿠키는 **브라우저가 종료**되어도 쿠키 만료 기간이 유효하다면 **클라이언트의 로컬에 계속 저장**되어 있다.

### 동작 과정

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FnaQBL%2Fbtrzd9hCR2W%2FtRw3FatkqCQDZUuXXoFKjk%2Fimg.png)

1. 클라이언트가 페이지를 요청
2. 서버에서 쿠키를 생성
3. 생성한 쿠키에 정보를 담아 **HTTP header에 포함시켜 응답**
4. 넘겨받은 쿠키를 클라이언트가 로컬에 저장했다가 사이트를 재방문할 경우  **서버에 요청할 때 HTTP header에 담아 전송**
5. 서버에서 쿠키를 읽어 이전 상태 정보를 변경할 필요가 있을 때 **쿠키를 업데이트하여 HTTP header에 담아 응답**

### 예시

- 로그인 시 아이디와 비밀번호를 저장하는 옵션 또는 아이디 비밀번호 자동 입력
- 팝업창의 오늘 이 창을 다시 보지 않기 옵션

---

## 세션

: 클라이언트가 **웹 서버에 접속한 시점**부터 웹 브라우저를 종료하여 **연결을 끊기 전까지** 사용자로부터 들어오는 요청을 **하나의 상태로 보고 그 상태를 유지**시키는 기술

- 클라이언트가 웹 서버에 접속해 있는 상태를 하나의 단위로 보고 클라이언트를 구분하기 위해 **클라이언트마다 세션 ID**를 부여한다.
- 쿠키를 기반으로 동작하지만 사용자 정보를 클라이언트가 아닌 **서버측에서 관리**한다.
- 서버에서 **session을 종료**하거나 클라이언트가 **브라우저를 닫을 때 삭제**가 되고 클라이언트의 **정보를 서버에 저장**하기 때문에 쿠키보다 비교적 **보안이 좋다.**
- 클라이언트가 많아질수록 서버의 메모리를 많이 차지하므로 **서버의 성능을 저하**시킬 수 있다.

### 동작 과정

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F9jDVd%2Fbtry8x5uYnp%2FnCfd5TqkPuKgxuey3k5571%2Fimg.png)

1. 클라이언트가 서버에 접속시 **Request header를 확인해 세션 ID가 있는지** 확인하고 없다면 **세션 ID를 발급해 클라이언트에게 전달**
2. 클라이언트는 서버로부터 받은 **세션 ID를 쿠키에 저장**
3. 클라이언트는 서버에 요청을 보낼 때 **세션 ID를 HTTP header에 담아 전송**
4. 서버는 전달받은 **세션 ID로 세션에 있는 클라이언트의 정보**를 이용해 요청을 처리한 후 응답

### 예시

- 화면을 이동해도 로그인이 풀리지 않고 로그아웃하기 전까지 유지

<br>

## 쿠키와 세션의 차이점

|  | **쿠키 Cookie** | **세션 Session** |
| --- | --- | --- |
| **저장위치** | 클라이언트 로컬 (브라우저/ 개인컴퓨터) | 서버 (웹사이트) |
| **보안** | 취약 | 강함 |
| **라이프사이클** | 브라우저 종료해도만료시점 지나지 않으면 삭제X | 브라우저 종료되면 삭제 |
| **속도** | 빠름 | 느림 |
| **저장형식** | Text | Object |
| **용량** | 제한 있음 총 300개 하나의 도메인 당 20개하나의 쿠키당 4KB | 서버가 허용하는 한 제한 없음 |

1. **저장위치**
    - **쿠키**는 **클라이언트의 로컬에 저장**됨으로써 서버의 자원을 전혀 사용하지 않는다.
    - **세션**은 **서버에 저장**되기 때문에 서버의 자원을 사용한다.
        
        → 이때문에 서버의 성능이 저하될 수 있으므로 세션보다 쿠키가 더 유리할 때가 있다.
        
2. **보안** 
    - **쿠키**는 클라이언트에 저장되기 때문에 **변질되거나 request 단계에서 스니핑을 당할 가능성**이 있어 **보안에 취약**하다.
    - **세션**은 쿠키를 이용해 **세션 ID만 저장하고 이를 서버에서 구분해 처리**하기 때문에 비교적 **보안성이 좋다.**
3. **속도**
    - **쿠키**에 **정보가 저장**되어 있기 때문에 서버에 요청시 **속도가 빠르다.**
    - **세션**은 정보가 **서버에 있기 때문에 한번 처리 과정**을 거쳐야 한다는 점에서 **비교적 속도가 느리다.**

## Cookie의 HttpOnly와 Secure 속성

- HttpOnly : JavaScript를 통해 쿠키에 접근할 수 없게 막는다.
    
    → **악성 스크립트를 통해** 쿠키 값에 접근하는 것을 막는다.
    
- Secure : HTTPS 프로토콜을 사용하여 데이터를 암호화
    
    → **네트워크상에서의 쿠키 탈취**를 막는다.