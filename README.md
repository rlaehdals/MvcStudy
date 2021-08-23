### MvcStudy
---
## spring setting
1. spring boot version 2.4.3
2. java 11
3. war


## dependency
1. web
2. lombok
3. tomcat
4. servlet
5. jasper

>postman 설치하여 test 진행


## application.yml setting
```
logging:
  level:
    org.apache.coyote.http11 = debug
spring:
  mvc:
    view:
      prefix= /WEB-INF/views/   -> 경로지정
      suffix = .jsp     -> 파일명 지정
```

## 서블릿 등록하기

```

@ServletComponentScan
@SpringBootApplication
public class ServletApplication{
  
  public static void main(String[] args){
    SpringApplication.run(ServletApplication.class, args);
  }
}
```
> @ServletComponentScan를 활용하여 서블릿을 자동 등록한다. ServletApplication의 위치는 최상위 디렉터리이므로 하위 디렉터리에 적용된다. 

```
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
  public class HelloServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      System.out.println("HelloServlet.service"); System.out.println("request = " + request);
      System.out.println("response = " + response);
      String username = request.getParameter("username");
      System.out.println("username = " + username);
      response.setContentType("text/plain");
      response.setCharacterEncoding("utf-8");
      response.getWriter().write("hello " + username);
    }
}
```
>@WebServlet(name = "helloServlet", urlPatterns= "/hello")-> 서블릿 이름 설정, url을 매핑한다. HTTP요청을 통해 URL이 호출되면 service함수를 실행한다. 

## HttpServletRequest
> 서블릿은 개발자를 대신해서 HTTP요청 메시지를 파싱하여 HttpServletRequest객체에 담아서 제공한다.
1. http 요청 메시지
```
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
username=kim&age=20
```
  * START LINE
    * HTTP 메소드
    * URL
    * 쿼리 스트링
    * 스키마, 프로토콜
  * 헤더
    * 헤더 조회
  * 바디
    * form 파라미터 형식 조회
    * message body 데이터 직접 조회

2. 임시 저장소 기능
```
request.setAttribute(name, value) //저장
request.getAttribute(name)        // 조회
```
3. 세션 관리 기능
```
request.getSession(create: true) 
```

4. 사용법 
```

```

















