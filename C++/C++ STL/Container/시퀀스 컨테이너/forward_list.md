# forward_list

```C++
#include <forward_list>

void main()
{
    std::forward_list<int> fl;
}
```

### 1. forward_list의 개념
- 단방향 연결 리스트(단일 연결 리스트) 자료구조를 이용해 만든 시퀀스 컨테이너
- 그냥 list는 양방향 연결 리스트인데 반해 forward_list는 단방향 연결 리스트
- 즉 두 노드가 서로 가리키는 것이 아닌 한 노드가 다음의 노드만 가리킴

- list보다 메모리 사용량이 적고, 처리속도면에서 조금 유리함
- 단방향 list로 충분하다면 forward_list를, 양방향 list가 필요하다면 list를 사용하면 될 듯

- 기본적으로 설계디자인이 list에 맞춰져 있고, 속도와 사이즈같은 성능을 최우선으로 만들어짐
- list에 비해 한방향의 노드 주소만 기억하면 되므로 메모리를 적게 사용함
- 삽입/삭제도 하나의 노드값만 변경하면 되므로 list보다 아주 약간 더 빠름

- 하지만 다른 컨테이너에 비해 지원하지 않는 기능들이 있음
- size함수를 제공하지 않아서 size_t size = std::distance(list.begin(), list.end()); 처럼 구해야 함
- 또한 삽입과 삭제가 단방향이니 바로 한칸 앞의 노드를 삭제하려면 처음부터 다시 탐색해야 함
- 이런 문제때문에 list에는 있는 insert나 erase를 제공하지 않음
- insert 대신 insert_after(위치, 값) 함수를 사용해야 하는 듯

#### 2. forward_list의 장점
- 사이즈가 list보다 작음
- 삽입/삭제가 list보다 아주 약간 빠름

#### 3. forward_list의 단점
- 다른 컨테이너와 달리 지원하지 않는 기능들이 있음
- size / insert / erase함수 등을 지원하지 않음
- 양방향 리스트는 한칸앞이면 바로 이동이 가능하지만 단방향 리스트는 처음부터 다시 탐색을 해야 함

#### 4. forward_list의 함수원형
```C++
template <class T, class Allocator = allocator<T>>  
class forward_list;  
```
- T : (보관하려는) 원소의 타입  
- Allocator : 많은 경우 생략함  
		어떠한 방식으로 메모리를 할당할지에 관련한 할당자(allocator) 타입을 나타낸다.   
		기본값으로 T 의 할당자 클래스 템플릿을 사용하며, Heap 에 할당하게 된다.

#### 5. forward_list의 멤버 변수들
- reference : Allocator::reference
- const_reference : Allocator::const_reference

- list와는 다르게 reverse_iterator, const_reverse_iterator가 없음
- iterator : 단방향 반복자(forward iterator)
- const_iterator : 상수 단방향 반복자 (즉, 접근하는 원소의 내용을 수정 못함)

- size_type : 리스트 size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)
- difference_type : 리스트 내의 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptrdiff_t 와 타입이 같으며 부호있는 정수)
- value_type : 원소 타입 (T)
- allocator_type : 할당자

- pointer : 포인터 (Allocator::pointer)
- const_pointer : 상수 포인터 (Allocator::const_pointer)

#### 6. forward_list의 멤버 함수들
1) 생성자 : 리스트를 생성한다.
2) 소멸자 : 리스트를 소멸한다.

3) 연산자
- operator= : 리스트의 내용을 복사한다.

4) 반복자
- before_begin : Return iterator to before beginning
- begin : 시작 부분 (첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- cbefore_begin : Return const_iterator to before beginning
- cbegin : 시작 부분 (첫번째 원소) 을 가리키는 상수반복자를 리턴한다.
- cend : 끝 부분 (마지막 원소 바로 다음) 을 가리키는 상수반복자를 리턴한다.

5) 할당 관련
- max_size : 리스트 최대 크기를 리턴한다.
- empty : 리스트가 비었는지 체크한다.

6) 단방향 접근
- front : 첫번째 원소에 접근한다.

7) 수정자(Modifier)
- assign : 리스트에 원소를 집어넣는다.

- push_front : 리스트 맨 앞에 원소를 집어 넣는다.
- pop_front : 첫번째 원소를 제거한다.

- insert_after : 리스트 중간에 원소를 추가한다.
- erase_after : 원소를 제거한다.

- swap : 다른 리스트와 원소를 바꿔치기 한다.
- clear : 원소를 모두 제거한다.
- resize : 리스트가 size 개의 원소를 포함하도록 변경한다.

8) 할당자
- get_allocator : 할당자(allocator) 을 얻는다.

9) 특별한 작업들(Operations)
- splice_after : 리스트와 리스트 사이에서 원소를 이동한다.

- remove : 특정 값을 지닌 원소를 삭제한다.
- remove_if : 특정 조건을 만족시 원소를 삭제한다.

- unique : 중복된 값을 삭제한다.
- merge : 정렬된 리스트를 합친다.
- sort : 컨테이너의 원소를 정렬한다.
- reverse : 원소들의 순서를 거꾸로 뒤집는다.

출처 : [https://cplusplus.com/reference/forward_list/forward_list/](https://cplusplus.com/reference/forward_list/forward_list/)