# list

```C++
#include <list>

void main()
{
    std::list<int> d;
}
```

#### 1. list의 개념
1) 양방향 연결 구조를 가진 자료형(이중 연결 리스트)
	- 1~6까지 있다면 1과 2가 서로 가리킴, 2와 3이 서로 가리킴, ... 5와 6이 서로 가리킴
2) 원소의 삽입, 삭제, 원소의 위치변경의 빈도가 많을 때 사용
	- 삽입할 위치의 바로 앞뒤 값 두개만 변경하면 되므로
	- list는 insert나 erase를 해도 iterator 값이 무효화되지 않음(가리키는 곳만 달라지므로)
	- 따라서 정렬 알고리즘 처럼 원소의 이동이 빈번하게 일어나는 곳에 적용하면 효율적이다.
	- 자체 함수도 많이 지원해줌
	- 정렬을 위한 list::sort 함수, 원소 이동 관련 list::splice 함수가 좋은 성능으로 기본제공됨
3) 리스트는 첫번째 원소의 위치와 마지막 원소의 위치만 기억함
	- 시작과 끝만 알면 타고 찾아갈 수 있음
	- 왼쪽 오른쪽이 서로 가리키고 있으므로
4) 즉시 접근이 불가능함
	- 메모리상에서 연속적으로 존재하지 않을 수 있음
	- 그러므로 특정 원소에 접근하려면 시작이나 끝에서부터 순서대로 찾아가야하는 비효율이 있음
	- vector는 index로 특정 위치에 바로 접근이 가능하지만 리스트는 불가능  
	- 그러므로 list에는 []나 at함수가 없음  
	- lst.begin()과 lst.end()는 가능하지만 `itr += 5;`처럼 사용은 불가능하고 오직 itr++, itr--, ++itr, --itr만 가능

#### 2.list의 장점
- 컨테이너 임의의 위치에 원소의 삽입/삭제가 상수 시간에 가능하다.
- 서로 다른 컨테이너 사이나 컨테이너 내부에서 원소들 간의 이동이 매우 효율적이다.
- 원소들을 앞에서 뒤로, 혹은 뒤에서 앞으로 선형시간에 참조해나갈 수 있다.
  
#### 3.list의 단점
- 원소들을 인덱스로 직접 참조하는 것이 비효율적
- 링크되는 다른 원소의 주소값을 저장하기 위해 메모리공간을 더 사용해야 함
	- 저장할 데이터의 크기가 작다면 배보다 배꼽이 커지게 됨
	- 1바이트 데이터를 저장하기 위해 4바이트의 주소값을 저장한다면 전체 메모리의 80%가 낭비되는 수준
  
#### 4. list의 함수 원형
```C++
template <class T, class Allocator = allocator<T>>
class list;
```
- T : (보관하려는) 원소의 타입  
- Allocator : 많은 경우 생략함  
              어떠한 방식으로 메모리를 할당할지에 관련한 할당자(allocator) 타입을 나타낸다.   
              기본값으로 T 의 할당자 클래스 템플릿을 사용하며, Heap 에 할당하게 된다.

#### 5. list의 멤버 변수들
- reference : Allocator::reference
- const_reference : Allocator::const_reference

- iterator : 양방향 반복자(Bidirectional iterator)
- const_iterator : 상수 양방향 반복자 (즉, 접근하는 원소의 내용을 수정 못함)
- reverse_iterator : 역 반복자 (끝에서 부터 참조해나간다) `reverse_iterator<iterator>`
- const_reverse_iterator : 상수 역 반복자 `reverse_iterator<const_iterator>`

- size_type : 리스트 size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)
- difference_type : 리스트 내의 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- value_type : 원소 타입 (T)
- allocator_type : 할당자

- pointer : 포인터 (Allocator::pointer)
- const_pointer : 상수 포인터 (Allocator::const_pointer)

#### 6. list의 멤버 함수들
1) 생성자 : 리스트를 생성한다.
2) 소멸자 : 리스트를 소멸한다.

3) 연산자
- operator= : 리스트의 내용을 복사한다.

4) 반복자
- begin : 시작 부분 (첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- rbegin : 역순으로 첫번째 (즉, 마지막 원소) 를 가리키는 반복자를 리턴한다.
- rend : 역순으로 끝 부분 (즉, 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
- cbegin, cend, crbegin, crend도 지원

5) 할당 관련  
- 벡터와는 다르게 capacity 와 reserve 가 없음
- size : 리스트의 size 를 리턴한다 (현재 원소의 개수)
- max_size : 리스트 최대 크기를 리턴한다.
- empty : 리스트가 비었는지 체크한다.

6) 양방향 접근
- front : 첫번째 원소에 접근한다.
- back : 마지막 원소에 접근한다.

7) 수정자(Modifier)
- assign : 리스트에 원소를 집어넣는다.
- push_back : 리스트 끝에 원소를 집어 넣는다.
- push_front : 리스트 맨 앞에 원소를 집어 넣는다.
- pop_back : 마지막 원소를 제거한다.
- pop_front : 첫번째 원소를 제거한다.

- emplace_front : 리스트 맨 앞에 원소를 넣음, 이것 대신 push_front를 쓰자
- emplace_back : 리스트 끝에 원소를 넣음, 이것 대신 push_back 쓰자

- insert : 리스트 중간에 원소를 추가한다.
- emplace : 값을 만들어 insert한다, 근데 이것 대신 insert 쓰자

- resize : 리스트가 size 개의 원소를 포함하도록 변경한다.
- erase : 원소를 제거한다.

- swap : 다른 리스트와 원소를 바꿔치기 한다.
- clear : 원소를 모두 제거한다.

8) 할당자
- get_allocator : 할당자(allocator) 을 얻는다.

9) 특별한 작업들(Operations)
- splice : 리스트와 리스트 사이에서 원소를 이동한다.

- remove : 특정 값을 지닌 원소를 삭제한다.
- remove_if : 특정 조건을 만족시 원소를 삭제한다.
- unique : 중복된 값을 삭제한다.

- merge : 정렬된 리스트를 합친다.
- sort : 컨테이너의 원소를 정렬한다.
- reverse : 원소들의 순서를 거꾸로 뒤집는다.

출처 : [https://cplusplus.com/reference/list/list/](https://cplusplus.com/reference/list/list/)