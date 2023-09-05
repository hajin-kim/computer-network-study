# 스터디 3-1, 3-2 발표

Date: August 7, 2023

# Transport layer

---

- 서로 다른 host에서 돌아가는 app process들 간의 logical communication을 제공한다
- end system에서 돌아간다.
- Sender가 하는 일
    - Segment 단위로 메세지를 잘라서 network layer으로 보낸다.
- Receiver가 하는 일
    - Segment 단위로 받은 메세지를 합쳐서 app layer로 보낸다.

- Network layer과의 차이
    - Network layer는 host 간의 logical communication
    - Transport layer는 process 간의 logical communication

## TCP & UDP

- TCP : reliable, in-order delivery
    - Congestion control, flow control, handshaking
- UDP : unreliable, unordered delivery
- TCP와 UDP 모두 제공하지 않는 기능
    - delay guarantees
    - bandwidth guarantees
    - 다른 layer에서 구현하는 것은 가능

# Multiplexing/Demultiplexing

![캡처.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%203-1,%203-2%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%20bb76fc4ad8614107b4c316917ee58163/%25EC%25BA%25A1%25EC%25B2%2598.png)

서버 하나에 여러 개의 end device에서 여러 개의 packet이 들어오는 상황

서버의 어떤 프로세스로 packet을 보낼 지 결정

![화면 캡처 2023-08-14 035829.png](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%203-1,%203-2%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%20bb76fc4ad8614107b4c316917ee58163/%25ED%2599%2594%25EB%25A9%25B4_%25EC%25BA%25A1%25EC%25B2%2598_2023-08-14_035829.png)

- Multiplexing
    - Sender 측에서 transport header를 붙여서 전송
    - 여러 source에서 오는 data를 하나의 source로 보내주는 것
    - App layer에서 packet이 transport layer로 전달될 때 여러 소켓에서 오는 packet을 모아 하나의 segment로 캡슐화하는 것
- Demultiplexing
    - Receiver 측에서 header를 보고 어떤 소켓으로 보낼 지 결정
    - 하나의 source로부터 오는 stream을 여러개의 source로 보내주는 것
    - Transport layer에 전달된 segment가 여러개의 socket 중에 올바른 socket으로 전달하는 것
        - Header에 있는 정보들을 활용해서 전달한다

## Connectionless demultiplexing (UDP)

![캡처1.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%203-1,%203-2%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%20bb76fc4ad8614107b4c316917ee58163/%25EC%25BA%25A1%25EC%25B2%25981.png)

서버

- 소켓 생성
- 서버의 address 넣어주고 bind
- client로부터 온 메세지를 읽음 ⇒ 클라이언트의 port 번호를 저장
- client에게 메시지를 보냄 ⇒ 메세지를 읽을 때 저장한 port 번호와 IP address로 보냄

클라이언트

- 소켓 생성
- 서버의 정보 저장
- 메세지를 보낸 뒤 메세지를 받아들임

## Connection-oriented demultiplexing (TCP)

![캡처2.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%203-1,%203-2%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%20bb76fc4ad8614107b4c316917ee58163/%25EC%25BA%25A1%25EC%25B2%25982.png)

TCP는 소켓을 구별하기 위해 4가지 정보를 모두 이용한다(4가지 조건이 모두 같아야 해당 소켓으로 전달)

- Source IP address
- Source port number
- Dest IP address
- Dest port number