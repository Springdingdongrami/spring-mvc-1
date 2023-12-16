# 1. 웹 애플리케이션 이해

POSTMAN 주소: https://documenter.getpostman.com/view/25554684/2s9Ykn8Mka

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
