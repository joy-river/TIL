### Timer

---

- 아주 심플한, 그러나 중요한 디바이스이다.

  - 시간을 측정하기 위해 사용한다.

- 그럼 시간을 측정하는 이유가 뭔가?

  - 하드웨어간 거리(distance)를 각 하드웨어의 타이머 간격을 통해 측정할 수 있다.

  - 여러 프로세스를 동시에 실행하는 Time Multiplexing Scheduling에 필수적이다.

- 우리는 Private Timer에 대해 다루게 된다.

  - 다른 IO디바이스와 마찬가지로 타이머도 세 종류의 레지스터를 가진다.

    - Configuration

    - Data

    - Status

- 이 타이머는 100에서 1씩 감소하는 Decrementing timer이다.

  - 타이머의 값이 0이 되면 인터럽트를 발생시킨다.

  - 클락의 rising edge마다 flip/flop을 통해 1씩 변화한다.

    - 첫 rising edge에 counter 신호가 발생하면, 다음 rising edge에 이를 counter_next를 적용한다.

    - -1인 0xffff_Ffff를 계속해서 더한다.

  - 이때 타이머에 들어오는 클락은 PERIPHCLK라는 디바이스 고유의 클락이며, 일반적으로 cpu의 절반인 333MHZ다.

    - 더 느린 클락을 만들고 싶다면, prescalar 값 + 1로 333MHZ를 나눠서 새로운 클락을 만들 수 있다.

- Control register를 통해 여러가지 설정이 가능하다.

  - [0] = timer enable

    - 타이머를 활성화해 카운터를 감소시킬지를 결정한다.

  - [1] = Auto-reload

    - auto-reload

      - Periodic한 타이머를 만들때 설정한다.

      - load, counter 레지스터 둘을 가지며, load 레지스터에 타이머의 시작값을 넣는다.

      - counter가 0이 되면 인터럽트를 발생시키고, 다시 load값을 불러와서 카운트한다.

    - single-shot

      - counter 레지스터가 한번 카운트를 하고 끝난다.

  - [2] = IRQ enable

    - 인터럽트를 발생시킬지를 결정한다.

  - [15:8] = clock divider

    - 더 느린 클락을 만들 수 있는 Prescalar 값이다.

        - 이 값을 사용할 때는 << 8 을 통해 비트 위치를 이동시켜줘야한다.

    - clock_out 값은 clock_in / (Prescalar + 1)로 계산할 수 있다.

    - Time interval은 최종적으로 (Prescalar + 1) \* (load_value + 1) / PERIPHCLK가 된다.

- Interrupt status register는 인터럽트를 담당한다.

    - [0] 비트가 event flag가 되며, 카운터가 0이 되었을 때 자동적으로 1 이 된다.

    - 1이 되면 자동적으로 0으로 바뀌게끔 구현되어있다.