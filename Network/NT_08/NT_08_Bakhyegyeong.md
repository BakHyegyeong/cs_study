# 출처 (Origin)

![](https://hudi.blog/static/2dbd8008d9c0300ef6003d88ca4baf7a/29373/origin.jpg)

- 동일 출처 : 두 URL의 **프로토콜, 호스트, 포트(명시한 경우)** 가 모두 같은 출처
    - 프로토콜 : https://, http://
    - 호스트 : http://`news`.company.com / http://`store`.company.com/
    - 포트번호
- 동일 출처 예시
    - **`http://example.com/hello`** 와 **`http://example.com/bye`** 는 다른 URL이지만, **프로토콜, 도메인, 포트가 모두 같으므로** 동일 출처이다.
    - **`http://example.com`** 와 **`http://example.com:80`** 은 얼핏보면 다른 출처같지만, 전자의 경우 http 의 기본 포트인 80이 생략된 형태이므로 이는 같은 출처라고 할 수 있다.
- 다른 출처 예시
    - **`http://example.com`** 와 **`https://example.com`** 은 같은 리소스를 가리키고 있지만, **프로토콜이 다르므로** 동일 출처가 아니다.
    

# SOP

**동일한 출처 사이에서만 리소스를 공유**할 수 있다는 규칙

**→ 다른 출처에서 가져온 리소스(문서, 스크립트 등)와 상호작용하는 것을 제한**

## 등장 배경

예전의 웹은 프**론트엔드 레이어와 백엔드 레이어를 별도로 구성하지 않는 경우**가 많았다.

- 서버가 직접 요청 처리의 결과를 HTML 문서로 만들어 클라이언트에게 전송하는 과정으로 이루어진다.
- 모든 처리가 같은 도메인 내에서 일어나 **다른 출처로 요청을 보낼 필요가 없었다.**
- 웹 기술로 할 수 있는 것들이 많아졌고, 자연스럽게 다른 출처로 요청을 하고 응답을 받아오는 수요가 증가하였다.
    
    ex) 제 3자가 제공하는 API 사용, CSS 
    

![](https://velog.velcdn.com/images/wogkr1383/post/32853941-0c7a-4cc2-a118-fe3aef5dac7b/image.png)

# CORS (Cross-Origin Resource Sharing)

도메인 또는 포트가 다른 서버의 자원을 요청하는 매커니즘

→ 리소스 호출이 허용된 출처를 **서버가 명시해놓으면, 출처가 다르더라도 요청과 응답을 주고 받을 수 있도록** 만들어놓은 정책

- **보안 유지**: CORS는 악의적인 사이트가 사용자 데이터를 훔치거나 불법적인 행위를 수행하는 것을 방지하면서도, 필요할 때 안전하게 자원을 공유할 수 있도록 한다.
- **API 활용성 증가**: 많은 웹 서비스가 API를 통해 데이터를 제공한다.
    
    → CORS를 이해하고 구현하면, 다양한 출처에서 자신의 서비스에 접근하도록 허용할 수 있다.
    
- **개발 편의성**: 개발 중에 자주 발생할 수 있는 다양한 출처의 리소스를 통합하는 문제를 해결할 수 있다.

## CORS 동작

1. 단순 요청
2. 예비 요청
3. 인증정보를 포함한 요청

## 단순 요청 **(Simple Requests)**

서버에 본 요청을 한 후, 서버가 이에 대한 응답의 헤더에 `Access-Control-Allow-Origin`를 보내주면 브라우저가 `CORS`정책 위반여부를 검사하는 방식

![](https://hudi.blog/static/e622e1c7cb555d792007aea5c9b1695d/02d09/simple-request.png)

1. 브라우저 : 다른 출처로의 요청을 보낼 때 자동으로 **HTTP 헤더에 Origin을 추가**해 요청
2. 서버 : 응답 헤더에 **`Access-Control-Allow-Origin`** 을 담아서 전송
    
    → 허가된 출처 정보를 담아 브라우저가 SOP 위반 여부를 판단하게 한다.
    
    - `Access-Control-Allow-Origin: * | <origin> | null`
3. 브라우저 : Origin 헤더의 출처와 응답의 **`Access-Control-Allow-Origin`** 를 비교
    - 같다면 해당 요청을 안전하다고 간주하고 응답을 가져온다.
    - 다르다면 브라우저가 해당 응답을 임의로 파기하고 자바스크립트로 응답의 내용을 전달하지 않는다.

### 단순 요청의 조건

- **`GET`**, **`POST`**, **`HEAD`** 메서드만 허용된다.
- **`Accept`**, **`Accept-Language`**, **`Content-Language`**, **`Content-Type`** 헤더만 허용된다.
- **`Content-Type`** 는 **`application/x-www-form-urlencoded`**, **`multipart/form-data`**, **`text/plain`** 이 세가지 값만 허용된다.

## **예비 요청 (Preflight Request)**

실제 요청을 보내기 전에 **사전 요청**을 보내서 해당 리소스에 접근이 가능한지 먼저 확인하는 방식

![](https://hudi.blog/static/54c48d0ab653e9def8662d768170011b/02d09/preflight-request.png)

1. 브라우저 : **OPTIONS 메소드**를 통해 **사전 요청**
    - 요청에 Origin 헤더를 추가 (기존)
    - **`Access-Control-Request-Method`** : 실제 요청의 메서드
    - **`Access-Control-Request-Headers`** : 실제 요청의 헤더 목록
2. 서버 : 사전 요청에 대한 응답
    - **`Access-Control-Allow-Origin`** (기존)
    - **`Access-Control-Allow-Methods`** : 서버 측에서 허용하는 메서드 목록
    - **`Access-Control-Allow-Headers`** : 서버 측에서 허용하는 헤더 목록
    - **`Access-Control-Max-Age`** : 사전 요청의 캐시 기간
        
        → 실제 요청은 한번이지만 두번의 요청을 보내야하는 일이 발생
        
        → 사전 요청으로 발생하는 오버헤드를 줄이기 위해 사전 요청 캐시 기간을 전송한다.
        

### 사전 요청이 필요한 이유

CORS는 **서버가 아닌 브라우저 구현 스펙에 포함된 정책**이다.

→ 서버는 CORS 위반 여부와 상관없이 **일단 요청이 들어오면 처리를 하고 응답을 보낸다.**

→ **`GET`** 과 같은 요청은 단순 조회를 하기 때문에 상관없지만, **`POST`**, **`PUT`**, **`DELETE`** 와 같은 메서드는 서버에 **부작용을 야기할 수 있다.**

→ 사전 요청은 **실제 요청이 CORS를 위반하지 않았는지를 미리 확인하고, 부작용으로부터 서버를 보호하는 역할**을 한다.

## **인증정보를 포함한 요청 (Credentialed Requests)**

쿠키, 토큰과 같이 **사용자 식별 정보가 담긴 요청**일 때 credentials 옵션을 설정하는 방법

### 브라우저

- fetch API의 `credentials` 옵션
    - `same-origin`  : 기본값이며, 같은 출처 간 요청에만 인증 정보를 담게 한다.
    - `include`  : 모든 요청에 인증 정보를 담는다.
    - `omit` : 모든 요청에 인증 정보를 담지 않는다.
- Axios의 `withCredentials` 옵션
    - `true/false`

### 서버

- **`Access-Control-Allow-Credentials`** : true로 설정
- **`Access-Control-Allow-Origin`** : 와일드카드(*)값을 제외한 명확한 출처를 명시해주어야 한다.
