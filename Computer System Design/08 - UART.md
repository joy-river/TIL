### UART

---

- Universal Asynchronous Receiver and Transmitter

  - PC와 Zedboard를 이어주는 디바이스이다.

  - Serial Communication, 즉 비트 단위로 연속적인 통신이 이루어진다.

    - TxD에는 보내는 데이터가, RxD에는 받는 데이터가 있다.

    - 각각 64바이트의 FIFO로 이루어져있다.

    - 두 개의 와이어를 통해 통신이 이뤄진다.

  - Async = clk과 관련 없이 통신이 이뤄진다.

- 통신을 위해서는 상호간에 프로토콜을 설정해야한다.

  - 프토토콜은 두 가지 요소가 있으며, 이 요소들이 일치해야한다.

  - Data rate

    - baud rate라고도 하며, 얼마나 많은 비트를 송신할지 결정한다.

    - 115,200bps가 국룰이다.

  - Packet format

    - 패킷 하나에 몇개의 비트가 담길지 결정한다.

    - 일반적으로 8비트이나, 6, 7비트등도 될 수 있다.

- 프토토콜은 PC에선 teraterm, 보드에선 config register(=Mode_reg0) 에서 설정한다.

  - baud rate를 결정하기 위해 UART에 50MHZ, CD(=Clock divider)를 62로, BDIV = 6으로 줘서 115,200bps(=hz)를 만든다.

    > Baud_rate = sel_clk / (CD \* (BDIV + 1))

  - shift register를 통해 bit-by-bit로 전송한다.

    - shift 레지스터는 직렬(serial)한 입력과 출력, 그리고 병렬(parallel) 출력을 가진다.

    - serial to parallel이나, parallel to serial로 사용할 수 있다.

  - 패킷 포맷을 결정하기 위해 패킷의 구성 요소부터 이해한다.

    - 전송 전(idle) 상태에서 TxD 와이어는 high = 전압이 높게 유지된다.

    - Start bit는 전압을 낮춰 전송이 시작되게 한다.

    - 8 bit data가 전송된다.

    - 오류 감지를 위한 부가적인 parity 비트가 있을 수 있다.

      - 1의 개수를 parity까지 합쳐 even, odd로 유지시킨다.

    - 전송을 끝내고 전압을 다시 high하게 만드는 stop bit로 마무리된다.

    - 전송이 끝나면 인터럽트가 발생된다.

- 당연히 teraterm과 zedboard에서 데이터를 tx, rx하는 방식은 동일해야한다.

  - teraterm은 ASCii를 사용하므로, zedboard도 ASCii를 사용한다.

    - 0x0D는 CR(carrige return)= 줄넘김을 의미한다.

  - TX_RX_FIFO0에 두 개의 FIFO가 저장되어있다.

    - ldr = RxFIFO를 읽어온다.

      - ldr시 cpu가 addr, data, control info를 제공한다.

    - str = TxFIFO에 저장한다.

  - Channel_sts_reg0 register는 FIFO가 full, empty인지 체크한다.
