### Memory Map

---

- 우리는 많은 종류의 IO 디바이스가 필요하다.

  - 컴퓨터, 보드 내부에서는 어떤 인터페이스도 존재하지 않으나, IO 디바이스는 외부 인터페이스를 제공할 수 있다.

- 수업에는 총 세 가지 디바이스를 다룬다.

  - GPIO(General Purpose Input Output)

    - 다른 디바이스(LED, sensor)등을 연결, 통제하고 데이터를 주고 받게 한다.

  - Timer

    - 시간을 측정, 인터럽트를 발생시킨다.

  - UART(Universal Asynchronous Receiver and Transmitter)

    - 보드와 컴퓨터 사이에 송수신을 가능케 한다.

- 모든 종류의 IO 디바이스는 프로그래밍을 위해 세가지 레지스터를 제공한다.

  - Config

  - Data

  - Status

- 그럼 이 레지스터에는 어떻게 접근하는가?

  - Memory Access Inst가 필요하겠다.

  - 그러면 IO 디바이스가 가진 레지스터를 cpu의 메모리로 옮겨오는 과정이 필요하다.

  - 이를 Memory Mapping이라고 한다.

- CPU는 32비트의 주소 버스(Address bus)를 운행한다.

  - 이 주소가 PC 값이 되며, 이 주소 버스가 도착한 곳에서 명령어를 읽어온다.

  - 바꿔 얘기하면, 주소 버스가 도착하는 곳이 메모리의 한계 범위가 된다.

    - 32비트 버스는 $2^32$ 비트, 4GB의 메모리 공간을 가질 수 있다.

    - 0x0000_0000 ~ 0xffff_ffff가 가능한 메모리 버스의 한계가 된다.

  - 이 메모리는 모두 사용하고 있는 것이 아니고, 꽉 차 있지도 않다.

    - 얼마나 메모리를 사용할지는 개발자가 결정한다.

  - 이 메모리를 각 디바이스에 할당할 수 있다.

    - 예를 들어 가장 낮은 주소의 1기가를 메인 메모리(DDR)

      - 0x0000_0000 부터 0x3fff_ffff까지

    - 그 위의 1기가를 UART

      - 0x4000_0000 부터 0x7fff_ffff까지

    - 그 위 1기가는 타이머, 나머지는 GPIO 식으로 할당할 수 있다.

  - 32비트 주소 버스를 원하는 주소로 보내기 위해

    - 앞 2비트는 어느 디바이스로 향할지 결정한다.

      - 2 : 4 decoder를 사용해서

      - 00 : Memory

      - 01 : UART

      - 10 : Timer

      - 11 : GPIO

    - 뒤 30비트는 디바이스의 메모리에 접근한다.