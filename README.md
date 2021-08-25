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
  * start lie 정보
```
request.getMethod() -> GET, POST 등등
request.getProtocal() -> HTTP/1.1
request.getScheme() -> http
request.getRequestURL() -> http://localhost:8080/request-header
request.getReqeustURI() -> request-header
request.getQueryString() -> ?username=hello
request.isSecure() -: false
```
  * header 정보
```
request.getHeaderNames().asIterator().forEachRemaining(headerName -> request.getHeader(headerName)
```

  * 편리한 정보
```
request.getLocales()
request.getCookies()
request.getContentType()
request.getContentLength()
```

## HTTP 요청 데이터 - 개요
  * 쿼리 파라미터
    * /url?useranme=hello&age=20
    * 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
    * ?로 시작하고 &로 여러개를 보낼 수 있다. 
  * HTML FORM
    * content-Type: application/x-www-form-urlencoded
    * 메시지 바디에 쿼리 파라미터 형식으로 전달 username=hello&age=20
  * HTTP message body 
    * 데이터를 직접 담아서 요청
    * HTTP API에서 주로 사용 JSON, XML TEXT

## HttpServletResponse
1. 역할
  * HTTP 응답코드 지정
  * 헤더 생성
  * 바디 생성
2. 편의 기능
  * Content-Type, 쿠키, Redirect
```
response.setStatus(HttpServlerResponse.SC_OK)
response.setHeader("name", "name")

Cookie cookie = new Cookie( "myCookie", "good")
cookie.setMaxAge(700)
response.setCookie(cookie)
response.sendRedirect("/basic/hello-form.html")
```

## 서블릿, JSP, MVC패턴
> dependency 추가 
> implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
> implementation 'javax.servlet:jstl'
> gradle 다시 빌드

1. 서블릿 + jsp 특징
  * 자바 코드를 부분 입력이 가능하여 HTML 중간에 자바 코드를 출력할 수 있다. 
  1. 서블릿 + jsp의 한계 
    * JAVA 코드, 데이터를 조회하는 리포지토리 등등 다양한 코드가 모두 JSP에 노출되어 있기 때문에 JSP이 역할이 너무 무겁다.
    > 유지보수가 어려운 단점이 있다.


2. MVC 패턴 등장
> Modle View Controller
  * Controller
  > HTTP 요청을 받아서 파라미터를 검증하고 비즈니스 로직을 실행한다. 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
  * Modle
  > 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달하므로 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 된다
  * 뷰
  > 모델에 담겨 있는 데이터를 사용해서 화면을 그리는 일을한다. 

## MVC 패턴 예시

```
@WebServlet(name= "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
public class MvcMemberFormServlet extends HttpServlet{
  
  @Override
  protected void service( HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
    
    
    String viewPath = "/WEB-INF/views/new-form.jsp";
    ReqeustDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);   // 다른 서블릿이나 JSP로 이동할 수 있게 해주는 기능이다.
  }
}
```
> redirect vs forward 
> 리다이렉트는 실제 클라이언트에 응답이 나갔다가 클라이언트가 다시 redirect경로로 요청하는 것이므로 클라이언트 인지가능
> 포워드는 서버 내부에서 일어나는 일이므로 클라이언트는 인지 불가능
> 위 코드의 단점 forward, ViewPath가 중복된다. 
> MVC 프레임워크라면 단점이 해결된다.

## 스프링 MVC 구조
1. HTTP 요청
2. DispatcherServlet
3. 핸들러 매핑
4. 핸들러 어댑터 목록
5. 핸들러 어댑터
6. 핸들러(컨트롤러) 호출
7. 핸들러 어댑터 Model and View 반환
8. DispatcherServlet ViewResolver 호출
9. ViewResolver View 반환
10. DispatcherServlet render호출
11. View HTML 응답


##최종적인 spring mvc형태
```
@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {
  private MemberRepository memberRepository = MemberRepository.getInstance();
  
  @GetMapping("/new-form")
  public String newForm() {
    return "new-form";
  }
 
 @PostMapping("/save")
 public String save( @RequestParam("username") String username, @RequestParam("age") int age, Model model) {
    Member member = new Member(username, age);
    memberRepository.save(member);
    model.addAttribute("member", member);
    return "save-result"; 
 }
 
 @GetMapping
 public String members(Model model) {
    List<Member> members = memberRepository.findAll();
    model.addAttribute("members", members);
    return "members";
 }
}
```





















































































































