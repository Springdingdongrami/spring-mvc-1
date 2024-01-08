POSTMAN 주소: https://documenter.getpostman.com/view/25554684/2s9Ykn8Mka

# 1. 웹 애플리케이션 이해

### 웹 서버, 웹 애플리케이션 서버

- 모든 것이 HTTP - HTTP 메시지에 모든 것을 전송

- 웹 서버
    - HTTP 기반으로 동작
    - 정적 리소스 제공, 기타 부가 기능
    - 정적(파일) HTML, CSS, JS, 이미지, 영상
    - 예) NGINX, APACHE

- 웹 애플리케이션 서버 (WAS)
    - HTTP 기반으로 동작
    - 웹 서버 기능 포함 + (정적 리소스 제공 가능)
    - 프로그램 코드를 실행해서 애플리케이션 로직 실행
        - 동적 HTML, HTTP API (JSON)
        - 서블릿, JSP, 스프링 MVC
    - 예) 톰캣 Jetty, Undertow

- 웹 서버 vs 웹 애플리케이션 서버
    - 웹 서버는 정적 리소스(파일), WAS는 애플리케이션 로직
    - 자바는 서블릿 컨테이너 기능을 제공하면 WAS
    - WAS는 애플리케이션 코드를 실행하는데 더 특화

- 웹 시스템 구성
    - WEB, WAS, DB
    - 정적 리소스는 웹 서버가 처리
    - 웹 서버는 애플리케이션 로직 같은 동적인 처리가 필요하면 WAS에 요청을 위임
    - WAS는 중요한 애플리케이션 로직 처리 전담
    - 효율적인 리소스 관리
        - 정적 리소스가 많이 사용되면 Web 서버 증설
        - 애플리케이션 리소스가 많이 사용되면 WAS 증설
    - WAS, DB 장애 시 WEB 서버가 오류 화면 제공 가능

### 서블릿

- 서버에서 처리해야 하는 업무
    - 서버 TCP/IP 대기, 소켓 연결
    - HTTP 요청 메시지를 파싱해서 읽기
    - POST 방식, /save URL 인지
    - Content-Type 확인
    - HTTP 메시지 바디 내용 파싱
    - 저장 프로세스 실행
    - 비즈니스 로직 실행
        - 데이터베이스에 저장 요청
    - HTTP 응답 메시지 생성 시작
        - HTTP 시작 라인 생성
        - Header 생성
        - 메시지 바디에 HTML 생성에서 입력
    - TCP/IP에 응답 전달, 소켓 종료

- 서블릿이 비즈니스 로직 실행을 제외한 모든 일을 수행한다!
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/883f19f0-12db-407d-9839-9e569ef6716e)
    
    - urlPatterns의 URL의 호출되면 서블릿 코드가 실행
    - HTTP 요청 정보를 편리하게 사용할 수 있는 HttpServletRequest
    - HTTP 응답 정보를 편리하게 제공할 수 있는 HttpServletResponse
    - 개발자는 HTTP 스펙을 매우 편리하게 사용

- HTTP 요청, 응답 흐름
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/3c162fb0-7acf-4520-9148-5e8a985d5f1d)
    
    - HTTP 요청 시 WAS는 request, response 객체를 새로 만들어서 서블릿 객체 호출
    - 개발자는 request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
    - 개발자는 response 객체에서 HTTP 응답 정보를 편리하게 입력
    - WAS는 response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

- 서블릿 컨테이너
    - 서블릿을 지원하는 WAS
    - 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
    - 서블릿 객체는 싱글톤으로 관리
        - 고객의 요청이 올 때 마다 계속 객체를 생성하는 것은 비효율적이다.
        - 최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용
        - 모든 고객 요청은 동일한 서블릿 객체 인스턴스에 접근
        - **공유 변수 사용 주의**
        - 서블릿 컨테이너 종료 시 함께 종료
    - JSP도 서블릿으로 변환 되어서 사용
    - **동시 요청을 위한 멀테 스레드 처리 지원**

### 동시 요청 - 멀티 스레드

- 스레드
    - 애플리케이션 코드를 하나하나 순차적으로 실행하는 것은 스레드
    - 자바 메인 메서드를 처음 실행하면 main이라는 이름의 스레드가 실행
    - 스레드가 없다면 자바 애플리케이션 실행 불가능
    - 스레드는 한 번에 하나의 코드 라인만 수행
    - 동시 처리가 필요하면 스레드를 추가로 생성

- 요청 마다 스레드 생성
    - 장점
        - 동시 요청을 처리할 수 있다.
        - 리소스(CPU, 메모리)가 허용할 때까지 처리 가능
        - 하나의 스레드가 지연 되어도, 나머지 스레드는 정상 동작한다.
    - 단점
        - 스레드 생성 비용은 매우 비싸다. 고객의 요청이 올 때마다 스레드를 생성하면 응답 속도가 늦어진다.
        - 스레드는 컨텍스트 스위칭 비용이 발생한다.
        - 스레드 생성에 제한이 없다. 고객 요청이 너무 많이 오면 CPU, 메모리 임계점을 넘어서 서버가 죽을 수 있다.

- 스레드 풀
    - 특징
        - 필요한 스레드를 스레드 풀에 보관하고 관리한다.
        - 스레드 풀에 생성 가능한 스레드의 최대치를 관리한다. 톰캣은 최대 200개 기본 설정 (변경 가능)
    - 사용
        - 스레드가 필요하면 이미 생성되어 있는 스레드를 스레드 풀에서 꺼내서 사용한다.
        - 사용을 종료하면 스레드 풀에 해당 스레드를 반납한다.
        - 최대 스레드가 모두 사용중이어서 스레드 풀에 스레드가 없으면 기다리는 요청은 거절하거나 특정 숫자만큼만 대기하도록 설정할 수 있다.
    - 장점
        - 스레드가 미리 생성되어 있으므로 스레드를 생성하고 종료하는 비용(CPU)이 절약되고, 응답 시간이 빠르다.
        - 생성 가능한 스레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기본 요청은 안전하게 처리할 수 있다.

- WAS의 주요 튜닝 포인트 → 최대 스레드 수
    - 이 값을 너무 낮게 설정하면?
        - 동시 요청이 많으면 서버 리소스는 여유롭지만, 클라이언트는 금방 응답 지연
    - 이 값을 너무 높게 설정하면?
        - 동시 요청이 많으면 CPU, 메모리 리소스 임계점 초과로 서버 다운
    - 장애 발생 시?
        - 클라우드면 일단 서버부터 늘리고, 이후에 튜닝
        - 클라우드가 아니면 열심히 튜닝

- WAS의 멀티 스레드 지원
    - 멀티 스레드에 대한 부분은 WAS가 처리 (개발자는 신경 쓰지 않아도 됨)
    - 멀티 스레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용하자.

### HTML, HTTP API, CSR, SSR

- 정적 리소스
    - 고정된 HTML 파일, CSS, JS, 이미지, 영상 등을 제공
    - 주로 웹 브라우저

- HTML 페이지
    - 동적으로 필요한 HTML 파일을 생성해서 전달
    - 웹 브라우저: HTML 해석

- HTTP API
    - HTML이 아니라 데이터를 전달
    - 주로 JSON 형식 사용
    - 다양한 시스템에서 호출
    - 데이터만 주고 받음. UI 화면이 필요하면 클라이언트가 별도 처리
    - 앱, 웹 클라이언트, 서버 to 서버

- SSR (서버 사이드 렌더링)
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/474d3361-25a2-40de-a43e-6c9d7d644687)

    - HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달
    - 주로 정적인 화면에 사용
    - 관련 기술: JSP, 타임리프 → 백엔드 개발자

- CSR (클라이언트 사이드 렌더링)
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/e364b93f-dde7-452e-80bc-5e2fb452c54a)
    
    - HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용
    - 주로 동적인 화면에 사용, 웹 환경을 마치 앱처럼 필요한 부분부분 변경할 수 있음
    - 예) 구글 지도, Gmail, 구글 캘린더
    - 관련 기술: React, Vue.js → 웹 프론트엔드 개발자
 
# 2. 서블릿

### 프로젝트 생성

** Packaging: War (JSP 사용을 위함)

** Dependencies: Spring Web, Lombok 추가

### Hello 서블릿

- 스프링 부트 서블릿 환경 구성
    - `@ServletComponentScan` : 스프링부트는 서블릿을 직접 등록해서 사용할 수 있도록 애노테이션을 지원한다.
        
        ```java
        package hello.servlet;
        
        import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;
        import org.springframework.boot.web.servlet.ServletComponentScan;
        
        @ServletComponentScan // 서블릿 자동 등록
        @SpringBootApplication
        public class ServletApplication {
        
        	public static void main(String[] args) {
        		SpringApplication.run(ServletApplication.class, args);
        	}
        
        }
        ```
        
    - `@WebServlet` : 서블릿 애노테이션
        - name: 서블릿 이름
        - urlPatterns: URL 매핑
        
        ```java
        package hello.servlet.basic;
        
        import jakarta.servlet.ServletException;
        import jakarta.servlet.annotation.WebServlet;
        import jakarta.servlet.http.HttpServlet;
        import jakarta.servlet.http.HttpServletRequest;
        import jakarta.servlet.http.HttpServletResponse;
        
        import java.io.IOException;
        
        @WebServlet(name = "helloServlet", urlPatterns = "/hello")
        public class HelloServlet extends HttpServlet {
        
            @Override
            protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                System.out.println("HelloServlet.service");
                System.out.println("request = " + request);
                System.out.println("response = " + response);
        
                String username = request.getParameter("username");
                System.out.println("username = " + username);
        
                response.setContentType("text/plain");
                response.setCharacterEncoding("utf-8");
                response.getWriter().write("hello " + username);
            }
        
        }
        ```
        
        - HTTP 요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 service 메서드를 실행한다.
        - 웹 브라우저 실행 → `http://localhost:8080/hello?username=kim`
            
            ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/bc748a51-cf8a-485b-ad01-a135588e71bd)
            
            ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/b00f976b-8662-42fa-b64d-d066d9a1a900)
            

- HTTP 요청 메시지 로그로 확인하기
    - application.properties
        
        ```java
        logging.level.org.apache.coyote.http11=debug
        ```
        
    
    ** 운영서버에 이렇게 모든 요청 정보를 다 남기면 성능저하가 발생할 수 있다. 개발 단계에서만 적용하자.
    

- 서블릿 컨테이너 동작 방식
    - 내장 톰캣 서버 생성
        
        ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/6b36c8f1-d7c1-48b1-bed6-d156d887dd91)
        
    - HTTP 요청, HTTP 응답 메시지
        
        ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/f36408ab-1edf-4fa4-a688-18d76fba583c)
        
    - 웹 애플리케이션 서버의 요청 응답 구조
        
        ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/d8f1312e-44d3-45c8-ad4d-95fecadeca22)
        
    
    ** HTTP 응답에서 Content-Length는 WAS가 자동으로 생성해준다.
    

### HttpServletRequest

- HttpServletRequest 역할
    - 서블릿은  HTTP 요청 메시지를 파싱한 결과를 `HttpServletRequest` 객체에 담아서 제공한다.

- HTTP 요청 메시지
    
    ```
    POST /save HTTP/1.1
    Host: localhost:8080
    Content-Type: application/x-www-form-urlencoded
    
    username=kim&age=20
    ```
    
    - START LINE
        - HTTP 메서드
        - URL
        - 쿼리 스트링
        - 스키파, 프로토콜
    - 헤더
        - 헤더 조회
    - 바디
        - form 파라미터 형식 조회
        - message body 데이터 직접 조회

- 임시 저장소 기능 (해당 HTTP 요청이 시작될 때부터 끝날 때까지 유지)
    - 저장: `request.setAttribute(name, value)`
    - 조회: `request.setAttribute(name)`

- 세션 관리 기능
    - `request.getSession(create: true)`

### HTTP 요청 데이터

- GET - 쿼리 파라미터
    - /url?username=hello&age=20
    - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
    - 예) 검색, 필터, 페이징 등에서 많이 사용

- POST - HTML Form
    - content-type: application/x-www-form-urlencoded
    - 메시지 바디에 쿼리 파라미터 형식으로 전달 username=hello&age=20
    - 예) 회원 가입, 상품 주문, HTML Form 사용

- HTTP message body에 데이터를 직접 담아서 요청
    - HTTP API에서 주로 사용, JSON, XML, TEXT
    - 데이터 형식은 주로 JSON 사용
    - POST, PUT, PATCH

### HTTP 요청 데이터 - GET 쿼리 파라미터

- 쿼리 파라미터는 URL에 다음과 같이 `?`를 시작으로 보낼 수 있다. 추가 파라미터는 `&`로 구분하면 된다.
    - `http://localhost:8080/request-param?username=hello&age=20`

- 서버에서는 HttpServletRequest가 제공하는 다음 메서드를 통해 쿼리 파라미터를 편리하게 조회할 수 있다.
    
    ```java
    /* 쿼리 파라미터 조회 메서드 */
    
    // 단일 파라미터 조회
    String username = request.getParameter("username");
    
    // 파라미터 이름들 모두 조회
    Enumeration<String> parameterNames = request.getParameterNames();
    
    // 파라미터를 Map으로 조회
    Map<String, String[]> parameterMap = request.getParameterMap();
    
    // 복수 파라미터 조회
    String[] usernames = request.getParameterValues("username");
    ```
    

- 복수 파라미터에서 단일 파라미터 조회
    - `request.getParameter()`는 하나의 파라미터에 대해 단 하나의 값만 있을 때 사용해야 한다. (중복일 때 사용하면 첫 번째 값을 반환함)
    - 중복일 때는 `request.getParameterValues()`를 사용해야 한다.

### HTTP 요청 데이터 - POST HTML Form

- POST의 HTML Form을 전송하면 웹 브라우저는 다음 형식으로 HTTP 메시지를 만든다.
    - 요청 URL: `http://localhost:8080/request-param`
    - content-type: `application/x-www-form-urlencoded`
    - message body: `username=hello&age=20`

- `application/x-www-form-urlencoded` 형식(폼으로 데이터를 전송하는 형식)은 앞서 GET에서 살펴본 쿼리 파라미터 형식과 같기 때문에 **쿼리 파라미터 조회 메서드를 그대로 사용**하면 된다.
    - `request.getParameter()`로 편리하게 구분 없이 조회할 수 있다.

> content-type은 HTTP 메시지 바디의 데이터 형식을 지정한다. <br> <br>
> ☑️ **GET URL 쿼리 파라미터 형식**으로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다. <br>
> ☑️ **POST HTML Form 형식**으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기 때문에 바디에 포함된 데이터가 어떤 형식인지 content-type을 꼭 지정해야 한다.

### HTTP 요청 데이터 - API 메시지 바디

- HTTP 메시지 바디의 데이터를 InputStream을 사용해서 직접 읽을 수 있다.
    - InputStream은 byte 코드를 반환한다.
    - byte 코드를 우리가 읽을 수 있는 문자(String)로 보려면 문자표(Charset)를 지정해주어야 한다. (여기서는 UTF_8 Charset을 지정해주었다.)

- 단순 텍스트 전송
    - POST http://localhost:8080/request-body-string
    - content-type: text/plain
    - message body: hello

- JSON
    - JSON 형식으로 파싱할 수 있도록 객체를 생성해야 한다.
        
        ```java
        package hello.servlet.basic;
        
        import lombok.Getter;
        import lombok.Setter;
        
        @Getter
        @Setter
        public class HelloData {
        
            private String username;
            private int age;
        
        }
        ```
        
    - JSON 형식 전송
        - POST http://localhost:8080/request-body-json
        - content-type: **application/json**
        - message body: {”username”: “hello”, “age”:20}
    - `ObjectMapper` 객체를 이용하여 JSON 결과를 파싱하여 자바 객체로 변환할 수 있다.

### HttpServletResponse

- 역할
    - HTTP 응답 메시지 생성
        - 응답 코드 지정, 헤더 생성, 바디 생성
    - 편의 기능 제공
        - Content-Type, 쿠키, Redirect

### HTTP 응답 데이터

- 단순 텍스트 응답
    
    ```java
    PrintWriter writer = response.getWriter();
    writer.println("ok");
    ```
    

- HTML 응답
    
    ```java
    package hello.servlet.basic.response;
    
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.io.PrintWriter;
    
    @WebServlet(name = "responseHtmlServlet", urlPatterns = "/response-html")
    public class ResponseHtmlServlet extends HttpServlet {
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            // Content-Type: text/html;charset=utf-8
            response.setContentType("text/html");
            response.setCharacterEncoding("utf-8");
    
            PrintWriter writer = response.getWriter();
            writer.println("<html>");
            writer.println("<body>");
            writer.println("  <div>안녕?</div>");
            writer.println("</body>");
            writer.println("</html>");
        }
    
    }
    ```
    
    - content-type: `text/html`

- API JSON 응답
    
    ```java
    package hello.servlet.basic.response;
    
    import com.fasterxml.jackson.databind.ObjectMapper;
    import hello.servlet.basic.HelloData;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    
    @WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
    public class ResponseJsonServlet extends HttpServlet {
    
        private ObjectMapper objectMapper = new ObjectMapper();
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            // Content-Type: application/json
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
    
            HelloData helloData = new HelloData();
            helloData.setUsername("kim");
            helloData.setAge(20);
    
            // {"username": "kim", "age": 20}
            String result = objectMapper.writeValueAsString(helloData);
            response.getWriter().write(result);
        }
        
    }
    ```
    
    - content-type: `application/json`
    - Jackson 라이브러리가 제공하는 `objectMapper.writeValueAsString()` 를 사용하면 객체를 JSON 문자로 변경할 수 있다.
 
# 3. 서블릿, JSP, MVC 패턴

### 회원 관리 웹 애플리케이션 요구사항

- 회원 정보
    - 이름: username
    - 나이: age

- 기능 요구사항
    - 회원 저장
    - 회원 목록 조회

### 서블릿으로 회원 관리 웹 애플리케이션 만들기

- MemberFormServlet
    - 회원 정보를 입력할 수 있는 HTML Form을 만들어서 응답한다.

- MemberSaveServlet
    - HTML Form에서 데이터를 입력하고 전송을 누르면 실제 회원 데이터가 저장되도록 한다.
    - 동작 순서
        1. 파라미터를 조회해서 Member 객체를 만든다.
        2. Member 객체를 MemberRepository를 통해 저장한다.
        3. Member 객체를 사용해서 결과 화면용 HTML을 동적으로 만들어서 응답한다.

- MemberListServlet
    - 저장된 모든 회원 목록을 조회한다.
    - 동작 순서
        1. `memberRepository.findAll()`을 통해 모든 회원을 조회한다.
        2. 회원 목록 HTML을 for 루프를 통해서 회원 수 만큼 동적으로 생성하고 응답한다.

### JSP로 회원 관리 웹 애플리케이션 만들기

- build.gradle에 JSP 라이브러리 추가
    
    ```java
    implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
    implementation 'jakarta.servlet:jakarta.servlet-api'
    implementation 'jakarta.servlet.jsp.jstl:jakarta.servlet.jsp.jstl-api'
    implementation 'org.glassfish.web:jakarta.servlet.jsp.jstl'
    ```
    

- JSP는 자바 코드를 그대로 다 사용할 수 있다.
    - `<%@ page ****contentType="text/html;charset=UTF-8" language="java" %>` 
        → JSP 문서라는 뜻이다. (꼭 붙여야 함)   
    - `<%@ page import="hello.servlet.domain.member.MemberRepository" %>`
        → 자바의 import 문과 같다.
    - `<% ~~ %>`
    → 자바 코드를 입력할 수 있다.
    - `<%= ~~ %>` 
    → 자바 코드를 출력할 수 있다.

- 실행 시 URL에 .jsp 까지 함께 적어주어야 한다.

- MVC 패턴의 등장
    - JSP를 사용한 덕분에 뷰를 생성하는 HTML 작업을 깔끔하게 가져가고, 중간중간 동적으로 변경이 필요한 부분에만 자바 코드를 적용했다.
    - 하지만 JSP 코드를 잘 보면, 코드의 상위 절반은 비즈니스 로직이고 나머지 하위 절반만 결과를 HTML로 보여주기 위한 뷰 영역이다. AVA 코드, 데이터를 조회하는 리포지토리 등등 다양한 코드가 모두 JSP에 노출되어 있다. JSP가 너무 많은 역할을 한다.
    - 비즈니스 로직은 서블릿 처럼 다른곳에서 처리하고, JSP는 목적에 맞게 HTML로 화면(View)을 그리는 일에 집중하도록 하자.

### MVC 패턴 - 개요

- MVC 패턴 이전
    - 하나의 서블릿이나 JSP만으로 비즈니스 로직과 뷰 렌더링까지 모두 처리하게 되면, 너무 많은 역할을 하게 되고 결과적으로 유지보수가 어려워진다.
    - 진짜 문제는 둘 사이에 변경의 라이프 사이클이 다르다는 점이다. 예를 들어 UI를 일부 수정하는 일과 비즈니스 로직을 수정하는 일은 각각 다르게 발생할 가능성이 매우 높고 대부분 서로에게 영향을 주지 않는다. 이렇게 변경의 라이프 사이클이 다른 부분을 하나의 코드로 관리하는 것은 유지보수하기 좋지 않다.
    - JSP 같은 뷰 템플릿은 화면을 렌더링 하는데 최적화 되어 있기 때문에 이 부분의 업무만 담당하는 것이 가장 효과적이다.

- Model View Controller
    - MVC 패턴은 하나의 서블릿이나 JSP로 처리하던 것을 컨트롤러와 뷰라는 영역으로 서로 역할을 나눈 것을 말한다.
        
        ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/66fa97e4-d167-440c-9c1d-33251c45abf5)

        
        - **컨트롤러**: HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
        - **모델**: 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다.
        - **뷰**: 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다.
    
    ** 컨트롤러에 비즈니스 로직을 둘 수도 있지만, 이렇게 되면 컨트롤러가 너무 많은 역할을 담당한다. 그래서 일반적으로 비즈니스 로직은 서비스(Service)라는 계층을 별도로 만들어서 처리한다. 그리고 컨트롤러는 비즈니스 로직이 있는 서비스를 호출하는 역할을 담당한다.
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/3c07593f-4ecb-409f-967a-6f7d90d54482)

    

### MVC 패턴 - 적용

- 서블릿을 컨트롤러로 사용하고 JSP를 뷰로 사용해서 MVC 패턴을 적용해보자.
    - Model은 HttpServletRequest 객체를 사용한다. request는 내부에 데이터 저장소를 가지고 있는데, `request.setAttribute()`, `request.getAttribute()`를 사용하면 데이터를 보관하고 조회할 수 있다.
    - `dispatcher.forward()` : 다른 서블릿이나 JSP로 이동할 수 있는 기능이다. 서버 내부에서 다시 호출이 발생한다.
    - `WEB-INF` 경로 안에 JSP가 있으면 외부에서 직접 JSP를 호출할 수 없다. 우리가 기대하는 것은 항상 컨트롤러를 통해서 JSP를 호출하는 것이다.
    - **redirect vs forward** : redirect는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다. 따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다. 반면에 forward는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.
    - JSP는 `${}` 문법을 제공하는데, 이 문법을 사용하면 request의 attribute에 담긴 데이터를 편리하게 조회할 수 있다.
    - taglib 기능을 사용해서 반복하면서 출력이 가능하다.
        - 선언 `<%@ taglib prefix="c" uri="<http://java.sun.com/jsp/jstl/core>"%>`
        - `<c:forEach>` 을 이용하여 리스트에서 각 아이템을 순서대로 꺼내 변수에 담아 출력하는 과정을 반복할 수 있다.

### MVC 패턴 - 한계

- MVC 컨트롤러의 단점
    - 포워드 중복
        - 뷰로 이동하는 코드가 항상 중복 호출된다.
    - viewPath 중복
        - prefix: /WEB-INF/views/
        - suffix: .jsp
    - 사용하지 않는 코드
        - request, response를 사용할 때도 있고 사용하지 않을 때도 있다.
    - 공통 처리가 어렵다.

⇒ **프론트 컨트롤러(Front Controller)** 패턴을 도입하면 이런 문제를 깔끔하게 해결할 수 있다. (입구를 하나로!)

# 4. MVC 프레임워크 만들기

### 프론트 컨트롤러 패턴 소개

- 프론트 컨트롤러 도입 전
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/220c428e-1e9d-4135-be38-e710f874826d)

    

- 프론트 컨트롤러 도입 후
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/4c2e8343-ebc3-4292-a4f4-4153b2d6e709)

    

- 특징
    - 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받는다.
    - 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출한다.
    - 입구를 하나로!
    - 공통 처리 가능
    - 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 된다.

** 스프링 웹 MVC의 핵심도 FrontController이다. 스프링 웹 MVC의 DispatcherServlet이 FrontController 패턴으로 구현되어 있다.

### 프론트 컨트롤러 도입 - v1

- 구조
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/a3260b24-7a99-42b2-96ab-49480c9fd7b8)

    
    ```java
    package hello.servlet.web.frontcontroller.v1;
    
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    
    public interface ControllerV1 {
        void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
    }
    ```
    
    - 서블릿과 비슷한 모양의 컨트롤러 인터페이스를 도입한다.
    - 각 컨트롤러들은 이 인터페이스를 구현하면 된다.
    - 프론트 컨트롤러는 이 인터페이스를 호출해서 구현과 관계없이 로직의 일관성을 가져갈 수 있다.

- 프론트 컨트롤러
    
    ```java
    package hello.servlet.web.frontcontroller.v1;
    
    import hello.servlet.web.frontcontroller.v1.controller.MemberFormControllerV1;
    import hello.servlet.web.frontcontroller.v1.controller.MemberListControllerV1;
    import hello.servlet.web.frontcontroller.v1.controller.MemberSaveControllerV1;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;
    
    @WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*")
    public class FrontControllerServletV1 extends HttpServlet {
    
        private Map<String, ControllerV1> controllerMap = new HashMap<>();
    
        public FrontControllerServletV1() {
            controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
            controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
            controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
        }
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("FrontControllerServletV1.service");
    
            String requestURI = request.getRequestURI();
    
            ControllerV1 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND); // 404
                return;
            }
            
            controller.process(request, response);
        }
    }
    ```
    
    - `urlPatterns = "/front-controller/v1/*"` : **/front-controller/v1** 를 포함한 하위 모든 요청은 이 서블릿에서 받아들인다.
    - controllerMap
        - key: 매핑 URL
        - value: 호출될 컨트롤러
    - service()
        - 먼저 `requestURI`를 조회해서 실제 호출할 컨트롤러를 `controllerMap`에서 찾는다. 만약 없다면 404(SC_NOT_FOUND) 상태 코드를 반환한다.
        - 컨트롤러를 찾고 `controller.process(request, response);`를 호출해서 해당 컨트롤러를 실행한다.

### View 분리 - v2

- 모든 컨트롤러에서 뷰로 이동하는 부분에 중복이 있고, 깔끔하지 않다.
    
    ```java
    String viewPath = "/WEB-INF/views/new-form.jsp";
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);
    ```
    
    - 이 부분을 깔끔하게 분리하기 위해 별도로 뷰를 처리하는 객체를 만들자.

- 구조
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/700c231d-9896-42cd-aec8-54d7fbe60797)

    
- 뷰 객체
    
    ```java
    package hello.servlet.web.frontcontroller;
    
    import jakarta.servlet.RequestDispatcher;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    
    public class MyView {
    
        private String viewPath;
    
        public MyView(String viewPath) {
            this.viewPath = viewPath;
        }
    
        public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
        
    }
    ```
    

- 컨트롤러 인터페이스
    
    ```java
    package hello.servlet.web.frontcontroller.v2;
    
    import hello.servlet.web.frontcontroller.MyView;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    
    public interface ControllerV2 {
        MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
    }
    ```
    
    - 컨트롤러가 뷰를 반환한다.

- 프론트 컨트롤러
    
    ```java
    package hello.servlet.web.frontcontroller.v2;
    
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v2.controller.MemberFormControllerV2;
    import hello.servlet.web.frontcontroller.v2.controller.MemberListControllerV2;
    import hello.servlet.web.frontcontroller.v2.controller.MemberSaveControllerV2;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;
    
    @WebServlet(name = "frontControllerServletV2", urlPatterns = "/front-controller/v2/*")
    public class FrontControllerServletV2 extends HttpServlet {
    
        private Map<String, ControllerV2> controllerMap = new HashMap<>();
    
        public FrontControllerServletV2() {
            controllerMap.put("/front-controller/v2/members/new-form", new MemberFormControllerV2());
            controllerMap.put("/front-controller/v2/members/save", new MemberSaveControllerV2());
            controllerMap.put("/front-controller/v2/members", new MemberListControllerV2());
        }
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("FrontControllerServletV2.service");
    
            String requestURI = request.getRequestURI();
    
            ControllerV2 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND); // 404
                return;
            }
    
            MyView view = controller.process(request, response);
            view.render(request, response);
        }
    }
    ```
    
    - ControllerV2의 반환 타입이 `MyView` 이므로 프론트 컨트롤러는 컨트롤러의 호출 결과로 `MyView` 를 반환 받는다. 그리고 `view.render()` 를 호출하면 `forward` 로직을 수행해서 JSP가 실행된다.
    - 프론트 컨트롤러의 도입으로 `MyView` 객체의 `render()` 를 호출하는 부분을 모두 일관되게 처리할 수 있다. 각각의 컨트롤러는 `MyView` 객체를 생성만 해서 반환하면 된다.

### Model 추가 - v3

- 서블릿 종속성 제거
    - 컨트롤러 입장에서 HttpServletRequest, HttpServletResponse이 꼭 필요할까?
    - 요청 파라미터 정보는 자바의 Map으로 대신 넘기도록 하면 지금 구조에서는 컨트롤러가 서블릿 기술을 몰라도 동작할 수 있다.
    - 그리고 request 객체를 Model로 사용하는 대신에 별도의 Model 객체를 만들어서 반환하면 된다.
    - 우리가 구현하는 컨트롤러가 서블릿 기술을 전혀 사용하지 않도록 변경해보자. → 구현 코드가 단순해지고, 테스트 코드 작성이 쉽다.

- 뷰 이름 중복 제거
    - 컨트롤러는 **뷰의 논리 이름**을 반환하고, 실제 물리 위치의 이름은 프론트 컨트롤러에서 처리하도록 단순화 하자.
    - `/WEB-INF/views/new-form.jsp` → **new-form**
    - `/WEB-INF/views/save-result.jsp` → **save-result**
    - `/WEB-INF/views/members.jsp` → **members**

- 구조
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/bd6a2e0c-d0c1-4b20-a5ea-b11d93dca6d6)

    

- ModelView
    
    ```java
    package hello.servlet.web.frontcontroller;
    
    import java.util.HashMap;
    import java.util.Map;
    
    public class ModelView {
    
        private String viewName;
        private Map<String, Object> model = new HashMap<>();
    
        public ModelView(String viewName) {
            this.viewName = viewName;
        }
    
        public String getViewName() {
            return viewName;
        }
    
        public void setViewName(String viewName) {
            this.viewName = viewName;
        }
    
        public Map<String, Object> getModel() {
            return model;
        }
    
        public void setModel(Map<String, Object> model) {
            this.model = model;
        }
        
    }
    ```
    
    - 지금까지 컨트롤러에서 서블릿에 종속적인 HttpServletRequest를 사용했다. 그리고 Model도 `request.setAttribute()` 를 통해 데이터를 저장하고 뷰에 전달했다.
    - 서블릿의 종속성을 제거하기 위해 Model을 직접 만들고, 추가로 View 이름까지 전달하는 객체를 만들어보자.
    - 뷰의 이름과 뷰를 렌더링할 때 필요한 model 객체를 가지고 있다. model은 단순히 map으로 되어 있으므로 컨트롤러에서 뷰에 필요한 데이터를 key, value로 넣어주면 된다.
    
- 컨트롤러 인터페이스
    
    ```java
    package hello.servlet.web.frontcontroller.v3;
    
    import hello.servlet.web.frontcontroller.ModelView;
    
    import java.util.Map;
    
    public interface ControllerV3 {
        ModelView process(Map<String, String> paramMap);
    }
    ```
    
    - 서블릿 기술을 전혀 사용하지 않는다. 따라서 구현이 매우 단순해지고, 테스트 코드 작성이 쉽다.
    - HttpServletRequest가 제공하는 파라미터는 프론트 컨트롤러가 paramMap에 담아서 호출해주면 된다.
    - 응답 결과로 뷰 이름과 뷰에 전달할 Model 데이터를 포함하는 ModelView 객체를 반환하면 된다.

- MyView 객체에 필요한 메서드를 추가한다.
    
    ```java
    public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        modelToRequestAttribute(model, request);
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
    
    private static void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
        model.forEach((key, value) -> request.setAttribute(key, value));
    }
    ```
    

- 프론트 컨트롤러
    
    ```java
    package hello.servlet.web.frontcontroller.v3;
    
    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;
    
    @WebServlet(name = "frontControllerServletV3", urlPatterns = "/front-controller/v3/*")
    public class FrontControllerServletV3 extends HttpServlet {
    
        private Map<String, ControllerV3> controllerMap = new HashMap<>();
    
        public FrontControllerServletV3() {
            controllerMap.put("/front-controller/v3/members/new-form", new MemberFormControllerV3());
            controllerMap.put("/front-controller/v3/members/save", new MemberSaveControllerV3());
            controllerMap.put("/front-controller/v3/members", new MemberListControllerV3());
        }
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("FrontControllerServletV3.service");
    
            String requestURI = request.getRequestURI();
    
            ControllerV3 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND); // 404
                return;
            }
    
            // paramMap
            Map<String, String> paramMap = createParamMap(request);
            ModelView mv = controller.process(paramMap);
    
            String viewName = mv.getViewName(); // 논리 이름
            MyView view = viewResolver(viewName);
            view.render(mv.getModel(), request, response);
        }
    
        private static MyView viewResolver(String viewName) {
            return new MyView("/WEB-INF/views/" + viewName + ".jsp");
        }
    
        private static Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
        
    }
    ```
    
    - createParamMap()
        - HttpServletRequest에서 파라미터 정보를 꺼내서 Map으로 변환한다. 그리고 해당 Map( `paramMap` )을 컨트롤러 에 전달하면서 호출한다.
    - viewResolver
        - 컨트롤러가 반환한 논리 뷰 이름을 실제 물리 뷰 경로로 변경한다. 그리고 실제 물리 경로가 있는 MyView 객체를 반환한다.
        - 논리 뷰 이름: `members`
        - 물리 뷰 경로: `/WEB-INF/views/members.jsp`
    - view.render(mv.getModel(), request, response)
        - 뷰 객체를 통해 HTML 화면을 렌더링 한다.
        - 뷰 객체의 `render()`는 모델 정보도 함께 받는다.
        - JSP는 `request.getAttribute()` 로 데이터를 조회하기 때문에, 모델의 데이터를 꺼내서
        `request.setAttribute()` 로 담아둔다.
        - JSP로 포워드 해서 JSP를 렌더링 한다.

### 단순하고 실용적인 컨트롤러 - v4

- 구조
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/c0a6e63b-774c-47cc-b200-fc00a822849e)

    
    - 컨트롤러가 ModelView를 반환하지 않고, ViewName만 반환한다.
    (항상 ModelView 객체를 생성하고 반환해야 하는 부분을 수정)

- 컨트롤러 인터페이스
    
    ```java
    package hello.servlet.web.frontcontroller.v4;
    
    import java.util.Map;
    
    public interface ControllerV4 {
        /**
         * @param paramMap
         * @param model
         * @return viewName
         */
        String process(Map<String, String> paramMap, Map<String, Object> model);
    }
    ```
    
    - 인터페이스에 ModelView가 없다.
    - model 객체를 파라미터로 전달되기 때문에 그냥 사용하면 되고, 결과로 뷰의 이름만 반환해주면 된다.
    - 모델이 파라미터로 전달되기 때문에, 각 컨트롤러에서 모델을 직접 생성하지 않아도 된다.

- 프론트 컨트롤러
    
    ```java
    package hello.servlet.web.frontcontroller.v4;
    
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v4.controller.MemberFormControllerV4;
    import hello.servlet.web.frontcontroller.v4.controller.MemberListControllerV4;
    import hello.servlet.web.frontcontroller.v4.controller.MemberSaveControllerV4;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;
    
    @WebServlet(name = "frontControllerServletV4", urlPatterns = "/front-controller/v4/*")
    public class FrontControllerServletV4 extends HttpServlet {
    
        private Map<String, ControllerV4> controllerMap = new HashMap<>();
    
        public FrontControllerServletV4() {
            controllerMap.put("/front-controller/v4/members/new-form", new MemberFormControllerV4());
            controllerMap.put("/front-controller/v4/members/save", new MemberSaveControllerV4());
            controllerMap.put("/front-controller/v4/members", new MemberListControllerV4());
        }
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("FrontControllerServletV4.service");
    
            String requestURI = request.getRequestURI();
    
            ControllerV4 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND); // 404
                return;
            }
    
            Map<String, String> paramMap = createParamMap(request);
            Map<String, Object> model = new HashMap<>();
            String viewName = controller.process(paramMap, model);
            
            MyView view = viewResolver(viewName);
            view.render(model, request, response);
        }
    
        private static MyView viewResolver(String viewName) {
            return new MyView("/WEB-INF/views/" + viewName + ".jsp");
        }
    
        private static Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    
    }
    ```
    
    - 모델 객체 전달
        - 모델 객체를 프론트 컨트롤러에서 생성해서 넘겨준다. 컨트롤러에서 모델 객체에 값을 담으면 여기에 그대로 담겨있게 된다.
    - 뷰의 논리 이름을 직접 반환
        - 컨트롤러가 직접 뷰의 논리 이름을 반환하므로 이 값을 사용해서 실제 물리 뷰를 찾을 수 있다.

### 유연한 컨트롤러1 - v5

- 어댑터 패턴
    - 프론트 컨트롤러가 다양한 방식의 컨트롤러를 처리할 수 있도록 한다. (여러 가지 방식의 인터페이스를 사용할 수 있다.)

- 구조
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/a3b3cf3d-8b9b-4c74-b18f-db39a7b5369f)

    
    - **핸들러 어댑터**: 중간에 어댑터 역할을 한다. 덕분에 다양한 종류의 컨트롤러를 호출할 수 있다.
    - **핸들러**: 컨트롤러의 이름을 더 넓은 범위인 핸들러로 변경했다. 꼭 컨트롤러의 개념 뿐만 아니라 어떠한 것이든 해당하는 종류의 어댑터만 있으면 다 처리할 수 있기 때문이다.

- 핸들러 어댑터
    
    ```java
    package hello.servlet.web.frontcontroller.v5;
    
    import hello.servlet.web.frontcontroller.ModelView;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    
    public interface MyHandlerAdapter {
        boolean supports(Object handler);
        ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
    }
    ```
    
    - `boolean supports(Object handler)`
        - 어댑터가 해당 컨트롤러를 처리할 수 있는지 판단하는 메서드
    - `ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler)`
        - 어댑터는 실제 컨트롤러를 호출하고, 그 결과로 ModelView를 반환해야 한다.
        - 실제 컨트롤러가 ModelView를 반환하지 못하면, 어댑터가 ModelView를 직접 생성해서라도 반환해야 한다.
        - 이전에는 프론트 컨트롤러가 실제 컨트롤러를 호출했지만 이제는 어댑터를 통해서 실제 컨트롤러가 호출된다.

- 어댑터
    
    ```java
    package hello.servlet.web.frontcontroller.v5.adapter;
    
    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v3.ControllerV3;
    import hello.servlet.web.frontcontroller.v5.MyHandlerAdapter;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;
    
    public class ControllerV3HandlerAdapter implements MyHandlerAdapter {
    
        @Override
        public boolean supports(Object handler) {
            return (handler instanceof ControllerV3);
        }
    
        @Override
        public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
            ControllerV3 controller = (ControllerV3) handler;
            Map<String, String> paramMap = createParamMap(request);
            
            ModelView mv = controller.process(paramMap);
            return mv;
        }
    
        private Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    
    }
    ```
    
    - supports
        - ControllerV3을 처리할 수 있는 어댑터인지를 확인한다.
    - handle
        - 핸들러를 ControllerV3로 변환한 다음, V3 형식에 맞도록 호출한다.
        - ControllerV3는 ModelView를 반환하므로 그대로 ModelView를 반환하면 된다.

- 프론트 컨트롤러
    
    ```java
    package hello.servlet.web.frontcontroller.v5;
    
    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;
    import hello.servlet.web.frontcontroller.v5.adapter.ControllerV3HandlerAdapter;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    
    @WebServlet(name = "frontControllerServletV5", urlPatterns = "/front-controller/v5/*")
    public class FrontControllerServletV5 extends HttpServlet {
    
        private final Map<String, Object> handlerMappingMap = new HashMap<>();
        private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();
    
        public FrontControllerServletV5() {
            initHandlerMappingMap();
            initHandlerAdapters();
        }
    
        private void initHandlerMappingMap() {
            handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
            handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
            handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
        }
    
        private void initHandlerAdapters() {
            handlerAdapters.add(new ControllerV3HandlerAdapter());
        }
    
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            Object handler = getHandler(request);
    
            if (handler == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND); // 404
                return;
            }
    
            MyHandlerAdapter adapter = getHandlerAdapter(handler);
            ModelView mv = adapter.handle(request, response, handler);
    
            String viewName = mv.getViewName(); // 논리 이름
            MyView view = viewResolver(viewName);
            view.render(mv.getModel(), request, response);
        }
    
        private MyHandlerAdapter getHandlerAdapter(Object handler) {
            for (MyHandlerAdapter adapter : handlerAdapters) {
                if (adapter.supports(handler)) {
                    return adapter;
                }
            }
            throw new IllegalStateException("handler adapter를 찾을 수 없습니다. handler = " + handler);
        }
    
        private Object getHandler(HttpServletRequest request) {
            String requestURI = request.getRequestURI();
            return handlerMappingMap.get(requestURI);
        }
    
        private static MyView viewResolver(String viewName) {
            return new MyView("/WEB-INF/views/" + viewName + ".jsp");
        }
    
        private static Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    
    }
    ```
    
    - 컨트롤러 뿐만 아니라 어댑터가 지원하기만 하면, 어떤 것이라도 URL에 매핑해서 사용할 수 있다.
    - 생성자
        - 생성자는 핸들러 매핑과 어댑터를 초기화(등록)한다.
    - 매핑 정보
        - 매핑 정보의 값이 `ControllerV3` , `ControllerV4` 같은 인터페이스에서 아무 값이나 받을 수 있는 `Object` 로 변경되었다.
    - 핸들러 매핑
        - 핸들러 매핑 정보인 `handlerMappingMap` 에서 URL에 매핑된 핸들러(컨트롤러) 객체를 찾아서 반환한다.
    - 핸들러를 처리할 수 있는 어댑터 조회
        - `handler` 를 처리할 수 있는 어댑터를 `adapter.supports(handler)` 를 통해서 찾는다.
        - handler가 `ControllerV3` 인터페이스를 구현했다면, `ControllerV3HandlerAdapter` 객체가 반환된다.
    - 어댑터 호출
        - 어댑터의 `handle(request, response, handler)` 메서드를 통해 실제 어댑터가 호출된다.
        - 어댑터는 handler(컨트롤러)를 호출하고 그 결과를 어댑터에 맞추어 반환한다.

### 유연한 컨트롤러2 - v5

- FrontControllerServletV5에 ControllerV4 기능도 추가한다.
    - 핸들러 매핑( `handlerMappingMap` )에 `ControllerV4` 를 사용하는 컨트롤러를 추가하고, 해당 컨트롤러를 처리할 수 있는 어댑터인 `ControllerV4HandlerAdapter` 도 추가한다.

- 핸들러 어댑터
    
    ```java
    package hello.servlet.web.frontcontroller.v5.adapter;
    
    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v4.ControllerV4;
    import hello.servlet.web.frontcontroller.v5.MyHandlerAdapter;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;
    
    public class ControllerV4HandlerAdapter implements MyHandlerAdapter {
        
        @Override
        public boolean supports(Object handler) {
            return (handler instanceof ControllerV4);
        }
    
        @Override
        public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
            ControllerV4 controller = (ControllerV4) handler;
            
            Map<String, String> paramMap = createParamMap(request);
            HashMap<String, Object> model = new HashMap<>();
            String viewName = controller.process(paramMap, model);
            
            ModelView mv = new ModelView(viewName);
            mv.setModel(model);
            return mv;
        }
    
        private Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
        
    }
    ```
    
    - handler를 ControllerV4로 캐스팅 하고, paramMap, model을 만들어서 해당 컨트롤러를 호출한다. 그리고 viewName을 반환 받는다.
    - 어댑터가 호출하는 `ControllerV4` 는 뷰의 이름을 반환한다. 그런데 어댑터는 뷰의 이름이 아니라 `ModelView` 를 만들어서 반환해야 한다.
    - `ControllerV4` 는 뷰의 이름을 반환했지만, 어댑터는 이것을 ModelView로 만들어서 형식을 맞추어 반환한다.
 
# 5. 스프링 MVC - 구조 이해

### 스프링 MVC 전체 구조

** 직접 만든 MVC 프레임워크 구조

![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/d16a4d66-db8f-4ccd-92b6-5b4a199060d7)

- DispatcherServlet 구조
    - `org.springframework.web.servlet.DispatcherServlet`
    - 스프링 MVC도 프론트 컨트롤러 패턴으로 구현되어 있다. ⇒ DispatcherServlet !!!
    - DispatcherServlet이 스프링 MVC의 핵심이다.

- DispatcherServlet 등록
    - DispatcherServlet도 부모 클래스에서 HttpServlet을 상속 받아서 사용하고, 서블릿으로 동작한다.
        - DispatcherServlet → FrameworkServlet → HttpServletBean → HttpServlet
    - 스프링 부트는 DispatcherServlet을 서블릿으로 자동 등록하면서 모든 경로(urlPatterns = “/”)에 대해서 매핑한다. (더 자세한 경로가 우선순위가 높다. 그래서 기존에 등록한 서블릿도 함께 동작한다.)

- 요청 흐름
    - 서블릿이 호출되면 `HttpServlet`이 제공하는 `service()`가 호출된다.
    - 스프링 MVC는 `DispatcherServlet`의 부모인 `FrameworkServlet`에서 `service()`를 오버라이드 해두었다.
    - `FrameworkServlet.service()`를 시작으로 여러 메서드가 호출되면서 `DispatcherServlet.doDispatch()`가 호출된다. ⭐️⭐️⭐️
        
        ```java
        protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
        		HttpServletRequest processedRequest = request;
        		HandlerExecutionChain mappedHandler = null;
        		ModelAndView mv = null;
        
        		// 1. 핸들러 조회
        		mappedHandler = getHandler(processedRequest); 
        		if (mappedHandler == null) {
                noHandlerFound(processedRequest, response);
        				return; 
        		}
        
        		// 2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
        		HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
        
        		// 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환 mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
             processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
        }
        
        private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
        			 HandlerExecutionChain mappedHandler, ModelAndView mv,
        			 Exception exception) throws Exception {
        		// 뷰 렌더링 호출
        		render(mv, request, response);
        }
        
        protected void render(ModelAndView mv, HttpServletRequest request,
        			HttpServletResponse response) throws Exception {
        		View view;
        		String viewName = mv.getViewName(); 
        
        		// 6. 뷰 리졸버를 통해서 뷰 찾기, 7. View 반환
        		view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
        
        		// 8. 뷰 렌더링
        		view.render(mv.getModelInternal(), request, response);
        }
        
        ```
        

- 스프링 MVC 구조
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/5ff89fde-3437-49e8-9175-6f37b76d1965)
    
    1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
    2. 핸들러 어댑터 조회: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
    3. 핸들러 어댑터 실행: 핸들러 어댑터를 실행한다.
    4. 핸들러 실행: 핸들러 어댑터가 실제 핸들러를 실행한다.
    5. ModelAndView 반환: 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
    6. viewResolver 호출: 뷰 리졸버를 찾고 실행한다. 
        - JSP의 경우: `InternalResourceViewResolver` 가 자동 등록되고, 사용된다.
    7. View 반환: 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
        - JSP의 경우 `InternalResourceView(JstlView)` 를 반환하는데, 내부에 `forward()` 로직이 있다.
    8. 뷰 렌더링: 뷰를 통해서 뷰를 렌더링 한다.

- 인터페이스
    - 스프링 MVC의 큰 강점은 `DispatcherServlet` 코드의 변경 없이, 원하는 기능을 변경하거나 확장할 수 있다는 점이다. 지금까지 설명한 대부분을 확장 가능할 수 있게 인터페이스로 제공한다.
    - 이 인터페이스들만 구현해서 `DispatcherServlet` 에 등록하면 직접 커스텀하여 컨트롤러를 만들 수도 있다.
    - 목록
        - 핸들러 매핑: `org.springframework.web.servlet.HandlerMapping`
        - 핸들러 어댑터: `org.springframework.web.servlet.HandlerAdapter`
        - 뷰 리졸버: `org.springframework.web.servlet.ViewResolver`
        - 뷰: `org.springframework.web.servlet.View`

### 핸들러 매핑과 핸들러 어댑터

- Controller 인터페이스
    
    ```java
    package hello.servlet.web.springmvc.old;
    
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.springframework.stereotype.Component;
    import org.springframework.web.servlet.ModelAndView;
    import org.springframework.web.servlet.mvc.Controller;
    
    @Component("/springmvc/old-controller")
    public class OldController implements Controller {
    
        @Override
        public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
            System.out.println("OldController.handleRequest");
            return null;
        }
    
    }
    ```
    
    - `@Component`: 이 컨트롤러는 `/springmvc/old-controller`라는 이름의 스프링 빈으로 등록되었다.
    - 빈의 이름으로 URL을 매핑할 것이다.
    - 이 컨트롤러가 호출되려면 다음 2가지가 필요하다.
        - HandlerMapping
            - 핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다.
            - 스프링 부트가 자동 등록하는 핸들러 매핑
                - 0순위 : RequestMappingHandlerMapping (애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용)
                - 1순위 : BeanNameUrlHandlerMapping (스프링 빈의 이름으로 핸들러를 찾는다.)
            - 예) 스프링 빈의 이름으로 핸들러를 찾을 수 있는 핸들러 매핑이 필요하다.
        - HandlerAdapter
            - 핸들러 매핑을 통해서 찾은 핸들러를 실행할 수 있는 핸들러 어댑터가 필요하다.
            - 스프링 부트가 자동 등록하는 핸들러 어댑터
                - 0순위 : RequestMappingHandlerAdapter (애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용)
                - 1순위 : HttpRequestHandlerAdapter (HttpRequestHandler 처리)
                - 2순위 : SimpleControllerHandlerAdapter (Controller 인터페이스 처리)
            - 예) Controller 인터페이스를 실행할 수 있는 핸들러 어댑터를 찾고 실행해야 한다.
    - 동작 과정
        1. 핸들러 매핑으로 핸들러 조회
            1. `HandlerMapping`을 순서대로 실행해서 핸들러를 찾는다.
            2. 이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 `BeanNameUrlHandlerMapping`이 실행에 성공하고 핸들러인 `OldController`를 반환한다.
        2. 핸들러 어댑터 조회
            1. `HandlerAdapter`의 `supports()`를 순서대로 호출한다.
            2. `SimpleControllerHandlerAdapter`가 `Controller` 인터페이스를 지원하므로 대상이 된다.
        3. 핸들러 어댑터 실행
            1. DispatcherServlet이 조회한 `SimpleControllerHandlerAdapter`를 실행하면서 핸들러 정보도 함께 넘겨준다.
            2. `SimpleControllerHandlerAdapter`는 핸들러인 `OldController`를 내부에서 실행하고, 그 결과를 반환한다.

- HttpRequestHandler
    
    ```java
    package hello.servlet.web.springmvc.old;
    
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.springframework.stereotype.Component;
    import org.springframework.web.HttpRequestHandler;
    
    import java.io.IOException;
    
    @Component("/springmvc/request-handler")
    public class MyHttpRequestHandler implements HttpRequestHandler {
        
        @Override
        public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("MyHttpRequestHandler.handleRequest");
        }
        
    }
    ```
    
    1. 핸들러 매핑으로 핸들러 조회
        1. `HandlerMapping`을 순서대로 실행해서 핸들러를 찾는다.
        2. 이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 `BeanNameUrlHandlerMapping`이 실행에 성공하고 핸들러인 `OldController`를 반환한다.
    2. 핸들러 어댑터 조회
        1. `HandlerAdapter`의 `supports()`를 순서대로 호출한다.
        2. `HttpRequestHandlerAdapter`가 `HttpRequestHandler` 인터페이스를 지원하므로 대상이 된다.
    3. 핸들러 어댑터 실행
        1. DispatcherServlet이 조회한 `HttpRequestHandlerAdapter`를 실행하면서 핸들러 정보도 함께 넘겨준다.
        2. `HttpRequestHandlerAdapter`는 핸들러인 `MyHttpRequestHandler`를 내부에서 실행하고, 그 결과를 반환한다.

### 뷰 리졸버

- OldController → View 조회할 수 있도록 변경
    
    ```java
    package hello.servlet.web.springmvc.old;
    
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.springframework.stereotype.Component;
    import org.springframework.web.servlet.ModelAndView;
    import org.springframework.web.servlet.mvc.Controller;
    
    @Component("/springmvc/old-controller")
    public class OldController implements Controller {
    
        @Override
        public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
            System.out.println("OldController.handleRequest");
            return new ModelAndView("new-form");
        }
    
    }
    ```
    
    - application.properties에 prefix, suffix 추가
        
        ```
        spring.mvc.view.prefix=/WEB-INF/views/
        spring.mvc.view.suffix=.jsp
        ```
        
    - 스프링 부트는 `InternalResourceViewResolver`라는 뷰 리졸버를 자동으로 등록하는데, 이때
    application.properties에 등록한 `spring.mvc.view.prefix`, `spring.mvc.view.suffix` 설정 정보를 사용해서 등록한다. (전체 경로를 주어도 동작하기는 한다. - 권장 X)
    - 스프링 부트가 자동 등록하는 뷰 리졸버
        - 1순위 : BeanNameViewResolver (빈 이름으로 뷰를 찾아서 반환한다.)
        - 2순위 : InternalResourceViewResolver (JSP를 처리할 수 있는 뷰를 반환한다.)
    - 동작 과정
        1. 핸들러 어댑터 호출
            1. 핸들러 어댑터를 통해 `new-form` 이라는 논리 뷰 이름을 획득한다.
        2. ViewResolver 호출
            1. `new-form`이라는 뷰 이름으로 viewResolver를 순서대로 호출한다.
            2. `BeanNameViewResolver`는 `new-form`이라는 이름의 스프링 빈으로 등록된 뷰를 찾아야 하는데 없다.
            3. `InternalResourceViewResolver`가 호출된다.
        3. InternalResourceViewResolver
            1. 이 뷰 리졸버는 `InternalResourceView` 를 반환한다.
        4. 뷰 - InternalResourceView
            1. `InternalResourceView` 는 JSP처럼 포워드 `forward()` 를 호출해서 처리할 수 있는 경우에 사용한다.
        5. view.render()
            1. `view.render()` 가 호출되고 `InternalResourceView` 는 `forward()` 를 사용해서 JSP를 실행한다.

### 스프링 MVC - 시작하기

- @Controller
    - 스프링이 자동으로 스프링 빈으로 등록한다. (내부에 `@Component` 애노테이션이 있어서 컴포넌트 스캔의 대상이 된다.)
    - 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식한다.

- @RequestMapping
    - RequestMappingHandlerMapping
        - 스프링 빈 중에서 `@Controller`가 클래스 레벨에 붙어 있는 경우에 매핑 정보로 인식한다.
        - 스프링 부트 3.0 이상부터는 클래스 레벨에 `@RequestMapping`이 있어도 스프링 컨트롤러로 인식하지 않는다.
    - RequestMappingHandlerAdapter
    - 요청 정보를 매핑한다. 해당 URL이 호출되면 이 메서드가 호출된다. 애노테이션을 기반으로 동작하기 때문에, 메서드의 이름은 임의로 지으면 된다.

- 지금까지 만들었던 프레임워크에서 사용했던 컨트롤러를 `@RequestMapping` 기반의 스프링 MVC 컨트롤러로 변경해 보자.

### 스프링 MVC - 컨트롤러 통합

- `@RequestMapping` 을 잘 보면 클래스 단위가 아니라 메서드 단위에 적용된 것을 확인할 수 있다. 따라서 컨트롤러 클래스를 유연하게 하나로 통합할 수 있다.
    
    ```java
    package hello.servlet.web.springmvc.v2;
    
    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;
    
    import java.util.List;
    
    @Controller
    public class SpringMemberControllerV2 {
    
        private MemberRepository memberRepository = MemberRepository.getInstance();
    
        @RequestMapping("/springmvc/v2/members/new-form")
        public ModelAndView newForm() {
            return new ModelAndView("new-form");
        }
    
        @RequestMapping("/springmvc/v2/members/save")
        public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {
            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));
    
            Member member = new Member(username, age);
            memberRepository.save(member);
    
            ModelAndView mv = new ModelAndView("save-result");
            mv.addObject("member", member);
            return mv;
        }
    
        @RequestMapping("/springmvc/v2/members")
        public ModelAndView members() {
            List<Member> members = memberRepository.findAll();
            ModelAndView mv = new ModelAndView("members");
            mv.addObject("members", members);
    
            return mv;
        }
    
    }
    ```
    

- 조합
    
    ```java
    @Controller
    @RequestMapping("/springmvc/v2/members")
    public class SpringMemberControllerV2 {
    
        private MemberRepository memberRepository = MemberRepository.getInstance();
    
        @RequestMapping("/new-form")
        public ModelAndView newForm() {
            ...
        }
    
        @RequestMapping("/save")
        public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {
            ...
        }
    
        @RequestMapping
        public ModelAndView members() {
            ...
        }
    
    }
    ```
    
    - `/springmvc/v2/members` 중복 → 클래스 레벨에 `@RequestMapping`을 두어 메서드 레벨과 조합하자.

### 스프링 MVC - 실용적인 방식

- 스프링 MVC는 개발자가 편리하게 개발할 수 있도록 수많은 편의 기능을 제공한다.
    - Model 파라미터
    - ViewName 직접 반환 (String)
        - 스프링이 알아서 문자열을 뷰 이름으로 인식한다.
    - @RequestParam 사용
        - 스프링은 HTTP 요청 파라미터를 `@RequestParam` 으로 받을 수 있다.
    - @GetMapping, @PostMapping
        - `@RequestMapping(value = "/new-form", method = RequestMethod.GET)`
        - 이것을 `@GetMapping` , `@PostMapping` 으로 더 편리하게 사용할 수 있다.

# 6. 스프링 MVC - 기본 기능

### 프로젝트 생성

- Packaging: Jar
    - Jar를 사용하면 항상 내장 서버(톰캣 등)을 사용하고, webapp 경로도 사용하지 않는다.
    - War를 사용하면 내장 서버도 사용 가능하지만, 주로 외부 서버에 배포하는 목적으로 사용한다.
- Dependencies: Spring Web, Thymeleaf, Lombok

### 로깅 간단히 알아보기

- 운영 시스템에서는 `System.out.println()` 같은 시스템 콘솔을 사용해서 필요한 정보를 출력하지 않고, 별도의 로깅 라이브러리를 사용해서 로그를 출력한다.
    - 스프링 부트 라이브러리를 사용하면 스프링 부트 로깅 라이브러리( `spring-boot-starter-logging` )가 함께 포함된다.
        - SLF4J - [http://www.slf4j.org](http://www.slf4j.org/)
        - Logback - [http://logback.qos.ch](http://logback.qos.ch/)
    - 로그 라이브러리는 Logback, Log4J, Log4J2 등등 수 많은 라이브러리가 있는데, 그것을 통합해서 인터페이스로 제공하는 것이 바로 SLF4J 라이브러리다. 쉽게 이야기해서 SLF4J는 인터페이스이고, 그 구현체로 Logback 같은 로그 라이브러리를 선택하면 된다.

- 로그 선언 및 호출
    
    ```java
    package hello.springmvc.basic;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class LogTestController {
    
        private final Logger log = LoggerFactory.getLogger(getClass()); // LogTestController.class
    
        @RequestMapping("/log-test")
        public String logTest() {
            String name = "Spring";
    
            System.out.println("name = " + name);
            log.info(" info log={}", name);
    
            return "ok";
        }
    
    }
    ```
    
    - @RestController
        - `@Controller`는 반환 값이 String이면 뷰 이름으로 인식된다. 그래서 뷰를 찾고 뷰가 랜더링 된다.
        - `@RestController`는 반환 값으로 뷰를 찾는 것이 아니라, HTTP 메시지 바디에 바로 입력한다. 따라서 실행 결과로 ok 메시지를 받을 수 있다.
    - 로그 선언
        - `private Logger log = LoggerFactory.getLogger(getClass());`
        - `@Slf4j` : 롬복 사용 가능
    - 로그 출력
        - `log.debug("data={}", data)`
        - 시간, 로그 레벨, 프로세스 ID, 쓰레드 명, 클래스명, 로그 메시지
        - 로그 레벨 설정 (application.properties)
            
            ```
            # hello.springmvc 패키지와 그 하위 로그 레벨 설정
            logging.level.hello.springmvc = trace
            ```
            
            - LEVEL : `TRACE > DEBUG > INFO > WARN > ERROR`
            - 개발 서버는 debug 출력
            - 운영 서버는 info 출력

- 로그 사용 시 장점
    - 스레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.
    - 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영 서버에서는 출력하지 않는 등 로그를 상황에 맞게 조절할 수 있다.
    - 시스템 아웃 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등, 로그를 별도의 위치에 남길 수 있다. 특히 파일로 남길 때는 일별, 특정 용량에 따라 로그를 분할하는 것도 가능하다.
    - 일반 System.out보다 성능이 좋다. (내부 버퍼링, 멀티 스레드 등등)

** 스프링 부트가 제공하는 로그 기능 - [https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-logging](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-logging)

### 요청 매핑

> @RequestMapping은 대부분의 속성을 배열로 제공하므로 다중 설정이 가능하다. 
`{”/hello-basic”, “/hello-go”}`
> 

- HTTP 메서드
    - `@RequestMapping` 에 `method` 속성으로 HTTP 메서드를 지정하지 않으면 HTTP 메서드와 무관하게 호출된다. (GET, HEAD, POST, PUT, PATCH, DELETE 모두 허용)
    - 만약 method 속성으로 GET 지정 후 POST 요청을 하면, 스프링 MVC는 `HTTP 405 상태코드(Method Not Allowed)`를 반환한다.

- Path Variable
    - 최근 HTTP API는 다음과 같이 리소스 경로에 식별자를 넣는 스타일을 선호한다.
        - `/mapping/userA`
        - `/users/1`
    - `@RequestMapping`은 URL 경로를 템플릿화 할 수 있는데, `@PathVariable`을 사용하면 매칭 되는 부분을 편리하게 조회할 수 있다.

- 특정 파라미터 조건 매핑
    - 특정 파라미터가 있거나 없는 조건을 추가할 수 있다.

- 특정 헤더 조건 매핑
    - 특정 HTTP 헤더가 있거나 없는 조건을 추가할 수 있다.

- 미디어 타입 조건 매핑 - HTTP 요청 Content-Type, consume
    - HTTP 요청의 Content-Type 헤더를 기반으로 미디어 타입으로 매핑한다.
    - 만약 맞지 않으면 HTTP 415 상태코드(Unsupported Media Type)을 반환한다.

- 미디어 타입 조건 매핑 - HTTP 요청 Accept, produce
    - HTTP 요청의 Accept 헤더를 기반으로 미디어 타입으로 매핑한다.
    - 만약 맞지 않으면 HTTP 406 상태코드(Not Acceptable)을 반환한다.

### 요청 매핑 - API 예시

- 회원 관리 API
    - 회원 목록 조회 **GET** `/users`
    - 회원 등록 **POST** `/users`
    - 회원 조회 **GET** `/users/{userId}`
    - 회원 수정 **PATCH** `/users/{userId}`
    - 회원 삭제 **DELETE** `/users/{userId}`

### HTTP 요청 - 기본, 헤더 조회

- HTTP 헤더 정보 조회
    
    ```java
    package hello.springmvc.basic.request;
    
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.http.HttpMethod;
    import org.springframework.util.MultiValueMap;
    import org.springframework.web.bind.annotation.CookieValue;
    import org.springframework.web.bind.annotation.RequestHeader;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.util.Locale;
    
    @Slf4j
    @RestController
    public class RequestHeaderController {
    
        @RequestMapping("/headers")
        public String headers(HttpServletRequest request,
                              HttpServletResponse response,
                              HttpMethod httpMethod,
                              Locale locale,
                              @RequestHeader MultiValueMap<String, String> headerMap,
                              @RequestHeader("host") String host,
                              @CookieValue(value = "myCookie", required = false) String cookie) {
    
            log.info("request={}", request);
            log.info("response={}", response);
            log.info("httpMethod={}", httpMethod);
            log.info("locale={}", locale);
            log.info("headerMap={}", headerMap);
            log.info("header host={}", host);
            log.info("myCookie={}", cookie);
            return "ok";
    
        }
    
    }
    ```
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/83f24572-cf20-4465-b476-5818cbde57bb)
    
    - `HttpServletRequest`
    - `HttpServletResponse`
    - `HttpMethod`: HTTP 메서드 조회
    - `Locale`: Locale 정보 조회
    - `@RequestHeader MultiValueMap<String, String> headerMap`
        - 모든 HTTP 헤더를 MultiValueMap 형식으로 조회
    - `@RequestHeader("host") String host`
        - 특정 HTTP 헤더 조회
        - 속성
            - 필수 값 여부: `required`
            - 기본 값 속성: `defaultValue`
    - `@CookieValue(value = "myCookie", required = false) String cookie`
        - 특정 쿠키 조회
        - 속성
            - 필수 값 여부: `required`
            - 기본 값 속성: `defaultValue`
    - `MultiValueMap`: MAP과 유사한데, 하나의 키에 여러 값을 받을 수 있다. HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용한다.

** @Controller의 사용 가능한 파라미터 목록 - [https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-)

** @Controller의 사용 가능한 응답 값 목록 - [https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types)

### HTTP 요청 파라미터 - 쿼리 파라미터, HTML Form

- `HttpServletRequest` 의 `request.getParameter()` 를 사용하면 다음 두가지 요청 파라미터를 조회할 수 있다.
    - GET, 쿼리 파라미터 전송
    - POST, HTML Form 전송

### HTTP 요청 파라미터 - @RequestParam

- 스프링이 제공하는 @RequestParam을 사용하면 요청 파라미터를 매우 편리하게 사용할 수 있다.
    - @RequestParam : 파라미터 이름으로 바인딩
    - @ResponseBody : View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력

- 파라미터 이름 인식 문제 (스프링 부트 3.2 ~)
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/2ea6edbd-322b-4694-9b6b-36ab41f7eb32)

    - 해결방안 1 (권장)
        - 애노테이션에 이름을 생략하지 않고 다음과 같이 이름을 항상 적어준다.
    - 해결방안 2
        - 컴파일 시점에 -parameters 옵션 적용
        1. IntelliJ IDEA에서 File Settings를 연다. (Mac은 IntelliJ IDEA Settings)
        2. Build, Execution, Deployment → Compiler → Java Compiler로 이동한다.
        3. Additional command line parameters라는 항목에 다음을 추가한다. `-parameters`
        4. out 폴더를 삭제하고 다시 실행한다. 꼭 out 폴더를 삭제해야 다시 컴파일이 일어난다.
    - 해결방안 3
        - Gradle을 사용해서 빌드하고 실행한다.

- 파라미터 필수 여부
    - `@RequestParam(required=false)`
    - 기본값은 true
    
- 기본 값 적용
    - 파라미터에 값이 없는 경우 `defaultValue`를 사용하면 기본 값을 적용할 수 있다.
    - 이미 기본 값이 있기 때문에 `required`는 의미가 없다.
    - `defaultValue` 는 빈 문자의 경우에도 설정한 기본 값이 적용된다.

- 파라미터를 Map으로 조회하기
    - `@RequestParam Map`
        - `Map(key=value)`
    - `@RequestParam MultiValueMap`
        - `MultiValueMap(key=[value1, value2, ...]`
    - 파라미터의 값이 1개가 확실하다면 `Map` 을 사용해도 되지만, 그렇지 않다면 `MultiValueMap` 을 사용하자.

### HTTP 요청 파라미터 - @ModelAttribute

- 실제 개발을 하면 요청 파라미터를 받아서 필요한 객체를 만들고 그 객체에 값을 넣어주어야 한다.
    
    ```java
    @RequestParam String username;
    @RequestParam int age;
    
    HelloData data = new HelloData();
    data.setUsername(username);
    data.setAge(age);
    ```
    

- 스프링은 이 과정을 완전히 자동화해주는 `@ModelAttribute` 기능을 제공한다.
    - 먼저 요청 파라미터를 바인딩 받을 객체를 만들자.
        
        ```java
        package hello.springmvc.basic;
        
        import lombok.Data;
        
        @Data
        public class HelloData {
            
            private String username;
            private int age;
            
        }
        ```
        
        - 롬복 `@Data`
            - `@Getter` , `@Setter` , `@ToString` , `@EqualsAndHashCode` , `@RequiredArgsConstructor` 를 자동으로 적용해준다.
    - `@ModelAttribute` 적용
        
        ```java
        /**
         * @ModelAttribute 사용
         * 참고: model.addAttribute(helloData) 코드도 함께 자동 적용됨
         */
        @ResponseBody
        @RequestMapping("/model-attribute-v1")
        public String modelAttributeV1(@ModelAttribute HelloData helloData) {
            log.info("username={}, age={}", 
                    helloData.getUsername(), helloData.getAge());
            return "ok";
        }
        ```
        
        - 스프링 MVC는 @ModelAttribute가 있으면 다음을 실행한다.
            - HelloData 객체 생성
            - 요청 파라미터의 이름으로 HelloData 객체의 프로퍼티(Getter & Setter)를 찾는다. 그리고 해당 프로퍼티의 setter를 호출해서 파라미터의 값을 입력(바인딩)한다.
        - @ModelAttribute 생략 가능
            - `String` , `int` , `Integer` 같은 단순 타입 = `@RequestParam`
            - 나머지 = `@ModelAttribute` (argument resolver 로 지정해둔 타입 외)

### HTTP 요청 메시지 - 단순 텍스트

- 요청 파라미터와 다르게, HTTP 메시지 바디를 통해 데이터가 직접 넘어오는 경우는 `@RequestParam` , `@ModelAttribute` 를 사용할 수 없다. (HTML Form 형식으로 전달되는 경우는 요청 파라미터로 인정된다.)

- HTTP 메시지 바디의 데이터를 `InputStream` 을 사용해서 직접 읽을 수 있다.
    - 스프링 MVC는 InputStream(Reader), OutputStream(Writer) 파라미터도 지원한다.

- HttpEntity
    - Http header, body 정보를 편리하게 조회
        - 메시지 바디 정보를 직접 조회한다.
        - 요청 파라미터를 조회하는 기능과 관계 없다. `@RequestParam` X, `@ModelAttribute` X
    - 응답에도 사용 가능하다.
        - 메시지 바디 정보 직접 반환
        - 헤더 정보 포함 가능
        - view 조회 X
    - `HttpEntity` 를 상속받은 다음 객체들도 같은 기능을 제공한다.
        - RequestEntity
            - HttpMethod, url 정보가 추가, 요청에서 사용
        - ResponseEntity
            - HTTP 상태 코드 설정 가능, 응답에서 사용
            - `return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED)`

** 스프링MVC 내부에서 HTTP 메시지 바디를 읽어서 문자나 객체로 변환해서 전달해주는데, 이때 HTTP 메시지 컨버터( `HttpMessageConverter` )라는 기능을 사용한다.

- @RequestBody, @ResponseBody
    - `@RequestBody` 를 사용하면 HTTP 메시지 바디 정보를 편리하게 조회할 수 있다. 참고로 헤더 정보가 필요하다면 `HttpEntity` 를 사용하거나 `@RequestHeader` 를 사용하면 된다.
    - `@ResponseBody` 를 사용하면 응답 결과를 HTTP 메시지 바디에 직접 담아서 전달할 수 있다.

### HTTP 요청 메시지 - JSON

- HttpServletRequest 사용
    - 직접 HTTP 메시지 바디에서 데이터를 읽어와서 문자로 변환한다.
    - 문자로 된 JSON 데이터를 Jackson 라이브러리인 `objectMapper` 를 사용해서 자바 객체로 변환한다.

- @RequestBody 객체 변환
    - `@RequestBody HelloData data`
    - `@RequestBody` 에 직접 만든 객체를 지정할 수 있다.
    - `HttpEntity`, `@RequestBody` 를 사용하면 HTTP 메시지 컨버터가 HTTP 메시지 바디의 내용을 우리가 원하는 문자나 객체 등으로 변환해준다.
    - @RequestBody는 생략 불가능!
    - HTTP 요청시에 content-type이 application/json인지 꼭! 확인해야 한다. 그래야 JSON을 처리할 수 있는 HTTP 메시지 컨버터가 실행된다.
    - HttpEntity 사용도 가능하다.

- @ResponseBody
    - 응답의 경우에도 `@ResponseBody`를 사용하면 해당 객체를 HTTP 메시지 바디에 직접 넣어줄 수 있다.
    - HttpEntity 사용도 가능하다.

					

> `@RequestBody` 요청
- JSON 요청 → HTTP 메시지 컨버터 → 객체
`@ResponseBody` 응답
- 객체 → HTTP 메시지 컨버터 → JSON 응답
> 

### HTTP 응답 - 정적 리소스, 뷰 템플릿

- 스프링에서 응답 데이터를 만드는 방법
    - 정적 리소스
        - 예) 웹 브라우저에 정적인 HTML, css, js를 제공할 때
    - 뷰 템플릿 사용
        - 예) 웹 브라우저에 동적인 HTML을 제공할 때
    - HTTP 메시지 사용
        - HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로, HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.

- 정적 리소스
    - 스프링 부트는 클래스패스의 다음 디렉토리에 있는 정적 리소스를 제공한다.
        - `/static` , `/public` , `/resources` ,`/META-INF/resources`
    - `src/main/resources` 는 리소스를 보관하는 곳이고, 또 클래스패스의 시작 경로이다. 따라서 다음 디렉토리에 리소스를 넣어두면 스프링 부트가 정적 리소스로 서비스를 제공한다.
    - 정적 리소스는 해당 파일을 변경 없이 그대로 서비스하는 것이다.

- 뷰 템플릿
    - 뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어서 전달한다.
    - 스프링 부트는 기본 뷰 템플릿 경로를 제공한다.
        - `src/main/resources/templates`
    - 뷰 템플릿을 호출하는 컨트롤러
        - String을 반환하는 경우 - View or HTTP 메시지
            - `@ResponseBody` 가 없으면 `response/hello` 로 뷰 리졸버가 실행되어서 뷰를 찾고, 렌더링 한다.
            - `@ResponseBody` 가 있으면 뷰 리졸버를 실행하지 않고, HTTP 메시지 바디에 직접 `response/hello` 라는 문자가 입력된다.
        - void를 반환하는 경우 (권장 X)
            - `@Controller` 를 사용하고, `HttpServletResponse` , `OutputStream(Writer)` 같은 HTTP 메시지 바디를 처리하는 파라미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 사용한다.

** 스프링 부트의 타임리프 관련 추가 설정 (페이지 안에서 thymeleaf 검색) - [https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-application-properties.html#common-application-properties-templating](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-application-properties.html#common-application-properties-templating)

### HTTP 응답 - HTTP API, 메시지 바디에 직접 입력

- HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.
    - HttpServletResponse 객체 → HTTP 메시지 바디에 직접 ok 메시지 전달
    - ResponseEntity 사용 → HTTP 응답 코드 설정 가능
    - @ResponseBody → view를 사용하지 않고, HTTP 메시지 컨버터를 통해 HTTP 메시지를 직접 입력 가능
    - @ResponseStatus → 응답 코드 설정

- @RestController
    - 해당 컨트롤러에 모두 @ResponseBody가 적용되는 효과가 있다.
    - 따라서 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에 직접 데이터를 입력한다.

### HTTP 메시지 컨버터

- 뷰 템플릿으로 HTML을 생성해서 응답하는 것이 아니라, HTTP API처럼 JSON 데이터를 HTTP 메시지 바디에서 직접 읽거나 쓰는 경우 HTTP 메시지 컨버터를 사용하면 편리하다.
    - 스프링 MVC는 다음의 경우에 HTTP 메시지 컨버터를 적용한다.
        - HTTP 요청: `@RequestBody`, `HttpEntity(RequestEntity)`
        - HTTP 응답: `@ResponseBody`, `HttpEntity(ResponseEntity)`
    - HTTP 메시지 컨버터(인터페이스)는 HTTP 요청, HTTP 응답 둘 다 사용된다.
        - `canRead()`, `canWrite()` : 메시지 컨버터가 해당 클래스, 미디어타입을 지원하는지 체크
        - `read()`, `write()` : 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능

- 스프링 부트 기본 메시지 컨버터
    - 0순위 : ByteArrayHttpMessageConverter
        - 클래스 타입: `byte[]`, 미디어타입: `*/*`
    - 1순위 : StringHttpMessageConverter
        - 클래스 타입: `String`, 미디어타입: `*/*`
    - 2순위 : MappingJackson2HttpMessageConverter
        - 클래스 타입: 객체 또는 `HashMap`, 미디어타입: `application/json` 관련
    - 스프링 부트는 다양한 메시지 컨버터를 제공하는데, 대상 클래스 타입과 미디어 타입 둘을 체크해서 사용여부를 결정한다. 만약 만족하지 않으면 다음 메시지 컨버터로 우선순위가 넘어간다.

- HTTP 요청 데이터 읽기
    - HTTP 요청이 오고, 컨트롤러에서 `@RequestBody` , `HttpEntity` 파라미터를 사용한다.
    - 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 `canRead()`를 호출한다.
        - 대상 클래스 타입을 지원하는가
        - HTTP 요청의 Content-Type 미디어 타입을 지원하는가
    - `canRead()` 조건을 만족하면 `read()`를 호출해서 객체 생성하고, 반환한다.

- HTTP 응답 데이터 생성
    - 컨트롤러에서 `@ResponseBody`, `HttpEntity`로 값이 반환된다.
    - 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 `canWrite()` 를 호출한다.
        - 대상 클래스 타입을 지원하는가
        - HTTP 요청의 Accept 미디어 타입을 지원하는가
        (더 정확히는 `@RequestMapping`의 `produces`)
    - `canWrite()` 조건을 만족하면 `write()` 를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다.

### 요청 매핑 핸들러 어댑터 구조

- RequestMappingHandlerAdapter 동작 방식
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/11f73bb7-81b2-4a0e-9c61-e3033bbfcdcf)
  
    - ArgumentResolver
        - 애노테이션 기반 컨트롤러를 처리하는 `RequestMappingHandlerAdapter` 는 `ArgumentResolver`를 호출해서 컨트롤러(핸들러)가 필요로 하는 다양한 파라미터의 값(객체)을 생성한다. 그리고 파라미터의 값이 모두 준비되면 컨트롤러를 호출하면서 값을 넘겨준다.
        - `ArgumentResolver`의 `supportsParameter()`를 호출해서 해당 파라미터를 지원하는지 체크하고, 지원하면 `resolveArgument()`를 호출해서 실제 객체를 생성한다. 그리고 이렇게 생성된 객체가 컨트롤러 호출시 넘어가는 것이다.
        
        ** 가능한 파라미터 목록 - [https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments)
        
    - ReturnValueHandler
        - 응답 값을 변환하고 처리한다.
        
        ** 가능한 응답 값 목록 - [https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types)
        

- HTTP 메시지 컨버터
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/da3f9750-3c97-4059-b8d0-ae2a1d152348)
    
    - HTTP 메시지 컨버터를 사용하는 `@RequestBody` 도 컨트롤러가 필요로 하는 파라미터의 값에 사용된다. `@ResponseBody` 의 경우도 컨트롤러의 반환 값을 이용한다.
    - **요청의 경우** `@RequestBody`를 처리하는 `ArgumentResolver`가 있고, `HttpEntity`를 처리하는`ArgumentResolver`가 있다. 이 `ArgumentResolver`들이 HTTP 메시지 컨버터를 사용해서 필요한 객체를 생성 하는 것이다.
    - **응답의 경우** `@ResponseBody`와 `HttpEntity`를 처리하는 `ReturnValueHandler`가 있다. 그리고 여기에서 HTTP 메시지 컨버터를 호출해서 응답 결과를 만든다.
    - 스프링 MVC는 `@RequestBody` `@ResponseBody` 가 있으면 `RequestResponseBodyMethodProcessor()`를 사용하고, `HttpEntity` 가 있으면 `HttpEntityMethodProcessor()`를 사용한다.

- 기능 확장
    - 스프링은 다음을 모두 인터페이스로 제공한다.
        - `HandlerMethodArgumentResolver`
        - `HandlerMethodReturnValueHandler`
        - `HttpMessageConverter`
    - `WebMvcConfigurer`를 상속 받아서 스프링 빈으로 등록하면 된다.

# 7. 스프링 MVC - 웹 페이지 만들기

### 프로젝트 생성

- Packaging: jar
- Dependencies: Spring Web, Thymeleaf, Lombok

### 요구사항 분석

- 상품 도메인 모델
    - 상품 ID
    - 상품명
    - 가격
    - 수량

- 상품 관리 기능
    - 상품 목록
    - 상품 상세
    - 상품 등록
    - 상품 수정

- 서비스 제공 흐름
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/7e9a75f9-a02d-4ddd-b3fd-8b03571b5bc5)


### 상품 도메인 개발

- 상품 객체 **Item**
- 상품 저장소 **ItemRepository**

### 상품 서비스 HTML

- 부트스트랩
    - [https://getbootstrap.com/docs/5.0/getting-started/download/](https://getbootstrap.com/docs/5.0/getting-started/download/)
    - Compiled CSS and JS 항목 다운로드 → 압축 풀기 → bootstrap.min.css 복사 → resources/static/css/bootstrap.min.css 추가

### 상품 목록 - 타임리프

- BasicItemController
    
    ```java
    package hello.itemservice.web.item.basic;
    
    import hello.itemservice.domain.item.Item;
    import hello.itemservice.domain.item.ItemRepository;
    import jakarta.annotation.PostConstruct;
    import lombok.RequiredArgsConstructor;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    import java.util.List;
    
    @Controller
    @RequestMapping("/basic/items")
    @RequiredArgsConstructor
    public class BasicItemController {
    
        private final ItemRepository itemRepository;
    
        @GetMapping
        public String items(Model model) {
            List<Item> items = itemRepository.findAll();
            model.addAttribute("items", items);
            return "basic/items";
        }
        
        /**
         * 테스트용 데이터 추가
         */
        @PostConstruct
        public void init() {
            itemRepository.save(new Item("testA", 10000, 10));
            itemRepository.save(new Item("testB", 20000, 20));
        }
    
    }
    ```
    
    - `@RequiredArgsConstructor`
        - `final`이 붙은 멤버 변수만 이용해서 생성자를 자동으로 만들어준다.
    - 생성자가 딱 1개만 있으면 스프링이 해당 생성자에 `@Autowired`로 의존관계를 주입해준다. (final 키워드를 빼면 안 된다! 그러면 의존관계 주입 X)
    - 테스트용 데이터 → `@PostConstruct` : 해당 빈의 의존관계가 모두 주입되고 나면 초기화 용도로 호출된다.

- 타임 리프
    - 사용 선언
    `<html xmlns:th="<http://www.thymeleaf.org>">`
    - 속성 변경 - th:href
        - `href="value1"`을 `th:href="value2"`의 값으로 변경한다.
        - 타임리프 뷰 템플릿을 거치게 되면 원래 값을 `th:xxx` 값으로 변경한다. 만약 값이 없다면 새로 생성한다.
        - HTML을 그대로 볼 때는 `href` 속성이 사용되고, 뷰 템플릿을 거치면 `th:href`의 값이 `href`로 대체되면서 동적으로 변경할 수 있다.
        - 따라서 HTML을 파일 보기를 유지하면서 템플릿 기능도 할 수 있다.
        - 대부분의 HTML 속성을 `th:xxx` 로 변경할 수 있다.
    - URL 링크 표현식 - @{..}
        - 타임리프는 URL 링크를 사용하는 경우 `@{...}` 를 사용한다. 이것을 URL 링크 표현식이라 한다.
        - URL 링크 표현식을 사용하면 서블릿 컨텍스트를 자동으로 포함한다.
        - 경로 변수, 쿼리 파라미터 생성 가능
        - `th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}"`
    - 리터럴 대체 표현식 - |..|
        - 타임리프에서 문자와 표현식 등은 분리되어 있기 때문에 더해서 사용해야 한다. 리터럴 대체 문법을 사용하면, 더하기 없이 편리하게 사용할 수 있다.
        - 전) `<span th:text="'Welcome to our application, ' + ${user.name} + '!'">`
        - 후) `<span th:text="|Welcome to our application, ${user.name}!|">`
    - 반복 출력 - th:each
        - 반복은 `th:each`를 사용한다. 이렇게 하면 모델에 포함된 `items` 컬렉션 데이터가 `item` 변수에 하나씩 포함되고, 반복문 안에서 `item` 변수를 사용할 수 있다.
        - `<tr th:each="item : ${items}">`
    - 변수 표현식 - ${..}
        - 모델에 포함된 값이나, 타임리프 변수로 선언한 값을 조회할 수 있다.
        - 프로퍼티 접근법을 사용한다. (item.getPrice())
    - 내용 변경 - th:text
        - 내용의 값을 `th:text`의 값으로 변경한다.

** 순수 HTML을 그대로 유지하면서 뷰 템플릿도 사용할 수 있는 타임리프의 특징을 **네츄럴 템플릿**이라고 한다.

### 상품 상세

- BasicItemController
    
    ```java
    @GetMapping("/{itemId}")
    public String item(@PathVariable("itemId") long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "basic/item";
    }
    ```
    
    - `PathVariable` 로 넘어온 상품ID로 상품을 조회하고, 모델에 담아둔다. 그리고 뷰 템플릿을 호출한다.

### 상품 등록 폼

- BasicItemController
    
    ```java
    @GetMapping("/add")
    public String addForm() {
        return "basic/addForm";
    }
    ```
    
    - 단순히 뷰 템플릿만 호출한다.

- 타임리프
    - `th:action`
        - HTML form에서 `action`에 값이 없으면 현재 URL에 데이터를 전송한다.
        - 상품 등록 폼의 URL과 실제 상품 등록을 처리하는 URL을 똑같이 맞추고 HTTP 메서드로 두 기능을 구분한다.
            - 상품 등록 폼: **GET** `/basic/items/add`
            - 상품 등록 처리: **POST** `/basic/items/add`

### 상품 등록 처리 - @ModelAttribute

- BasicItemController
    - v1
        
        ```java
        @PostMapping("/add")
        public String addItemV1(@RequestParam("itemName") String itemName,
                                @RequestParam("price") int price,
                                @RequestParam("quantity") Integer quantity,
                                Model model) {
            Item item = new Item();
            item.setItemName(itemName);
            item.setPrice(price);
            item.setQuantity(quantity);
        
            itemRepository.save(item);
            model.addAttribute("item", item);
        
            return "basic/item";
        }
        ```
        
        - `@RequestParam`을 통해 요청 파라미터 데이터를 받는다.
        - `Item` 객체를 생성하고 `itemRepository` 를 통해서 저장한다.
        - 저장된 `item`을 모델에 담아서 뷰에 전달한다.
    - v2
        
        ```java
        @PostMapping("/add")
        public String addItemV2(@ModelAttribute("item") Item item) {
            itemRepository.save(item);
            return "basic/item";
        }
        ```
        
        - `@ModelAttribute`
            - `Item` 객체를 생성하고, 요청 파라미터의 값을 프로퍼티 접근법으로 입력해준다.
            - `Model`에 `@ModelAttribute`로 지정한 객체를 자동으로 넣어준다. 모델에 담을 때는 이름이 필요하다. 이름은 `@ModelAttribute`에 지정한 `name(value)` 속성을 사용한다.

### 상품 수정

- BasicItemController
    - 상품 수정 폼
        
        ```java
        @GetMapping("/{itemId}/edit")
        public String editForm(@PathVariable("itemId") Long itemId, Model model) {
            Item item = itemRepository.findById(itemId);
            model.addAttribute("item", item);
            return "basic/editForm";
        }
        ```
        
        - 수정에 필요한 정보를 조회하고, 수정용 폼 뷰를 호출한다.
    - 상품 수정
        
        ```java
        @PostMapping("{itemId}/edit")
        public String edit(@PathVariable("itemId") Long itemId, @ModelAttribute("item") Item item) {
            itemRepository.update(itemId, item);
            return "redirect:/basic/items/{itemId}";
        }
        ```
        
        - `redirect:/basic/items/{itemId}`
            - 상품 수정은 마지막에 뷰 템플릿을 호출하는 대신에 상품 상세 화면으로 이동하도록 리다이렉트를 호출한다.
            - 스프링은 `redirect:/...` 으로 편리하게 리다이렉트를 지원한다.
            - 컨트롤러에 매핑된 `@PathVariable` 의 값은 `redirect` 에도 사용 할 수 있다.
            - `redirect:/basic/items/{itemId}` `{itemId}` 는 `@PathVariable Long itemId` 의 값 을 그대로 사용한다.

** HTML Form 전송은 PUT, PATCH를 지원하지 않는다. GET, POST만 사용할 수 있다. 스프링에서 HTTP POST로 Form 요청할 때 히든 필드를 통해서 PUT, PATCH 매핑을 사용하는 방법이 있지만, HTTP 요청상 POST 요청이다.

### PRG Post/Redirect/Get

- 문제
    - 상품 등록을 완료하고 웹 브라우저의 새로고침 버튼을 클릭해보자.
    - 상품이 계속해서 중복 등록되는 것을 확인할 수 있다.

- POST 등록 후 새로고침
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/8c213252-3cb9-4dcd-aa1d-a7a18f033af8)
    
    - 웹 브라우저의 새로 고침은 마지막에 서버에 전송한 데이터를 다시 전송한다.
    - 상품 등록 폼에서 데이터를 입력하고 저장을 선택하면 `POST /add + 상품 데이터`를 서버로 전송한다. 이 상태에서 새로 고침을 또 선택하면 마지막에 전송한 `POST /add + 상품 데이터`를 서버로 다시 전송하게 된다.
    - 그래서 내용은 같고, ID만 다른 상품 데이터가 계속 쌓이게 된다.

- 해결 방법 - POST, Redirect GET
    
    ![image](https://github.com/Springdingdongrami/spring-mvc-1/assets/66028419/37ca4748-7496-4525-af08-fc5d90c67a30)
    
    - 새로 고침 문제를 해결하려면 상품 저장 후에 뷰 템플릿으로 이동하는 것이 아니라, 상품 상세 화면으로 리다이렉트를 호출해주면 된다.
    - 웹 브라우저는 리다이렉트의 영향으로 상품 저장 후에 실제 상품 상세 화면으로 다시 이동한다. 따라서 마지막에 호출한 내용이 상품 상세 화면인 `GET /items/{id}`가 되는 것이다.
    - 이후 새로고침을 해도 상품 상세 화면으로 이동하게 되므로 새로 고침 문제를 해결할 수 있다.

- BasicItemController
    
    ```java
    @PostMapping("/add")
    public String addItemV5(Item item) {
        itemRepository.save(item);
        return "redirect:/basic/items/" + item.getId();
    }
    ```
    
    - 상품 등록 처리 이후에 뷰 템플릿이 아니라 상품 상세 화면으로 리다이렉트 하도록 코드를 작성한다.
    - 이런 문제 해결 방식을 `PRG Post/Redirect/Get` 라 한다.

### RedirectAttributes

- RedirectAttrtibutes
    - URL 인코딩
    - pathVariable, 쿼리 파라미터 처리

- 상품 저장 → 상품 상세 화면에 “저장되었습니다” 메시지 추가
    - BasicItemController
        
        ```java
        @PostMapping("/add")
        public String addItemV6(Item item, RedirectAttributes redirectAttributes) {
            Item savedItem = itemRepository.save(item);
            redirectAttributes.addAttribute("itemId", savedItem.getId());
            redirectAttributes.addAttribute("status", true);
            return "redirect:/basic/items/{itemId}";
        }
        ```
        
        - 리다이렉트 할 때 간단히 `status=true` 를 추가해보자.
    - 실행해보면 다음과 같은 리다이렉트 결과가 나온다. 
    `http://localhost:8080/basic/items/3?status=true`
        - pathVariable 바인딩: `{itemId}`
        - 나머지는 쿼리 파라미터로 처리: `?status=true`
    - 뷰 템플릿 메시지 추가
        
        `<h2 th:if="${param.status}" th:text="'저장 완료'"></h2>`
        
        - `th:if` : 해당 조건이 참이면 실행
        - `${param.status}` : 타임리프에서 쿼리 파라미터를 편리하게 조회하는 기능
