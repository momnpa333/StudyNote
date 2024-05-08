## 해시 테이블(Hash Table)

### 해시 테이블은 연관배열 구조를 이용하여 키(key)에 결과 값(value)을 저장하는 자료구조이다.

#### 연관배열 구조(associative array)란,

키(key) 1개와 값(value) 1개가 1:1로 연관되어 있는 자료구조이다. 따라서 키(key)를 이용하여 값(value)을 도출할 수 있다.

**연관배열 구조는 다음의 명령을 지원한다.**

- 키(key)와 값(value)이 주어졌을 때, 연관 배열에 그 두 값(key & value)을 저장하는 명령
- 키(key)가 주어졌을 때, 연관되는 값(value)을 얻는 명령
- 키(key)와 새로운 값(value)이 주어졌을 때, 원래 키에 연관된 값(value)을 새로운 값(value)으로 교체하는 명령
- 키(key)가 주어졌을 때, 그 키(key)에 연관된 값(value)을 제거하는 명령

위의 명령은 해시테이블에서도 동일하게 적용이 된다.

### 해시 테이블의 구조(Hash Table Data Structure)

![1_iHTnDFd3sR5FqjHD1FDu9A.png](https://velog.velcdn.com/post-images%2Fcyranocoding%2F8d25f580-b225-11e9-a4ce-730fc6b3757a%2F1iHTnDFd3sR5FqjHD1FDu9A.png)

_출처 : 위키백과 — 해시테이블_

### 해시 테이블은 키(Key), 해시함수(Hash Function), 해시(Hash), 값(value), 저장소(Bucket, Slot)로 이루어져 있다.

#### 키(key)는 해시함수(hash function)를 통해 해시(hash)로 변경이 되며 해시는 값(value)과 매칭되어 저장소에 저장이 된다.

- 키(key) : 고유한 값이며, 해시 함수의 input이 된다. 다양한 길이의 값이 될 수 있다. 이 상태로 최종 저장소에 저장이 되면 다양한 길이 만큼의 저장소를 구성해 두어야 하기 때문에 해시 함수로 값을 바꾸어 저장이 되어야 공간의 효율성을 추구할 수 있다.
    
- 해시함수(Hash Function) : 키(key)를 해시(hash)로 바꿔주는 역할을 한다. 다양한 길이를 가지고 있는 키(key)를 일정한 길이를 가지는 해시(hash)로 변경하여 저장소를 효율적으로 운영할 수 있도록 도와준다. 다만, 서로 다른 키(key)가 같은 해시(hash)가 되는 경우를 해시 충돌(Hash Collision)이라고 하는데, 해시 충돌을 일으키는 확률을 최대한 줄이는 함수를 만드는 것이 중요하다.
    
- 해시(Hash) : 해시 함수(Hash Function)의 결과물이며, 저장소(bucket, slot)에서 값(value)과 매칭되어 저장된다.
    
- 값(Value) : 저장소(bucket, slot)에 최종적으로 저장되는 값으로 키와 매칭되어 저장, 삭제, 검색, 접근이 가능해야 한다.
    

![1_yCYrVNOVhKJccNFkN76QHw.jpeg](https://velog.velcdn.com/post-images%2Fcyranocoding%2Fb6e11850-b225-11e9-8452-4532e58e59eb%2F1yCYrVNOVhKJccNFkN76QHw.jpeg)_출처 : [https://www.geeksforgeeks.org/implementing-hash-table-open-addressing-linear-probing-cpp/](https://www.geeksforgeeks.org/implementing-hash-table-open-addressing-linear-probing-cpp/)_

### Insertion(저장)

해시 테이블에서 자료를 저장하기 위해서는 해시 함수(Hash Fucntion)으로 키(key)를 해시(hash)로 변경해야 한다. 위의 사진처럼 해시 함수가 input key를 7로 나눈 나머지로 변경해서 출력했을 때, 키(key)는 ‘76’, 해시(Hash)는 ‘6’이다.

그러면 미리 준비해놓은 0, 1, 2, 3, 4, 5, 6의 저장소(bucket, slot) 중에 맞는 해시(hash)값을 찾아 해당 값(value)를 저장한다.

해시 함수로 해시를 산출하는 과정에서 서로 다른 key가 같은 hash로 변경되는 문제가 발생할 수 있는데, 이는 key 와 value가 1:1로 매칭이 되어야 한다는 규칠을 위배한 것이되므로 이 문제를 해결하면서 저장되어야 한다. 이는 **해시 충돌(Hash Collision)** 이라고 하며 해시출동 해결에 대한 내용은 후술 하겠다.

#### Insertion Big-O

저장 단계의 시간복잡도는 O(1)이다. 키(key)는 고유하며 해시함수(hash function)의 결과로 나온 해시(hash)와 값(value)를 저장소에 넣으면 되기 때문이다. 이 때, 해시함수의 시간복잡도는 함께 고려하지 않는다.  
하지만, 최악의 경우 O(n)이 될 수 있다. 해시 충돌로 인해 모든 bucket의 value들을 찾아봐야 하는 경우도 있기 때문이다. 이에 관해서도 해시충돌과 함께 후술하겠다.

### Deletion(삭제)

**저장되어 있는 값을 삭제할 때는 저장소에서 해당 key와 매칭되는 값(value)를 찾아서 삭제하면 된다. (저장소에는 hash와 value가 함께 저장되어 있으므로 함께 지우면 된다.)**

#### Deletion Big-O

삭제 단계의 시간복잡도는 O(1)이다. 키(key)는 고유하며 해시함수(hash function)의 결과로 나온 해시(hash)에 매칭되는 값(value)를 삭제하면 되기 때문이다. 이 때, 해시함수의 시간복잡도는 함께 고려하지 않는다.  
하지만, 최악의 경우 O(n)이 될 수 있다. 해시 충돌로 인해 모든 bucket의 value들을 찾아봐야 하는 경우도 있기 때문이다. 이에 관해서도 해시충돌과 함께 후술하겠다.

### Search(검색)

키(key)로 값(value)를 찾아내는 과정은 Deletion 과정과 비슷한다. 1) 키로 hash를 구한다. 2) hash로 값(value)를 찾는다.

#### Search Big-O

저장 단계의 시간복잡도는 O(1)이다. 키(key)는 고유하며 해시함수(hash function)의 결과로 나온 해시(hash)에 매칭되는 값(value)를 찾으면 되기 때문이다. 이 때, 해시함수의 시간복잡도는 함께 고려하지 않는다.  
하지만, 최악의 경우 O(n)이 될 수 있다. 해시 충돌로 인해 모든 bucket의 value들을 찾아봐야 하는 경우도 있기 때문이다. 이에 관해서도 해시충돌과 함께 후술하겠다.

## Hash Collision(해시 충돌)

해시테이블은 Insertion, Deletion, Search 과정에서 모두 평균적으로 O(1)의 시간복잡도를 가지고 있기 때문에 자료구조의 효율성 측면에서 매우 우수하다고 볼 수 있다. 하지만 이런 장점만 있는 것일까?

해시(hash)를 이용한 자료구조 방식에 필연적으로 나타날 수 있는 문제는,

> 무한한 값(해시테이블에서는 KEY를 의미한다.)을 유한한 값(해시 테이블에서는 Hash를 의미한다.)으로 표현하면서 서로 다른 두 개 이상의 유한한 값이 동일한 출력 값을 가지게 된다는 것이다.

다음의 예시를 보자

![1_Y_WWO5pFPN70lM7jOKsPHw.png](https://velog.velcdn.com/post-images%2Fcyranocoding%2F99252300-b226-11e9-89af-8fc0a61dbc3e%2F1YWWO5pFPN70lM7jOKsPHw.png)

John과 Sandra의 hash가 같다. 이런 현상을 hash collision이라고 한다.

앞에서도 말했지만 Hash Collision은 필연적으로 나타날 수 밖에 없다. n+1개의 비둘기가 n개의 비둘기 집에 들어간다면 적어도 1개 이상의 비둘기 집에 2마리 이상의 비둘기가 있을 것이다. (비둘기의 집의 원리)

그렇다면 이러한 문제를 해결하는 방법은 무엇일까?

### Collision Resolution!!

#### 1. Separate Chaining(간추려서 Chaining)

![1_6eBeaqTt_i8MxWPsw4xBgw.png](https://velog.velcdn.com/post-images%2Fcyranocoding%2F329e7e60-b226-11e9-a4ce-730fc6b3757a%2F16eBeaqTti8MxWPsw4xBgw.png)

Sandra가 들어가는데 충돌이 일어나니 기존에 있던 John의 값에 연결시켰다.  
체이닝(Chaining)은 자료 저장 시, 저장소(bucket)에서 충돌이 일어나면 해당 값을 기존 값과 연결시키는 기법이다.  
위의 사진에서 Sandra를 저장할 때 충돌이 일어났고, 기존에 있던 John에 연결시켰다. 이 때 연결리스트(Linked List) 자료구조를 이용한다. 다음에 저장된 자료를 기존의 자료 다음에 위치시키는 것이다.

Chaining 장단점

장점 :  
1) 한정된 저장소(Bucket)을 효율적으로 사용할 수 있다.  
2) 해시 함수(Hash Function)을 선택하는 중요성이 상대적으로 적다.  
3) 상대적으로 적은 메모리를 사용한다. 미리 공간을 잡아 놓을 필요가 없다.

단점 :  
1) 한 Hash에 자료들이 계속 연결된다면(쏠림 현상) 검색 효율을 낮출 수 있다.  
2) 외부 저장 공간을 사용한다.  
3) 외부 저장 공간 작업을 추가로 해야 한다.

### Chaining 시간복잡도(Big-O)

복잡도를 계산하기 전, 한 가지를 추가하자면 해시 테이블의 저장소(Bucket)의 길이를 ‘n’, 키(key)의 수를 ‘m’이라고 가정했을 때, 평균적으로 저장소에서 1개의 hash당 (m/n)개의 키가 들어있다. 이를 ‘α’라고 정의한다.

```null
m/n = α (1개의 Hash당 평균적으로 α개의 키가 들어있다.)
```

#### Insertion :

충돌이 일어났을 때, 해당 해시(Hash)가 가진 연결리스트의 Head에 자료를 저장할 경우, O(1)의 시간복잡도를 가진다. 해당 해시(Hash)를 산출하고 저장하면서 기존 값(value)를 연결하는 행위만 하면 되기 때문이다.

반면 Tail에 자료를 저장할 경우, O(α)의 시간 복잡도를 가진다. 해당 해시(Hash)를 저장할 때 모든 연결리스트를 지나서 Tail에 접근해야 하기 때문이다. 최악의 경우, O(n)의 시간 복잡도를 가진다. 한 개의 해시(Hash)에 모든 자료가 연결되어 있을 수 있기 때문이다.

#### Deletion & Search :

삭제와 검색은 시간 복잡도 측면에서 비슷한 개념을 공유한다. 산출된 Hash의 연결리스트를 차례로 살펴보아야 하므로 O(α)의 시간 복잡도를 가진다. 최악의 경우 O(n)의 시간복잡도를 가진다. 한 개의 해시(Hash)에 모든 자료가 연결되어 있을 수 있기 때문이다. 이 경우 모든 자료를 다 살펴보아야 한다.

![1__9O8Eyd9wEhZKhwrXzKJaw.png](https://velog.velcdn.com/post-images%2Fcyranocoding%2F7c9f8040-b226-11e9-89af-8fc0a61dbc3e%2F19O8Eyd9wEhZKhwrXzKJaw.png)_Hash를 임의로 바꾸어서 저장했다._

#### 2. Open Addressing(개방주소법)

개방주소법은 데이터의 해시(hash)가 변경되지 않았던 chaining과는 달리 비어있는 해시(hash)를 찾아 데이터를 저장하는 기법이다. 따라서 개방주소법에서의 해시테이블은 1개의 해시와 1개의 값(value)가 매칭되어 있는 형태로 유지된다.

위의 그림을 보면, Sandra가 저장될때 해시가 John으로 채워져 있어서 그 다음 Hash에 Sandra를 저장했다. 그리고 Ted의 해시도 Sandra가 저장되어 있으므로 그 다음 해시에 Ted를 저장했다. 이처럼 비어있는 해시를 찾아 저장하는 방법을 Open Addressing라고 한다.

이 때, 비어있는 해시(Hash)를 찾는 과정은 동일해야 한다.(일정한 규칙을 따라 찾아가야 한다.)

Open Addressing는 위에서 언급한 비어있는 해시를 찾는 규칙에 따라 다음과 같이 구분할 수 있다.

- 선형 탐색(Linear Probing): 다음 해시(+1)나 n개(+n)를 건너뛰어 비어있는 해시에 데이터를 저장한다.
- 제곱 탐색(Quadratic Probing): 충돌이 일어난 해시의 제곱을 한 해시에 데이터를 저장한다.
- 이중 해시(Double Hashing): 다른 해시함수를 한 번 더 적용한 해시에 데이터를 저장한다.

#### Open Addressing의 장단점

장점 :  
1) 또 다른 저장공간 없이 해시테이블 내에서 데이터 저장 및 처리가 가능하다.  
2) 또 다른 저장공간에서의 추가적인 작업이 없다.  
단점 :  
1) 해시 함수(Hash Function)의 성능에 전체 해시테이블의 성능이 좌지우지된다.  
2) 데이터의 길이가 늘어나면 그에 해당하는 저장소를 마련해 두어야 한다.

#### Open Addressing 시간 복잡도 (Big-O)

Chaining 에서 정의한 ‘α’를 Open Addressing 에서도 정의하자면, 해시 테이블의 저장소(Bucket)의 길이를 ’n’, 키(key)의 수를 ‘m’이라고 가정했을 때, ‘α’는 1보다 작거나 같다. 저장소 1개 버킷 당 1개의 값(value)만 가지기 때문이다.

```null
m/n = α (α <= 1)
```

#### Insertion & Deletion & Search :

삽입, 삭제, 검색 모두 대상이 되는 Hash를 찾아가는 과정에 따라 시간복잡도가 계산이 된다. 해시함수를 통해 얻은 Hash가 비어있지 않으면 다음 버킷을 찾아가야 한다. 이 찾아가는 횟수가 많아지면 많아질 수록 시간복잡도가 증가한다. 최상의 경우 O(1) ~ 최악의 경우 (O(n)).

따라서 Open Addressing에서는 비어있는 공간을 확보하는 것(= 저장소가 어느 정도 채워졌을 때 저장소의 사이즈를 늘려주는 것)이 필요하다.

최악의 경우 저장소를 모두 살펴보아야 하는 경우가 생길 수 있다.(O(n))

## Last : Hash Table Data Structure의 단점

- 순서가 있는 배열에는 어울리지 않는다.  
    : 상하관계가 있거나, 순서가 중요한 데이터의 경우 Hash Table은 어울리지 않다. 순서와 상관없이 key만을 가지고 hash를 찾아 저장하기 때문이다.
- 공간 효율성이 떨어진다.  
    : 데이터가 저장되기 전에 미리 저장공간을 확보해 놓아야 한다. 공간이 부족하거나 아예 채워지지 않은 경우가 생길 가능성이 있다.
- Hash Function의 의존도가 높다.  
    : 평균 데이터 처리의 시간복잡도는 O(1)이지만, 이는 해시 함수의 연산을 고려하지 않는 결과이다. 해시함수가 매우 복잡하다면 해시테이블의 모든 연산의 시간 효율성은 증가할 것이다.

해시 테이블은 키를 가지고 빠르게 value에 접근하고 조작할 수 있는 장점이 있어서 많이 사용된다. 예를 들어 주소록 저장형태의 경우 이름 — 전화번호의 매칭을 이용하여 데이터를 처리한다.