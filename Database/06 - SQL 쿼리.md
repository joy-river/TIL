### SQL

---

- SQL은 세가지 부분으로 구성되어 있다.

  - DDL

    - 스키마를 정의하기 위한 언어

  - DML

    - 데이터베이스에 정보를 요청하고, DB를 수정하는 언어.

  - DCL

    - DB의 트랜잭션을 통제하고, 인증과 접근을 통제하는 언어.

- SQL에는 Domain type이 정해져있다.

  - char(n) : 정해진 n 길이의 문자열

  - varchar(n) : 최대 n 길이를 자유롭게 가질 수 있는 문자.

  - int, smallint

  - numeric(p, d) : 고정점 표기, 총 p 개의 자연수에서 d개가 소수점이 된다.

  - real, double, float(n) : 부동 소수점 표기, n 자리까지 정확히 표기 가능.

### 관계(테이블) 만들기

---

- create table로 테이블을 만들 수 있다.

```
Create table r (
    A1 D1,
    A2 D2
)
```

- 이때, integrity를 추가적으로 테이블에 넣을 수 있다.

  - primary key, foreign key, not null...

  - SQL은 이 integrity가 위반되지 않도록 항상 체크한다.

    - 너무 많아질 경우 전체적인 속도 저하가 발생할 수 있다.

  - 외래 키는 특정 값이 다른 테이블의 기본 키임을 강제한다.

  - 기본 키 제약은 무조건 not null이며, 중복될 수 없다.

    - 다른 속성은 중복되어도 괜찮다.

- 테이블을 수정하는 명령어도 있다.

  - Insert

    - 특정 튜플을 삽입한다.

    - values 뒤에 서브 쿼리의 결과를 넣을 수도 있다.

    - null 값도 넣을 수 있다.

    ```
    insert into r values ()

    insert into table1 select * from table1 // error!
    ```

    - 삽입하는 테이블은 삽입 전에 완전히 계산이 끝나야만 한다.

  - Delete

    - 튜플을 삭제한다.

    ```
    delete from r (where)

    delete from instructor
    where salary < (select avg(salary) from instructor);
    ```

    - 만약 조건을 통해 삭제한다면, 삭제가 이뤄질 때 마다 avg(salary)가 변화할 수도 있다는 것을 유념해야한다.

      - 삭제할 모든 튜플을 골라놓고 삭제하는 것이 안전하다.

  - Update

    - 특정 칼럼의 튜플을 새로운 값으로 교체한다.

    ```
    update instructor
    set salary = salary * 1.05;
    ```

    - update r, set A = ~~~의 형태를 취한다.

    - 여러 조건을 통한 업데이트는 순서가 중요해진다.

    > 급여가 100000 이하면 1.05배, 100000 초과면 1.03배 인상한다.

        - 순서가 잘못되면 동일한 사람이 두번 인상을 받을 수 있다.

        - 이런 경우 case when을 사용할 수도 있다.

    ```
    update instructor
    set salary = case
    when salary <= 100000 then salary * 1.05
    else salary * 1.03
    end;
    ```

    - 또한 스칼라 서브 쿼리를 사용하여 업데이트 하는 경우, null 값에 주의해야한다.

      - sum(null, null, null)과 같은 경우, null값이 출력되므로 테이블 전체가 무너질 수 있다.

  - Drop table

    - 테이블을 삭제한다.

    ```
    drop table r
    ```

  - Alter table

    - 테이블에 특정 칼럼을 추가하거나, 특정 칼럼을 삭제한다(삭제는 대부분 지원되지 않는다.)

    ```
    alter table r add A D
    alter table r drop A
    ```

### SQL 쿼리

---

- 기본적으로 sql 쿼리문은 다음의 형태를 취한다.

  ```
  select A1, A2....
  from r1, r2....
  where P;
  ```

  - 쿼리의 결과는 항상 관계의 형태로 나오게 된다.

- 쿼리에서 등장하는 절(clause)의 의미와 활용법을 알아보자.

### Select

---

- 관계 대수에서 Projection과 동일한 기능을 한다.

- 쿼리에서 대소문자를 구별하지 않고, 특정한 칼럼 A를 뽑아낸다.

  - distinct : 중복을 허용하지 않는다.

  - all : 중복을 허용한다 (기본 설정이다.)

- select \* 을 통해 테이블의 모든 튜플을 뽑아낼 수 있다.

- select 하는 속성에 특정한 연산을 적용할 수 있다(G projection)

  ```
  select salary / 12 as yours
  from instructor;
  ```

  - 이름을 별도로 지정하지 않으면 연산 자체가 속성의 이름이 된다.

### From

---

- 관계 대수에서 곱집합(Cartesian Product)에 대응하는 개념이다.

- 기본적으론 요청의 대상이 되는 관계를 지정하지만, 여러 관계를 넣을 경우 자동으로 곱해준다.

### Where

---

- 관계 대수에서 선택 조건식에 대응하는 개념이다.

  - SQL은 and, or, not 등 논리적 연산과 부등호를 통한 연산 등을 제공한다.

  - 속성끼리 비교하거나, 속성과 상수를 비교할 수 있다.

    - between을 통한 비교도 가능하다.

- 조건식에 들어가는 식은 항상 T/F로 나와야 한다.

### String 연산

---

- 문자열 내부에서 특정한 패턴, 글자를 찾기 위한 명령어.

- where 절에서 like를 통해 사용할 수 있다.

  - % = Substring 찾기

  ```
  select name
  from student
  where dept_name like '%Comp%'; // 학과 이름에 Comp가 들어가는 학생 찾기
  ```

  - 'Comp%'로 Comp로 시작하는 학생을, '%Comp'로는 Comp로 끝나는 학생을 찾아낼 수 있다.

  - \_ = 글자 수 찾기

  ```
  select name
  from student
  where dept_name like '_______'; // 학과 이름이 7글자인 학생 찾기
  ```

  - '\_\_\_%' 와 같이 사용하면 적어도 3글자인 학생을 찾을 수 있다.
