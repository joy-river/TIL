### Application layer

---

- OSI에서는 7계층, IP에서는 5계층에 Application layer가 존재한다.

  - Presentation layer, Session layer가 합쳐져있다.

- 이번 장에서는 Principle of network applications를 중점적으로 살펴보게 된다.

### Principle of network applications

---

- 네트워크 app은 단순히 네트워크를 사용하는 프로그램이다.

  - 가장 오래된 email부터 웹 브라우저, sns, 게임, 유튜브, Skype 등이 모두 속한다.

- Internet Protocol Stack를 되짚어 보자.

  - Application layer는 프로그램이 실제 구현할 수 있는 프로토콜을 제공하여 프로그램들을 지원한다.

    - HTTP, SMTP, IMAP, FTP 등이 여기에 속한다.

  - Transport layer는 실제로 다른 호스트 내의 프로세스 간 통신, 종단 시스템 간의 통신을 담당한다.

    - TCP, UDP가 여기에 속한다.

  - Network layer는 데이터그램을 출발지 -> 목적지로 라우팅하고, 전송, Addressing을 한다.

    - IP, Routing protocol이 속한다.

  - Link layer는 프레임을 실제로 유, 무선을 통해 연결된 이웃 네트워크 사이에 전송한다.

    - Ethernet과 WiFi(802.11)이 여기에 속한다.

  - Physical layer는 선으로 비트를 전송한다.

    - Optical fiber, Twisted Pair 등이 이를 사용한다.

- IP를 거쳐내려오며 전송할 메시지에 헤더를 붙이는 과정을 캡슐화(Encapsulation)이라고 칭한다.

  - 각 단계의 메시지는 Payload라고 칭한다.

  - Application layer -> Message

  - Transport layer -> H1 + Message = segment

  - Network layer -> H2 + Segment -> Datagram

  - Link layer -> H3 + Datagram => Frame

- 응용 프로그램은 크게 두 가지 구조를 가진다.

  > 실제 통신은 네트워크 코어가 아닌, 엣지들에서 이뤄진다.
  > 네트워크 코어는 프로그램이 실행되는게 아니라, 패킷을 전달하는게 전부이다.

  - Client-Server 구조

    - 서버

      - 항상 호스트, 항상 연결되어있는 상태이다.

      - 영구적인 IP 주소를 가진다.

      - 처리할 데이터의 규모에 따라 바뀌는 데이터 센터

    - 클라이언트

      - 서버를 통해서만 간헐적으로 소통한다

      - 유동적인 IP 주소를 가진다.

      - 클라이언트끼리는 절대 연결하지 않는다.

    - Peer-to-Peer(P2P) 구조

      - 항상 켜져있을 필요가 없다.

      - 소통시 무작위로 연결된다.

      - 자가 확장성 - 피어가 늘어날 수록 성능이 좋아진다.

      - 동적인 IP를 가지고, 변경될 수 있다.

- 프로세스 통신

  - 프로세스는 호스트 내부에서 실행되는 프로그램이다.

    - Client 프로세스는 서버에 무언가를 요청하는, 통신을 시작하는 프로세스이다.

    - Server 프로세스는 요청을 기다리는, 통신을 기다리고 있는 프로세스이다.

  - 프로세스는 다른 프로세스와 통신한다.

    - 같은 호스트 내부에서는 Inter Process Communication을 통해 통신한다.

    - 서로 다른 호스트의 프로세스는 Message를 주고받으며 통신한다.

      - 이때 서로 다른 프로세스는 Socket을 통해 메시지를 주고받게 된다.

- 소켓(Socket)

  - 프로세스가 메시지를 주고받을 때 사용하는 도구이다.

    - 프로세스 하나당 하나씩 적어도 두 개의 소켓이 필요하다.

  - Transport layer와 Application layer 사이에 위치하여, 개발자가 통제하는 문이다.

    - 그 아래 계층은 OS가 통제한다.

  - 보내는 프로세스가 소켓에 데이터를 넣으면, 받는 프로세스가 소켓에 있는 데이터를 건져간다.

- 주소(Addressing)

  - 메시지를 주고받기 위해서는 프로세스가 각자의 식별자(Identifier)를 가져야한다.

  - 호스트 디바이스를 구분하는 식별자 = IP

    - IP 주소는 32비트(IPv4)로 이루어져 있다.

    - 집 주소와 비슷한 기능을 한다.

  - 데이터를 받을 프로세스를 구분하는 식별자 = 포트 번호(Port number)

    - 포트 번호는 정수이며, 오래된 기술일 수록 낮은 번호를 가진다.

    - HTTP 서버는 80, 메일 서버는 25를 가진다.

    - 집 내부의 방 번호와 비슷한 기능을 한다.

- Application layer protocol

  - 프로토콜은 여러 정보를 담고 있어야한다.

    - 주고 받을 메시지의 타입

      - request? response?

    - 메시지 문법(Syntax)

      - Header, 메시지 내부에 어떤 필드가 있는지

    - 메시지 시멘틱(Semantic)

      - 메시지의 의미, 출발지와 도착지 ip, 포트 번호 등

    - 규칙(rules)

      - 언제 어떻게 메시지를 보내고 받을지.

  - 프로토콜은 크게 두 종류가 있다.

    - Open protocol

      - TCP/IP 등 RFC 표준으로 지정된 프로토콜

    - Proprietary protocol

      - Skype 등 특정 기업이 만든 프로토콜

- Application layer가 필요로 하는(기대하는) Transport layer의 서비스(역할)

  - 서로 붙어있는 두 계층은 상호작용하기 때문에, 상부상조해야한다.

  - Data Integrity(데이터 보전)

    - 데이터를 손실 없이 전송해줬으면 좋겠다(보안과는 전혀 관련 없다).

    - Reliable data transfer = 내가 보낸 데이터가 100% 도착해야 함.

    - 그러나 이는 프로그램의 종류에 따라 변화할 수 있음.

      - 메일이나 웹 통신은 100% 보장되어야하지만, 오디오 등은 조금의 손실이 있어도 괜찮다.

  - Timing

    - Delay와 반대되는 말로, Transport layer가 적은 딜레이로 데이터를 주고받기를 원한다.

  - Throughput(처리량)

    - 사실상 최종 목적이며, 높을 수록 좋겠다.

    - less delay = more throughput

      - 일반적으로 패킷 손실 = delay가 되는 경우가 많다.

    - 하지만 이 처리량도 elastic(탄력적)일 수 있다.

      - 실시간 멀티미디어는 최소치가 정해져 있으나, 문서 전송, 메일 등의 경우에는 조금 천천히 와도 받기만 하면 된다.

  - Security(보안)

  - 이 4가지는 아주 중요하나, 모든 프로그램에서 이 4가지를 완전히 만족시키지 못하므로 적절히 타협이 필요하다.

### TCP와 UDP

---

- 4 계층, Transport 프로토콜에는 TCP와 UDP의 두 종류가 있다.

- TCP

  - Transmission control protocol이며, 신뢰적인 전송을 추구한다.

  - 송수신자 간의 신뢰적인 전송을 보장한다.

  - 흐름 제어 = 송, 수신측의 데이터 처리 속도 차이를 해결한다.

    - 송신자의 데이터 처리 속도를 수신자보다 낮춘다.

  - 혼잡 제어 = 송신측의 데이터 전송과 네트워크의 데이터 처리 속도 차이 해결(Bottleneck)

    - 라우터의 버퍼를 늘리거나, 버퍼가 넘치지 않게 송신자의 속도를 낮춘다.

  - 데이터 보전을 제공, 다른 건 전혀 신경쓰지 않는다.

  - 서버-클라이언트 간 연결 설정 단계를 거친다.

- UDP

  - User datagram protocol이며, 단순한 전송을 추구한다.

  - 비신뢰적인 전송 방식이다.

  - 그 어떤 것도 제공해주지는 않지만, 데이터를 빠르게 전송한다.(real-time streaming = fast!)

- TCP가 대부분 UDP를 대체했으나, 아직 UDP를 쓰는 프로그램도 존재한다.

  - 멀티미디어 스트리밍, 인터넷 전화 등은 loss-tolerant하므로 UDP를 사용하기도 한다.
