# 데이터베이스 인덱스 (INDEX)

## 인덱스란?

- RDBMS에서 **대용량**의 데이터(레코드)가 있을 때, 특정 데이터를 검색하기 위해서 테이블의 레코드를 full scan하는 것이 아니라,

- 인덱스가 적용된 컬럼의 테이블(컬럼, 인덱스주소)을 따로 **파일**로 저장해놓고 그것을 검색해서 검색 효율을 높이는 방법이다.

  

> **인덱스는 범위 스캔(Range Scan)을 한다**

인덱스는 키 컬럼순으로 정렬되어 있기때문에 특정값을 찾다가 해당 범위를 넘어서는 값을 만나면 멈춘다. (=Range Scan)

> **인덱스에 가장 많이 사용되는 구조 = B-tree**

인덱스를 저장하는 블럭들이 트리구조를 이루고 있다.

root block과 branch block, leaf block이 있고 B-tree는 기본적으로 **leaf block의 깊이가 모두 동일하게** 균형(Balanced)이 잡혀있다.

![img](https://t1.daumcdn.net/cfile/tistory/99BB6C4D5A45E8F935)

## 인덱스의 장점

- **키 값을 기초**로 하여 테이블에서 **검색과 정렬 속도를 향상**시킵니다.
- 질의나 보고서에서 **그룹화 작업의 속도를 향상**시킵니다.
- 인덱스를 사용하면 테이블 **행의 고유성을 강화**시킬 수 있습니다.
- 테이블의 **기본 키는 자동으로 인덱스** 됩니다.
- 필드 중에는 데이터 형식 때문에 인덱스 될 수 없는 필드도 있습니다.
- 여러 필드로 이루어진(다중 필드) 인덱스를 사용하면 첫 필드 값이 같은 레코드도 구분할  수 있습니다.
  참고로 액세스에서 다중 필드 인덱스는 최대 10개의 필드를 포함할 수 있습니다.

## 인덱스의 단점

- 인덱스를 만들면 **파일 크기가 늘어난다.**
- 여러 사용자 응용 프로그램에서의 여러 사용자가 한 페이지를 동시에 수정할 수 있는 **병행성이 줄어든다.**
- 인덱스 된 필드에서 데이터를 **업데이트**하거나, 레코드를 **추가 또는 삭제**할 때 **성능이 떨어집니다.**
- 인덱스가 데이터베이스 공간을 차지해 **추가적인 공간이 필요**해진다. (DB의 10퍼센트 내외의 공간이 추가로 필요)
- 인덱스를 생성하는데 시간이 많이 소요될 수 있다.
- 데이터 변경 작업이 자주 일어날 경우에 인덱스를 재작성해야 할 필요가 있기에 **성능에 영향을 끼칠 수 있다.**



따라서 어느 필드를 인덱스 해야 하는지 미리 시험해 보고 결정하는 것이 좋습니다. 인덱스를 추가하면 쿼리 속도가 1초 정도 빨라지지만, 데이터 행을 추가하는 속도는 2초 정도 느려지게 되어 여러 사용자가 사용하는 경우 레코드 잠금 문제가 발생할 수 있습니다.





## **인덱스는 언제 적용해야 좋을까?**

인덱스는 **select문의 where, join에서 좋은 성능**을 발휘한다. 대신 insert, update, delete문에서 성능이 떨어진다.

=> insert문의 경우 새로운 데이터를 삽입하면서 테이블뿐만 아니라 인덱스 테이블에도 생성을 해줘야 하며, 만약 인덱스의 leaf block이 꽉차 있으면 추가적인 작업이 더 필요하다

=> delete문의 경우 기존 테이블에서는 그냥 레코드를 삭제하고 그 공간을 다른 레코드가 사용할 수 있지만 인덱스 테이블은 사용 안함 표시만 하고 자리를 그대로 차지하기 때문에 유념해야함

=> update문은 delete하고 insert하는 식으로 처리하기 때문에 insert, delete문이 인덱스에 동시에 작용하기 때문에 부하가 많아짐

결론은 **검색(SELECT)이 많고 INSERT, UPDATE, DELETE문이 적게 일어나는 테이블에서** 인덱스를 사용하면 좋음.

 **인덱스 컬럼의 분포도가 좋아야(골고루 유일성을 갖게 분포) 한다**. (분포도의 관점에서 성별에 대한 인덱스는 이름에 대한 인덱스 보다 성능이 떨어진다. )



## RDBMS에서 Hash 인덱스를 안 쓰는 이유?

칼럼의 값으로 해시 값을 계산해서 인덱싱하는 해시는 매우 빠른 속도의 검색을 지원한다. 하지만 **eqaulity 연산(=)에 최적화 되 있는 해시**는, 부등호 연산에는 적합하지 않다. 메모리 기반의 DB에서는 많이 사용한다.



## Clusted Index

**클러스터(Cluster)란 여러 개를 하나로 묶는다는 의미**로 주로 사용되는데, 클러스터드 인덱스도 크게 다르지 않다. 인덱스에서 클러스터드는 비슷한 것들을 묶어 저장하는 형태로 구현되는데, 이는 주로 비슷한 값들을 동시에 조회하는 경우가 많다는 점에서 착안된 것이다. 여기서 비**슷한 값들은 물리적으로 인접한 장소에 저장**되어 있는 데이터들을 말한다.

클러스터드 인덱스는 테이블의 프라이머리 키에 대해서만 적용되는 내용이다. 즉 프라이머리 키 값이 비슷한 레코드끼리 묶어서 저장하는 것을 클러스터드 인덱스라고 표현한다.



### 클러스터와 비클러스터

**클러스터형 인덱스**

- 인덱스를 생성할 때는 데이터 페이지 전체를 다시 정렬한다.
- 인덱스 자체의 리프 페이지가 곧 데이터 페이지이다. 즉, 인덱스 자체에 데이터가 포함되어 있다.
- 비클러스터형보다 검색속도는 더 빠르다. 하지만 입력/수정/삭제는 느리다
- 성능이 좋지만, 테이블에 한 개만 생성할 수 있다. 그래서 어느 열에서 클러스터형 인덱스르 생성하느냐에 따라 시스템 성능이 달라진다.

**비클러스터형 인덱스**

- 데이터 페이지를 그냥 둔 상태에서 별도의 페이지에 인덱스를 구성한다
- 인덱스의 리프 페이지는 데이터가 아니라, 데이터가 위치하는 포인터이다. 
- 클러스터형 보다 검색속도는 느리지만 입력/수정/삭제는 더 빠르다
- 여러개 사용할 수 있지만 남용하면 시스템 성능을 떨어트린다.





## REFERNECE

https://jeong-pro.tistory.com/114

https://lalwr.blogspot.com/2016/02/db-index.html
