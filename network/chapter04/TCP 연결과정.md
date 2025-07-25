# 전송계층 
## TCP(Transmission Control Protocol)
### TCP 통신 단계와 세그먼트 구조 
- TCP 통신을 크게 연결 수립, 데이터 송수신, 연결 종료의 세 단계로 구분 가능하다.
- 데이터 송수신 과정에서 오류 제어, 흐름제어, 혼잡 제어 등의 기능을 제공한다. 
- TCP 연결 수립과 종료를 이해하려면 MSS라는 단위와 TCP 세그먼트 구조를 이해해야 한다. 
  
#### MSS(Maximum Segment Size)
- MSS는 TCP로 전송할 수 있는 최대 페이 로드 크기를 의미한다.
- MTU와 다르게 MSS의 크기를 고려할 때 TCP의 헤더 크기는 제외한다.  
  
#### TCP Header 구조 
- source port / destination port  : 송수신지 애플리케이션을 식별하는 포트 번호  
- sequence number : 송수신되는 세그먼트의 올바른 순서 보장을 위해 세그먼트 데이터의 첫바이트에 부여되는 번호 
- acknowledgement number : 상대 호스트가 보낸 세그먼트에 대한 응답 
- control bit : 플래그 비트로  현재 세그먼트에 대한 부가정보가 담겨있다 
- window : 수신 윈도우의 크기 명시 되는데 수신 윈도우는 한번에 수신하고자 하는 데이터의 양을 의미한다. 
  

#### 제어 비트(control bit) 
- 제어 비트 필드는 기본적으로 8bit 로 구성된다. 
- 각 자리의 비트는 각기 다른 의미를 가지는데 TCP 기본 동작을 논할 때 가장 많이 언급 되는 세가지 비트는 ACK, SYN, FIN이다.
- 세그먼트의 승인을 나타내기 위한 비트 
- 연결을 수립하기 위한 비트 
- 연결을 종료하기 위한 비트 

#### 순서 번호(sequence number)과 확인 응답 번호( acknowledgement number)
- 순서번호 필드와 확인응답 번호 필드는 TCP의 신뢰성을 보장하기 위해 사용되는 중요한 필드로 한쌍을 묶어서 기억하는 것이 좋다 . 
- 순서번호 필드에 명시되는 순서번호는 세그먼트의 올바른 송수신을 보장하기 위한 번호로 세그먼트 데이터의 첫 바이트에 부여되는 번호다. 
- 전송 계층이 응용 계층으로 전송해야 하는 1900바이트 크기의 데이터는 MSS 단위로 전송이 가능하다. 
- 편의상 MSS가 500 바이트라고 가정했을 때 1900바이트는 MSS단위로 쪼개면 네 개의 세그먼트로 쪼갤 수 있다. 
- 처음 통신을 위해 연결을 수립한 경우 제어비트에서 연결을 수립하기 위한 비트인 SYN 플래그가 1로 설정된 세그먼트의 경우 순서번호는 무작위 값이 된다. 
- 연결 수립 이후에 데이터를 송신하는 동안 순서번호는 송신한 바이트를 더해 가는 형태로 누적 값을 가진다. 
- 확인 응답 번호는 순서 번호에 대한 응답이다. 일반적으로 수신한 순서번호 + 1 로 설정된다. 

### TCP 연결과 수립 종료 
- TCP이전에는 연결을 수립하고 통신 이후에는 연결을 종료한다. 
  
#### 연결 수립 : 쓰리 웨이 핸드셰이크 
- TCP의 연결 수립은 쓰리웨이 핸드 셰이크를 통해서 이루어진다. 세개읜 단계로 이루어진 TCP의 연결 수립 과정을 의미한다.

| 단계 | 송신 호스트 | 수신 호스트 | 세그먼트 내용                          | 순서 번호 (SEQ) | 확인 응답 번호 (ACK) | 플래그(SYN/ACK) |
|----|---|----|----|---|---|---|
| 1    | 호스트 A     | 호스트 B     | SYN 세그먼트 (연결 요청)               | A의 초기 순번     | -                      | SYN=1            |
| 2    | 호스트 B     | 호스트 A     | SYN+ACK 세그먼트 (연결 수락 응답)      | B의 초기 순번     | A의 순번 + 1           | SYN=1, ACK=1     |
| 3    | 호스트 A     | 호스트 B     | ACK 세그먼트 (연결 확정)               | A의 순번 + 1      | B의 순번 + 1           | ACK=1            |

- 처음 연결을 시작하는 호스트의 연결 수립 과정을 Active Open이라고 한다. 
- 처음 연결 요청을 받고 연결을 수립해 주는 연결 수립 과정을 Passive Open 이라고 한다. 
  
#### 연결 종료 
- 쓰리 웨이 핸드셰이크를 통해 연결을 수립한 뒤 데이터 송수신이 끝나면 이제 연결을 종료해야 한다. 
- TCP가 연결을 종료하는 과정은 송수신 호스트가 각자 한번씩 FIN과 ACK를 주고 받으며 이루어진다. 

| 단계 | 송신 호스트 | 수신 호스트 | 세그먼트 내용                          | 순서 번호 (SEQ) | 확인 응답 번호 (ACK) | 플래그(FIN/ACK) |
|------|--------------|--------------|----------------------------------------|------------------|------------------------|------------------|
| 1    | 호스트 A     | 호스트 B     | FIN 세그먼트 (연결 종료 요청)          | A의 현재 순번     | -                      | FIN=1            |
| 2    | 호스트 B     | 호스트 A     | ACK 세그먼트 (종료 요청 수락)          | B의 현재 순번     | A의 순번 + 1           | ACK=1            |
| 3    | 호스트 B     | 호스트 A     | FIN 세그먼트 (자신도 종료 요청)        | B의 현재 순번     | -                      | FIN=1            |
| 4    | 호스트 A     | 호스트 B     | ACK 세그먼트 (종료 수락 및 최종 응답)  | A의 순번 + 1      | B의 순번 + 1           | ACK=1            |

- 연결을 수립할 때 Active Open, Passive Open 이 있는 것 처럼 연결 종료도 Active Close, Passive Close 가 있다. 
  
## TCP 상태 
- TCP 는 연결형 통신과 신뢰할 수 있는 통신을 유지하기 위해 <u>다양한 상태(state)</u>를 유지한다.
- 상태는 현재 어떤 통신과정에 있는지를 나타내는 정보이다. <u> TCP는 상태를 유지하고 활용 한다는 점에서 Stateful 프로토콜이라고도 불리운다. 

| 상태               | 의미 및 설명                                                                  | 주요 전이 조건 (Trigger)                         |
|--------------------|-------------------------------------------------------------------------------|--------------------------------------------------|
| CLOSED             | 초기 상태 (연결 없음)                                                         | 수동/능동 오픈 요청                              |
| LISTEN             | 수동 대기 상태 (서버 측, 연결 요청 대기 중)                                   | `socket(), bind(), listen()` 호출 후            |
| SYN_SENT           | SYN 전송 후 응답 대기 (클라이언트가 능동적으로 연결 요청한 상태)              | 클라이언트가 `connect()` 호출                    |
| SYN_RECEIVED       | SYN 수신 후, SYN+ACK 송신 (수신 측 연결 요청 수락 준비 중)                    | 서버가 SYN 수신하고 SYN+ACK 응답 송신           |
| ESTABLISHED        | 연결 완료 (양쪽 모두 연결됨)                                                  | 3-way handshake 완료                             |
| FIN_WAIT_1         | 연결 종료 요청 (FIN 전송됨)                                                   | `close()` 호출                                   |
| FIN_WAIT_2         | FIN 전송 후, 상대방의 FIN 기다리는 중                                         | 상대가 ACK 응답                                  |
| CLOSE_WAIT         | 상대방의 FIN 수신 후, 애플리케이션이 종료 준비 중                             | FIN 수신함                                       |
| CLOSING            | 양쪽이 동시에 FIN 전송한 경우                                                  | FIN 송수신 거의 동시에 발생                     |
| LAST_ACK           | FIN 송신 후, ACK 기다리는 중 (수동 종료 측)                                   | 애플리케이션이 종료 후 FIN 보냄                 |
| TIME_WAIT          | 연결 종료 후 일정 시간 대기 (재전송 대비)                                     | FIN-ACK 후 마지막 ACK 송신                       |
|  CLOSED | 연결 종료 후 완전히 닫힌 상태                                                 | TIME_WAIT 종료 or LAST_ACK 후 ACK 수신 완료     |

--- 
> 강민철, 『혼자 공부하는 네트워크』, 한빛미디어 (2018)    
