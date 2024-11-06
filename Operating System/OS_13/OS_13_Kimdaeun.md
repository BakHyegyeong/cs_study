## 아파치 (Apache)

: 아파치 소프트웨어 재단의 오픈소스 프로젝트로 **정적인 데이터를 처리하는**  **Web Server이다.**

## 톰캣 (Tomcat)

: **동적 웹**을 만들기 위한 웹 컨테이너, 서블릿 컨테이너이자 **WAS (Web Application Server)** 이다.

 웹 서버에서 정적으로 처리해야할 데이터를 제외한 **JSP, PHP, HTTP 요청** 등의 **동적 데이터 처리를 담당**한다.

## 아파치 톰캣


![](https://velog.velcdn.com/images/whddlrs/post/bd88267b-ed69-4ce0-959a-acf8fc56cfb7/image.png)

톰캣 안의 컨테이너가 일부 아파치의 기능을 발휘하기 때문에 보통 아파치 톰캣으로 합쳐 부른다.

## 멀티 프로세스인가 멀티 스레드인가

![](https://blog.kakaocdn.net/dn/GnXky/btryXb1OIjc/CKjkDWWJ79TOxo0eddVKQK/img.png)

**아파치**는 기본적으로 **멀티 프로세스**로 구현되어 있지만 설정에 따라 멀티 스레드를 같이 운용할 수 있다.

**톰캣**은 요청을 처리하기 위한 **Thread Pool을 관리**하며 
요청이 들어오면 Thread Pool 내에 스레드를 제공하여 요청을 처리한다.
