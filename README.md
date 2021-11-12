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
## Tech
- SpringBoot + gradle + JPA + h2 + jsp
