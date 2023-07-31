# 2-3 Electronic Mail (SMTP, POP3, IMAP)

E-Mail의 중요한 세가지 요소

- User agents
- Mail servers
- Simple Mail Trasnfer Protocol (SMTP)

User Agent

- Mail reader라 불린다.
- Mail messages들을 다루는 것을 말한다.
- server-client format에서 clients를 담당하고 있다.

Mail Servers

- Its own server(네이버, gmail)
- Mail server 간의 연결을 통해 (SMTP protocol) mail message가 주고 받는다
- Mailbox라는 저장소에 message들을 저장한다.

### Mail의 기본적인 구조

![Mail의 기본적인 구조](resources/mail%20server.png)

## SMTP

- Uses TCP (port 25)
- direct transfer 즉 sending server to receiving server이다.
- 3가지 단계가 존재한다.

  -Handshaking(greeting)이란 통신의 양측 간에 조건에 합의해 가는 정보 교환 과정이다.

  -Transfer of messages

  -Closure

- Command는 ASCII text로 이루어져 있고 response는 status code and phrase로 이루어져 있다.
- Message의 경우 반드시 7-bit ASCI로 이루어져야 한다.

### Mail을 SMTP로 보내는 과정

![Mail을 SMTP로 보내는 과정](resources/스크린샷%202023-07-29%20124353.png)

중요한 것은 6번의 과정에서는 SMTP가 쓰이지 않는 다는 것이다.

교수님께서 설명은 안하셨지만 간단한 SMTP 명령어들

- HELO: SMTP 통신을 시작하기를 원한다는 것을 알리는 명령어. Handshaking에 해당함
- MAIL FROM: 발신자의 이메일 주소를 나타냄
- RCPT TO: 수신자의 이메일 주소를 나타내는 데 여러 명의 수신자가 있으면 여러 번 사용 사능
- DATA: 본문 내용 한 줄씩 전송
- QUIT: 연결 종료

Telnet이라는 개념이 나오는데 교수님께서 설명 안하시고 넘어가심. 간단하게 정리하면 사용자의 컴퓨터에서 네트워크를 이용하여 원격지에 떨어져 있는 서버에 접속해 자료를 교환할 수 있는 프로토콜을 말한다.

- SMTP는 persistent connection을 사용한다.

## HTTP와 SMTP의 비교

- HTTP는 pull, SMTP는 push라 한다. 즉 HTTP는 client 관점에서 보면 서버로 부터 정보를 가져오기 위해 사용하는 것이고 SMTP는 송신자가 수신자의 서버로 보내는 데에 사용하기 때문이다.
- 둘 모두 ASCII command/reponse interaction, status codes를 갖고 있다.
- 또 하나의 차이점은 다음과 같다.
  - HTTP: 각각의 object는 independent하게 message에 들어간다.
  - SMTP: 다양한 object가 하나의 message에 들어간다.(MIME이라 하심)
- Multipart라는 단어가 나왔는데 이는 여러가지 object를 구분해서 넣어주는 방법이라 한다.

## Message format

Message format에는 크게 header와 body가 존재한다.

- Header: TO, From, Subject가 들어가는데 중요한 것은 SMTP의 MAIL FROM, RCPT TO command들과는 다른것임을 아는 것이다.
- Body: 그저 message이다. 이는 아스키 코드로 이루어져있다.

## Mail Access Protocols

아까 메일을 보내는 순서에 대해 설명한 그림에서 6번이 SMTP 과정이 아니라 했는데 이 6번에 해당하는 과정을 설명한다. 이를 mail access protocol이라 한다.

### POP3

- 일방향 e-mail 동기화만 지원한다. 즉 server에서 cliient로 download하는 것만 허용이 된다.
- 따라서 server에 있는 e-mail은 삭제가 되고 client를 바꾸면 re-read할 수 없다.
- Stateless하다. 즉 server가 client의 상태를 보존하지 않는 것을 말한다. 요청에 대한 응답만 처리하는 방식이다.
- Authorization Phase: 인증 단계이다. Username과 password를 확인하고 server에서 ok인지 아닌지 response를 확인한다.
- Transaction Phase: 메일을 가져오는 단계이다. List, retr, dele, quit 명령어가 존재한다.

### IMAP

교수님께서 딱히 설명을 크게 안하셨지만 현대에서는 이게 많이 쓰인다고 한다.

- 서버에 모든 messages를 저장한다. 따라서 다른 컴퓨터에서도 접근이 가능하다.
- folder의 형태로 관리
