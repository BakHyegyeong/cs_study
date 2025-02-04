# SOP와 CORS란 무엇인가요?

## SOP(Same Origin Policy)
- 브라우저에서 다른 출처(Origin)의 자원을 요청하는 것을 제한하는 정책
> 동일한 출처란 프로토콜, 호스트, 포트가 모두 같은 경우를 의미합니다.
> https://example.com:443과 https://example.com:80은 포트가 다르므로 다른 출처로 간주합니다.
> https://example.com과 http://example.com은 프로토콜(http vs https)이 다르므로 다른 출처로 간주합니다.
> https://example.com/good과 https://example.com/bad은 프로토콜, 호스트, 포트가 모두 같으므로 동일한 출처로 간주합니다.(경로는 무시)
> https://example.com과 https://example.com:443은 포트가 생략된 형태이므로 동일한 출처로 간주합니다.

## CORS(Cross-Origin Resource Sharing)
- 도메인 또는 포트가 다른 서버의 자원을 요청하는 매커니즘

### 동작 방식
1. 클라이언트에서 HTTP 요청을 보낸다. 이때 Origin 헤더에 요청을 보낸 출처를 담아 보낸다. (Origin 헤더 - 사용자가 현재 보고있는 페이지의 출처)
2. 서버에서는 응답 헤더에 `Access-Control-Allow-Origin`을 담아 응답을 보낸다.
3. 브라우저는 Origin 헤더의 출처와 응답의 `Access-Control-Allow-Origin`을 비교한다.
    - 같다면 해당 요청을 안전하다고 간주하고 응답을 가져온다.
    - 다르다면 브라우저가 해당 응답을 임의로 파기하고 자바스크립트로 응답의 내용을 전달하지 않는다.

### CORS 에러
- SOP(Same Origin Policy) 때문에 다른 출처의 자원을 쓸 수 없어서 발생하는 에러

#### 해결 방법
1. 서버에서 응답 헤더에 `Access-Control-Allow-Origin`(허용할 출처)를 추가
    - 허용할 출처를 추가해두고 응답 헤더에 허용할 출처 리스트를 추가해서 보내면 브라우저에서 해당 출처에서만 요청을 허용한다.
    - `Access-Control-Allow-Origin: *` : 모든 출처에서 요청을 허용
    - `Access-Control-Allow-Origin: example.com` : example.com에서만 요청을 허용
    - `Access-Control-Allow-Origin: null` : 모든 출처에서 요청을 허용하지 않음
2. 프록시 서버 사용, 서버 프레임워크의 CORS 설정 변경

## CORS 정책에서 preflight 요청이 필요한 경우는 어떤 상황인가요?

### CORS 요청의 종류
- **Preflight Request(예비 요청)**
    - 실제 요청 전에 브라우저가 서버의 허용 여부를 확인하기 위해 보내는 사전 요청.
    - 조건
        - 단순 요청이 아닌 경우
    - 예: 요청 메서드가 `PUT`, `DELETE`, `PATCH`인 경우처럼 서버에 영향을 줄 수 있는 요청을 보낼 때
    - `Content-Type`이 `application/json` 같이 특정 값으로 설정된 경우

- **Simple Request(단순 요청)**
    - CORS 요청 중 예비 요청이 필요 없이 서버에 바로 요청을 보낼 수 있는 경우
    - 조건
        - HTTP 메서드가 GET, POST, 또는 HEAD 중 하나.
        - Content-Type 헤더 값이 application/x-www-form-urlencoded, multipart/form-data, 또는 text/plain 중 하나.
        - 커스텀 헤더나 인증 정보가 포함되지 않음.

- **Credentialed Request(인증정보를 포함한 요청)**
    - 요청에 인증 정보(쿠키, HTTP 인증, SSL 클라이언트 인증서 등)를 포함하는 경우
    - 조건
        - 요청에 인증 정보(예: 쿠키, 세션) 또는 Authorization 헤더가 포함될 경우.
        - 서버는 반드시 `Access-Control-Allow-Credentials: true`(인증정보를 포함한 요청을 허용)를 응답해야 함.
        - Axios 등의 라이브러리를 사용할 때 `withCredentials: true`로 설정해야 함.
        - `Access-Control-Allow-Origin` 헤더는 구체적인 도메인을 명시해야 하며, `*`는 허용되지 않음.
    - 예: 요청에 쿠키를 포함하여 요청을 보내는 경우, 사용자 인증이 필요한 경우
