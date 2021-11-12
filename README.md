# ggyuchat
웹 소켓을 이용한 실시간 채팅 토이 프로젝트


## HTTP vs 웹소켓
 - HTTP
   - 비 연결지향
   - 요청-응답 구조
   - stateless(계속 ack를 받아서 확인해야함)
   - 웹소켓에 비해 많은 양의 데이터를 주고받음
 - 웹소켓
   - 연결지향
   - 양방향 통신
   - 한번 연결후 유지
   - 처음 핸드쉐이크할때만 HTTP처럼 데이터가 오가고 이후부터는 메시지만 오고 감
   
## 웹소켓을 지원하지 않는 환경에서 사용
 - SockJs
 - Socket.io
   
## WebSocketHandler
  TextWebSocketHandler or BinaryWebSocketHandler 하나를 상속하여 구현하면 된다.
  
## WebSocketSession
 웹소켓이 연결되었을때 정보를 담고있는 세션
 
# STOMP(Simple Text Oriented Messaging Protocol)
## 쉽게 메세지를 주고 받을 수 있는 프로토콜
  - pub - sub: 발신자가 메시지를 발행하면 수신자가 그것을 수신하는 메시징 패러다임 ex) 해당 path 를 설정하면 그러한 path에서 오는 정보는 수신자가 받아낸다.
  - 메시지 브로커: 발신자의 메시지를 받아와서 수신자들에게 메시지를 전달하는 어떤 것
## 프레임
웹소켓만 사용하면 날것의 메시지만 오고 가지만, stomp를 사용하면 command, 헤더, 바디의 형태로 메시지가 오고 간다.(협업할때 가독성이 좋아진다.)
```
COMMAND 
header1:value1 
header2:value2 

Body^@
```
첫번째 라인은 텍스트(Command 명령어)이고 이후 key:value 형태로 header 정보를 포함한다.
header이후에 공백 줄을 하나 더 추가하는 것으로 header의 끝을 설정할 수 있다.
header이후에는 Payload(Body)가 존재한다. Payload(데이터)는 Body에 위치하는데, 끝은 NULL 문자로 설정한다.
command 명령어 : https://www.joinc.co.kr/w/man/12/STOMP


## STOMP를 사용할 때, 통신 흐름
/topic 의 path로는 구독자들이 사용하고 만약 메시지 처리가 필요하면 /app을 통해 메시지를 가공/처리 후에 다시 /topic의 path로 구독자에게 보여줄수 있다.
![image](https://user-images.githubusercontent.com/60054318/141477953-97a3ccba-78bf-42e9-b077-3474442a6c79.png)
출처 : https://docs.spring.io/spring/docs/5.0.4.RELEASE/spring-framework-reference/web.html#websocket-stomp-message-flow


## 메시지 브로커
### config
```
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketBrokerConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        //내장 브로커로서 prefix가 붙은 메시지 발행시 브로커가 처리
        registry.enableSimpleBroker("/topic", "/queue"); //topic 1:M, queue 1:1로 캐스팅될때 주로 사용
        //메시지 가공이 필요할때 핸들러로 라우팅되는 prefix
        registry.setApplicationDestinationPrefixes("/app");

    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/gs-guide-websocket").withSockJS(); // 웹소켓 연결 주소
    }
```
### Controller
```
@Controller
public class GreetingController {
    //stomp 웹소켓을 통해 /app/hello 들이 여기로 거침
    @MessageMapping("/hello")
    @SendTo("/topic/greeting") // 처리 마치고 보내줄 경로 : prefix+path
    public Hello greeting(HelloMessage message) throws Exception{
        Thread.sleep(1000);
        return new Hello(
                "Hello, "+ HtmlUtils.htmlEscape(message.getName())+"!"
        );
    }
}
```

## STOMP 장점
 - 하위 프로토콜 또는 컨벤션을 따로 정의할 필요가 없다.
 - 연결 주소마다 새로운 핸들러를 구현하고 설정할 필요가 없다.
 - 외부 Messaging Queue를 사용 가능(RabbitMQ, 카프카 등)
 - soring security사용 가능

## Tech
- SpringBoot + gradle + JPA + h2 + jsp
