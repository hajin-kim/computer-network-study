# 스터디 2-7 발표

Date: August 3, 2023

# Socket programming

---

> 소켓
> 
- 프로세스의 communication을 위한 인터페이스
- door between application area and OS area
- 프로세스는 소켓이 열려있는 동안 소켓을 통해 메세지를 보내거나 받을 수 있음.
- 한 개의 프로세스가 여러 개의 소켓을 가질 수 있음.

> 소켓 프로그래밍
> 
- 2가지 transport service에 따른 2가지 소켓을 볼 것임
    - UDP
    - TCP
- application example
    - client가 글을 쭉 입력해서 send
    - server는 데이터 받아서 모두 대문자로 바꾼 뒤 다시 client에게 전송
    - client는 변경된 데이터를 출력

### UDP

- No handshaking
- Unreliable data transport
- Sender는 IP address와 port # 를 packet에 기록

![캡처.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%202-7%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208467bd503ab3445b8102ce19760d7f3e/%25EC%25BA%25A1%25EC%25B2%2598.png)

```python
# UDP client
from socket import *
serverName = ‘hostname’
# hostname은 server의 IP나 domain name로 바꿔줘야함
# client와 server가 같은 컴퓨터에서 돌아가게 하고 싶으면 localhost로 바꾸면 된다
serverPort = 12000
clientSocket = socket(AF_INET, SOCK_DGRAM)
message = raw_input(’Input lowercase sentence:’)
clientSocket.sendto(message.encode(), (serverName, serverPort))
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
print modifiedMessage.decode()
clientSocket.close()
```

```python
# UDP server
from socket import *
serverPort = 12000
serverSocket = socket(AF_INET, SOCK_DGRAM)
serverSocket.bind(('', serverPort))
# 소켓을 특정 포트로 open 할 때 포트를 선언해주는 거
print (“The server is ready to receive”)
while True:
	message, clientAddress = serverSocket.recvfrom(2048)
	# 최대 수신 가능한 byte 수
	modifiedMessage = message.decode().upper()
	serverSocket.sendto(modifiedMessage.encode(),	clientAddress)
```

### TCP

- Reliable transport
- Flow control by receiver
- Congestion control
- 3-way handshake 이용

![캡처.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%202-7%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208467bd503ab3445b8102ce19760d7f3e/%25EC%25BA%25A1%25EC%25B2%2598%201.png)

```python
# TCP client
from socket import *
serverName = ’servername’
serverPort = 12000
clientSocket = socket(AF_INET, SOCK_STREAM)
clientSocket.connect((serverName,serverPort))
sentence = raw_input(‘Input lowercase sentence:’)
clientSocket.send(sentence.encode())
modifiedSentence = clientSocket.recv(1024)
print (‘From Server:’, modifiedSentence.decode())
clientSocket.close()
```

```python
# TCP server
from socket import *
serverPort = 12000
serverSocket = socket(AF_INET,SOCK_STREAM)
serverSocket.bind((‘’,serverPort))
serverSocket.listen(1)
# 숫자는 최대 몇개의 TCP connection을 받을 것인가
print ‘The server is ready to receive’
while True:
	connectionSocket, addr = serverSocket.accept()
	# client와 connection을 맺겠다
	sentence = connectionSocket.recv(1024).decode()
	capitalizedSentence = sentence.upper()
	connectionSocket.send(capitalizedSentence.encode())
	connectionSocket.close()
```
