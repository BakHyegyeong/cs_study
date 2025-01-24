# HTTP와 HTTPS의 차이점은 무엇인가요?
## HTTP
웹 서버와 통신하기 위한 프로토콜
``` http request
// index.html을 요청하는 HTTP 요청 메시지
GET /index.html HTTP/1.1  -  헤더
Host: namu.wiki  -  호스트
- 공백 -
```

``` http response
// index.html을 응답하는 HTTP 응답 메시지
HTTP/1.1 200 OK
<헤더>

<HTML>
    <HEAD>
        <TITLE>Hello!</TITLE>
    </HEAD>

    <BODY>
        <p>World!</p>
    </BODY>
</HTML>
```

## HTTPS
HTTP에 보안 기능을 추가한 프로토콜(SSL/TLS)

### 차이점
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbbdq6V%2FbtsFkHuk0YC%2Fqt2jNetemIHEOdH2i3KNBk%2Fimg.png" width="70%">

1. **보안**
    - HTTP는 평문 통신이므로 중간에서 정보를 탈취할 수 있음
    - HTTPS는 SSL/TLS 프로토콜을 사용해 암호화하여 중간에서 정보를 탈취할 수 없음
- **포트**
    - HTTP는 80번 포트를 사용
    - HTTPS는 443번 포트를 사용
- **인증서**
    - HTTPS는 인증서를 사용해 서버의 신원을 확인
    - HTTP는 인증서를 사용하지 않음
- **속도**
    - HTTPS는 암호화/복호화 과정이 추가되어 속도가 느림
    - HTTP는 암호화/복호화 과정이 없어 속도가 빠름

## SSL Handshake
<img src="https://img1.daumcdn.net/thumb/R1280x0.fjpg/?fname=http://t1.daumcdn.net/brunch/service/user/JqQ/image/lhPVlfrjDBwbTZ-ooFmx1qAgyBw" width="70%">

#### CA 인증서 발급 과정
1. 서버가 CA에게 인증서 발급 요청
2. CA는 서버의 정보를 확인하고 인증서 해시 값과 인증서를 CA의 개인키로 암호화하여 서버에게 전송
3. 서버는 CA의 공개키로 복호화하여 인증서를 해시하여 CA로부터 온 해시 값과 비교(인증서가 CA로부터 온 것인지 확인)

1. 클라이언트 -> 서버 : Client Hello(암호화 방식, 세션 ID, 랜덤 데이터)
2. 서버 -> 클라이언트 : Server Hello(암호화 방식, 세션 ID, 랜덤 데이터, 인증서(CA에게 받은 인증서))
3. 클라이언트 : 인증서 확인 및 Pre-Master Secret 생성하여 서버로 전송
    - CA의 공개키로 서버로부터 온 인증서 복호화
    - Pre-Master Secret 생성
4. 서버는 Pre-Master Secret을 복호화하여 Master Secret 생성
5. Master Secret으로 Session Key 생성. 해당 세션 키 대칭키 암호화로 통신

# GET과 POST 메소드의 차이를 설명해주세요.
## GET
- URL에 데이터를 포함하여 요청
- 데이터가 URL에 노출되어 중요한 정보를 전송할 때 사용하지 않음
- 데이터 길이 제한이 있음
- 캐싱 가능

## POST
- HTTP 요청의 body에 데이터를 포함하여 요청
- 데이터가 URL에 노출되지 않아 중요한 정보를 전송할 때 사용
- 데이터 길이 제한이 없음
- 캐싱을 하지 않음
