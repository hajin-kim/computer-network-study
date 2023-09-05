# 스터디 4-2 후반

Date: August 24, 2023

## Longest prefix matching

![캡처.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%204-2%20%E1%84%92%E1%85%AE%E1%84%87%E1%85%A1%E1%86%AB%20f853ca2234254ce3913cbf353766bc95/%25EC%25BA%25A1%25EC%25B2%2598.png)

- Router가 하는 일
    - Destination address range를 추출해서 forwarding table을 체크하여 outgoing interface를 결정한다
    - 여러개의 entry에 적합할 수 있다
        - Longest prefix matching policy를 이용한다
- Forwarding table의 크기
    - 최악의 경우 Internet 전체의 network 모두를 포함해야 한다
    - 현실에서는 전체를 갖고 있진 않지만 그래도 수많은 정보를 갖고 있다
    - 이 때 많은 packet이 동시에 들어오기 때문에 커다란 table에서 빠르게 찾아야 한다
- Ternary content addressable memory(TCAM)
    - Packet에서 outgoing interface를 빠르게 찾기위해 이용되는 special purpose hardware
    - CAM은 빠르게 데이터를 찾게 해주는 하드웨어
    - Ternary인 이유는 0, 1, *만으로 entry가 구성되기 때문이다

## Switching fabrics

![캡처1.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%204-2%20%E1%84%92%E1%85%AE%E1%84%87%E1%85%A1%E1%86%AB%20f853ca2234254ce3913cbf353766bc95/%25EC%25BA%25A1%25EC%25B2%25981.png)

- Outgoing interface를 결정한 뒤 packet을 incoming interface에서 outgoing interface로 이동시킬 때 이용되는 구조

### Memory

- 모든 input port가 shared memory를 이용한다
- Input port로 들어오는 packet은 memory에 복사되었다가 memory에서 output port로 보내진다
- 2번의 memory access로 인해 속도는 memory bandwidth에 의해 결정된다

### Bus

- Input port와 Output port 사이에 bus를 두고 이용한다
- 하나의 버스를 이용하므로 memory bandwidth에 의해 속도가 결정된다
- 충돌을 피하기 위해 한번에 한개의 packet만 전송할 수 있어 Bottleneck이 발생할 수 있다

### Interconnection network

- Memory bandwidth에 따른 속도 한계가 없다
- Multiple bus가 서로를 연결해주는 방법
- 동시에 여러개의 packet 전송이 가능하다

## Queueing

### Input port queueing

![캡처2.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%204-2%20%E1%84%92%E1%85%AE%E1%84%87%E1%85%A1%E1%86%AB%20f853ca2234254ce3913cbf353766bc95/%25EC%25BA%25A1%25EC%25B2%25982.png)

- Switching fabric이 input port에 들어오는 속도보다 느리면 queueing이 발생한다
    - Buffer overflow가 발생하면 loss가 발생할 수 있다
- Head-of-the-line(HOL) blocking
    - Queue 안의 packet이 forwarding되기 전까지 그 뒤에 있는 packet은 forwarding 되지 않는다

### Output port queueing

![캡처3.PNG](%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%84%83%E1%85%B5%204-2%20%E1%84%92%E1%85%AE%E1%84%87%E1%85%A1%E1%86%AB%20f853ca2234254ce3913cbf353766bc95/%25EC%25BA%25A1%25EC%25B2%25983.png)

- Transmission 되는 속도보다 outgoing port에 들어오는 속도가 빠르면 queueing 발생한다
    - buffer overflow가 발생하면 loss가 발생할 수 있다
- buffer의 크기
    - 너무 크면 큰 delay가 발생할 수 있다
    - Recent recommendation: $RTT * C \over \sqrt{n}$
        - RTT가 크거나 capacity가 크면 buffer의 크기도 증가한다
        - flow의 개수(N)가 증가하면 buffer의 크기는 감소한다

### Discard policy

- Tail drop
    - 마지막에 도착한 packet을 discard
- Priority
    - 우선순위가 낮은 packet을 discard
- Random early drop (RED)
    - 주로 이용됨
    - Queue가 거의 찼을 때 새로운 packet이 들어오면 randomly discard
    - Queue가 이미 다 찬 상태에서 버리면 너무 늦었기 때문에 다 차기 전에 버린다.

### Scheduling policy

- Priority scheduling
    - 가장 높은 priority의 packet을 먼저 전송한다.
    - IP source/dest, port number 등으로 priority가 결정된다.
- Round robin
    - 여러개의 class와 각각의 queue가 있다
    - Queue를 돌면서 각 queue에서 한개의 packet씩 보내는 방법
    - 하나의 flow에서 오는 packet은 하나의 queue에 들어가야 한다
        - 순서가 바뀔 수 있기 때문
- Weighted Fair Queueing(WFQ)
    - Generalized RR
    - 클래스마다 큐를 설정하여 class별로 보내는 것은 동일하다
    - 다만 큐에 가중치를 둔다는 것이 다른 점이다