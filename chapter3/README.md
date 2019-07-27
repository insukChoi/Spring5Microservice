## 스프링 부트 Restful 마이크로서비스 만들기

스프링 부트는 하나하나 직접 지정해야 하는 환경설정보다는 관례적으로 미리 정해진 접근방식을 사용해서,
애플리케이션 작성에 필요한 기본 코드와 환경설정에 드는 노력을 상당히 많이 줄여준다.

- 스프링부트 스타터 명명규칙
    - spring-boot-starter-*  <br>
    _(* 에 해당 스타터명 명시)_

- 스타터 종류 확인 방법
    - [Spring Boot Reference Guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)
    
- 자동 환경 설정 이해하기
    1) @SpringBootApplication 
        * @SpringBootConfiguration <br>
           : 스프링 부트의 설정을 나타내는 어노테이션.
           스프링의 @Configuration을 대체하며 스프링부트 전용으로 사용.
           
        * @EnableAutoConfiguration <br>
            : 자동 설정의 핵심 어노테이션. 클래스 경로에 지정된 내용을 기반으로 영리하게 설정 자동화를 수행.
            특별한 설정값을 추가하지 않으면 기본값으로 작동.
            ```
            * META-INF/spring.factories
                : 자동 설정 타깃 클래스 목록, 
                즉 이곳에 선언되어 있는 클래스들이 @EnableAutoConfiguration 사용 시 자동 설정 타깃이 됨
                
            * META-INF/spring-configuration-metadata.json
                : 자동 설정에 사용할 프로퍼티 정의 파일,
                미리 구현되어 있는 자동 설정에 프로퍼티만 주입시켜 준다. 따라서 별도의 환경설정이 필요하지 않음.
                
            * org/springframework/boot/autoconfigure
                : 미리 구현해놓은 자동 설정 리스트.
                '{특정 설정의 이름}AutoConfiguration' 형식으로 지정되어 있음.
            ```
        
        * @ComponentScan
            : 특정 패키지 경로를 기반으로 @Configuration에서 사용할 @Component 설정 클래스를 찾음.
            @ComponentScan의 basePackage 프로퍼티값에 별도의 경로를 설정하지 않으면 @ComponentScan이 위치한 패키지가 루트 경로로 설정 됨.
           
## HATEOAS 

Hypermedia As The Engine Of Application State

- 사용 이유 : RESTful API를 사용하는 클라이언트가 전적으로 서버와 동적인 상호작용이 가능하도록 하기 위해여 사용한다.

1. 예제 코드 1: 전형적인 REST API의 응답 데이터
    ```json
    {
      “accountId”:12345,
      “accountType”:”saving”,
      “balance”:350000”,
      “currency”:”KRW”
    }
    ```
1. 예제 코드 2: HATEAOS가 도입되어 자원에 대한 추가 정보가 제공되는 응답 데이터
    ```json
    {
      “accountId”:12345,
      “accountType”:”saving”,
      “balance”:350000”,
      “currency”:”KRW”
      “links”: [
           {
           “rel”: “self”
           “href”: “http://localhost:8080/accounts/1”
           },
           {
           “rel”: “withdraw”,
           “href”: “http://localhost:8080/accounts/1/withdraw”
           },
           {
           “rel”:”transfer”,
             “href”:”http://localhost:8080/accounts/1/transfer”
           }
      ]
    }
    ```

## 리액티브 스프링 부트 마이크로서비스
| 개념 | 의미 |
|---|:---:|
| `sync` | 적제되어있는 프로세스를 순차적으로 실행 |
| `async` | 프로세스를 순차적이지 않게 실행 |
| `blocking` | CPU 점유하는 방식 |
| `non-blocking` | CPU 점유하지 않는 방식 |

**Reactive programming** 은 비동기 프로세스를 동작하는 이벤트 기반의 non-blocking 애플리케이션을 구현하는 프로그래밍.

1. 스프링 웹플럭스를 활용 <br>
자바 리액티브 프로그래밍은 리액티브 스트림 명세를 바탕으로 함. <br>
리액티브 스트림 명세는 따로 떨어져 있는 컴포넌트 사이의 비동기 스트림 처리나 이벤트 흐름을 논블럭킹 방식으로 처리하기 위한 문법을 정의.

2. 스프링 부트와 래빗엠큐를 사용


## CORS
Same Origin Policy 에 의해 금지 된, 다른 도메인에게 데이터를 요청하는 것을 <br>
스프링부트에서 제공하는 CORS 로 가능하게 해준다.

1. @CrossOrigin  
    : 모든 도메인으로부터의 요청과 해더를 받아 준다.
   
2. @CrossOrigin("http://mytrustedorigin.com")
    : 특정 도메일으로부터의 요청만 허용할 수 있다.
    
3.
    ```java
     @Configuration
     public class MyConfiguration {
     
     	@Bean
     	public WebMvcConfigurer corsConfigurer() {
     		return new WebMvcConfigurer() {
     			@Override
     			public void addCorsMappings(CorsRegistry registry) {
     				registry.addMapping("/api/**");
     			}
     		};
     	}
     }
    ```
    
    
## 스프링 부트 데이터 레스트

@RepositoryRestResource <br>
    : 스프링 부트 데이터 레스트에서 지원하는 어노테이션으로, 별도의 컨트롤러와 서비스 영역 없이
    미리 내부적으로 정의되어 있는 로직을 따라 처리.
    
    
@RepositoryRestController <br>
    : 스프링 부트 데이터 레스트의 기본 설정을 원하지 않을 경우!! <br>
    @RestController 를 대체하는 용도로 사용하며 두 가지 주의사항이 있음. <br>
    <주의사항> <br>
    1. 매핑하는 URL 형식이 스프링 부트 데이터 레스트에서 정의하는 REST API 형식에 맞아야 함
    2. 기존에 기본으로 제공하는 URL 형식과 같게 제공해야 해당 컨트롤러의 메서드가 기존의 기본 API를 오버라이드 함.
    
   ```java
    @RepositoryRestController
    public class BoardRestController {
        private BoardRepository boardRepository;
        
        public BoardRestController(BoardRepository boardRepository){
            this.boardRepository = boardRepository;
        }
        
        @GetMapping("/boards")
        public @ResponseBody Resources<Board> simpleBoard(@PageableDefault Pageable pageable){
            Page<Board> boardList = boardRepository.findAll(pageable);
            
            PageMetadata pageMetadata = new PageMetadata(pageable.getPageSize(), 
                                        boardList.getNumber(), boardList.getTotalElements());
            
            PageResources<Board> resources = new PageResources<>(boardList.getContent(), pageMetadata);
            resources.add(linkTo(methodOn(BoardRestController.class).simpleBoard(pageable)).withSelfRel());
            
            return resources;
        }
    }
   ```