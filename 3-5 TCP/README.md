# 스터디 3-5

Date: August 14, 2023

# TCP

---

### Overview

- Point-to-point
- Reliable, in-order byte stream
    - Byte stream - 데이터를 text 형식이 아니라 binary로 입출력
- Pipelined
- Full duplex data
    - 양방향으로 데이터가 흐른다
    - MSS: maximum segment size
- Connection oriented
    - Sender와 receiver가 handshaking을 해야 데이터를 주고 받는게 가능
- Flow control
    - Receiver 입장에서 너무 많은 데이터가 들어오면 buffer overflow가 발생하므로 이를 막기 위해 필요
- Congestion control

### TCP segment structure

![화면 캡처 2023-08-14 025449.png](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%203-5%206ab471d5d57d4220b7e8aea7f092faf8/%25ED%2599%2594%25EB%25A9%25B4_%25EC%25BA%25A1%25EC%25B2%2598_2023-08-14_025449.png)

- Sequence number, Acknowledgement number
- RST, SYN, FIN : connection setup 관련 flag들
- URG, PSH : (내가 이해한 바로는) 먼저 처리해야 하는 request를 위한 flag

### TCP Sequence number

- byte stream에서 전송되는 세그먼트의 첫 번째 byte number
- Sequence number는 data의 크기만큼 증가
- 하나의 segment 크기가 8byte(얘는 정해진거) 이고 처음 sequence #가 1 (얘는 랜덤)이면
- 1, 9, 17 … 이런식으로 증가

### TCP ACK number

- Segment를 보낼 때 상대방으로부터 받을 것으로 예상되는 sequence #

![                                  MSS가 1byte인 경우의 흐름](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%203-5%206ab471d5d57d4220b7e8aea7f092faf8/%25ED%2599%2594%25EB%25A9%25B4_%25EC%25BA%25A1%25EC%25B2%2598_2023-08-14_033329.png)

                                  MSS가 1byte인 경우의 흐름

- Seq#와 Ack#를 이용해서 retransmission 해야함을 전달할 수 있음
    - 예를 들어 Seq# 0, 1, 2인 3개의 segment를 보낸다고 가정하자.
    - 이 때 Seq# 1인 segment에서 문제가 생겨서 데이터를 받지 못했다면
    - Receiver는 Ack#에 Seq# 1일 때의 Ack#를 보내 해당 segment에 문제가 있었음을 전달한다
    - Sender는 Ack#를 보고 해당 segment를 다시 보낼 수 있다

### TCP round trip time, timeout

- Round trip time(RTT) - Packet을 전송한 뒤 응답을 받는데 걸린 시간
- TCP는 Packet을 전송했는데 응답이 오지 않으면 retransmission을 한다.
- Timeout - Retransmission 여부를 결정하는 정해진 시간
    - Timeout value가 지났는데 응답이 오지 않으면 retransmission 한다
    - 너무 짧으면 packet loss가 없었는데 retransmission을 하는 경우가 발생
    - 너무 길면 packet loss에 빠르게 반응하지 못해 delay가 발생

- SampleRTT - 이전에 있었던 RTT들을 이용해서 미래의 RTT를 예상하는 방식
    - Exponential Weighted Moving Average(EWMA)
        - $EstimatedRTT = (1- \alpha )\ *\ EstimatedRTT + \alpha\ *\ SampleRTT$
        - 이 값을 Timeout value로 이용
        - 보통 $\alpha = 0.125$를 이용
    - Timeout interval
        - EWMA는 평균값과 비슷하기 때문에 EWMA를 넘는 경우가 많이 발생하게 된다.
        - 따라서 Safety margin을 EWMA에 더해줘서 timeout을 줄여준다
        - $Timeout\ interval = EstimatedRTT + 4*DevRTT$
    - Safety margin
        - $DEVRTT = (1- \beta )\ *\ DEVRTT + \beta\ *\ |SampleRTT - EstimatedRTT|$
        - 보통 $\beta = 0.25$를 이용