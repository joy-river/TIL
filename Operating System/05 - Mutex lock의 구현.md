### Mutex lock의 조건

---

- 가장 먼저, 모든 cs는 마치 하나의 atomic instruction과 같아야한다.

  > balance = balance + 1

  - 공유하는 변수에 대한 표준적인 업데이트 등이 이에 해당한다.

  - cs가 될 수 있는 조건은 다음과 같다.

    - 최소 하나의 공유되는 변수

    - 최소 하나의 update(write)와 read 연산을 가진다.

- cs의 위아래를 lock과 unlock으로 감싸준다.

- Lock 변수는 현재 락이 어떤 상태인지를 저장한다.

  - available = unlocked = free = 0

  - acquired = locked = held = 1

- lock()

  - 락을 얻으려고 시도하고, 락이 free하면 얻는다.

  - 락을 얻으면 cs에 접근한다.

  - POSIX에서 제공하는 API는 pthread_mutex_lock()이 된다.

    - 먼저 lock 변수를 pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER로 초기화한다.

  - 서로 다른 cs가 있다면, 각각의 cs가 서로 다른 lock을 가지는 것이 좋다.

    - 이를 Fine-grained approach라고 하며, 동시성을 증가시킨다.

    - 여러 cs에 단 하나의 락만 존재한다면, Course-grained approach라고 칭한다.

### Mutex lock의 평가 기준

---

- 저비용으로 상호 배제를 구현할 수 있는 효율적인 락을 만들자.

  - 이를 위해서는 하드웨어와 OS의 도움이 필요하다.

- 그럼 효율의 기준은 무엇인가?

  > Correctness

  - 락이 자신의 기능을 제대로 수행해야한다. 즉, 상호 배제를 예외없이 구현해야한다.

  > Fairness

  - 각 스레드가 공평하게 락을 얻을 기회를 가져야한다. 어떤 스레드도 한없이 굶주림(Starvation)을 겪지 않는다.

  > Performance

  - 락을 사용함으로 인한 시간 지연(Overhead)의 크기가 작아야한다.

- 뮤텍스 락을 구현하는 방법은 다수 존재하며, 이 세가지 기준을 통해 평가한다.

- 후술할 락들은 하드웨어의 지원을 기반으로 구현한 것이다.

### Mutex lock(Controlling Interrupt)

---

- 인터럽트를 제어하여 락을 구현하는 아주 초창기의 방법이다.

- 싱글 프로세서 시스템을 위한 구현 방법이다.

  - cpu는 시간 공유 자원에 해당하므로, 각 스레드는 자신에게 주어진 시간 만큼만 cpu를 사용할 수 있다.

  - 주어진 시간이 지나면 타이머 인터럽트가 호출되어 스레드를 중단시키고, 다음 실행될 스레드와 문맥 교환(Context switching)을 발생시킨다.

    - 인터럽트는 문맥 교환을 위한 함수라고 볼 수 있다.

  - 만약 스레드 1이 cs 내부에서 인터럽트를 받을 경우, 다음 스레드 2는 cs에 접근할 수 없다.

  - 따라서 cs에 접근하기 전 인터럽트를 비활성화하여 스레드가 계속 실행되게 한다.

  - cs에서 실행이 끝나면, 인터럽트를 다시 활성화해 스레드를 멈춘다.

- 초창기의 방식인 만큼 많은 문제점이 존재한다.

  1. 응용프로그램에 대한 너무 많은 신뢰가 필요하다.

     - 탐욕적(Greedy)인 프로그램이 cpu를 독점할 가능성이 있다.

  2. 멀티 프로세서 시스템에선 사용이 불가능하다.

  3. 인터럽트를 통제하는 것은 느리게 실행되므로, 아주 낮은 성능을 가진다.

### Mutex lock(Flag)

---

- 락이 현재 free한지 아닌지를 판단하는 flag를 만들자.

  - Unlock = 0, lock = 1

- 락을 얻을 때 플래그를 테스트하고, 락을 세트한다(Test and set)

  - 락이 free해질 때 까지 아무것도 하지 않으면서 계속 대기한다.(Busy waiting)

  - 단순한 형태의 spin lock이다.

- 이 과정에서 Atomic한 연산이 쓰이지 않았으므로, 문제가 발생한다.

  - 상호 배제가 제대로 구현되지 않는다.

  - Correctness를 위반했다.

- 그럼 어떡함 = 하드웨어가 제공하는 Atomic한 명령어를 사용하자.

  - Atomic exchange(Atomic TSL)는 아래 과정을 하나의 사이클로 실행한다.

    1. 포인터에서 old 값을 받아온다.

    2. 포인터에 new 값을 저장한다.

    3. old 값을 반환한다.

  - 비슷한 명령어로 Compare and swap도 존재한다.

    - exchange는 단순히 교환하지만 Compare는 비교 과정도 실행한다.

- 이렇게 만든 spin lock 을 평가해보자.

  - 상호배제를 구현했으므로, Correctness를 만족한다.

  - spin이 영원히 지속될 수도 있으며, 다음에 어떤 스레드가 올지 순서를 알지도 못한다. 따라서 Fairness를 충족하지 않는다.

  - 여러 cpu에서 스레드 개수가 cpu와 비슷할 때는 큰 손실은 없으나, 단일 cpu에선 엄청난 지연이 발생한다. 따라서 Performance도 애매하다.

### Mutex lock(Ticket)

---

- Fetch and add 라는 atomic 명령어를 통해 구현한다.

  - 하드웨어가 제공하는 연산이다.

  - 값을 1 증가시켜 저장하고, 과거의 old 값을 리턴한다.

- Fairness를 제공하기 위해 숫자를 가진 ticket을 제공하고, 각 스레드가 자신이 가진 숫자와 티켓의 숫자가 같을 때 lock을 얻게 만든다.

  - 모든 스레드가 공정하게 락을 얻을 기회를 가질 수 있다.

  - 마찬가지로 숫자가 다를 때에는 spin하면서 대기한다.

### Mutex lock(Queue)

---

- Spinning을 피하기 위해선 하드웨어적 지원에 더해서 OS의 지원도 필요하다.

- OS를 통해 spin하게 됐을 때, 다른 스레드에게 cpu를 넘겨준다.

  - OS의 시스템 콜을 통해 running -> ready로 상태 전환이 일어난다.

  - 즉, spin 대신 sleep한다.

- 큐는 락을 얻기 위해 대기하는 스레드들을 추적하기 위한 개념이다.

  - park()를 통해 호출한 스레드를 sleep시키고, 큐에 더한다.

  - unpark(threadID)로 특정 스레드를 깨운다.
