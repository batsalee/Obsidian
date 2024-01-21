# deque  

```C++
#include <deque>

void main()
{
    std::deque<int> d;
}
```

#### 1. deque의 개념
- 새로운 값을 앞과 뒤 양쪽에 넣는 일이 많을 때 사용
- 양쪽에서 끝나는 큐(Double ended queue)라는 의미이며 `[데크]`라고 발음  
  
- vector는 공간을 할당할 때 뒤에만 공간을 남겨두고, 앞에는 공간을 남기지 않음
- 반면 deque는 앞과 뒤에 모두 공간을 남겨놓고 할당함
- 그래서 새로운 값을 앞과 뒤 양쪽에 넣을때 vector보다 빠름

- stack의 경우 top에서 pop과 push가 일어나고 반대쪽에서는 아무것도 할 수 없음
- queue의 경우 front에서는 삭제만 back에서는 삽입만 일어나지만, front에서 삽입이나 back에서 삭제를 할 수는 없다.  
- 반면 deque의 경우 스택과 큐를 합친 것이라 볼 수 있으며, 양쪽 끝에서 삭제와 입력 모두 수행할 수 있게 된다.  
  
- vector는 사전에 할당한 공간이 꽉 찼을 경우 새로 큰 메모리를 할당해서 기존의 값들을 옮기고 새로 추가된 값을 이어 붙여줌  
- 반면 deque는 메모리상에서 잘게 쪼개서 여러곳에 보관하고, 테이블에 각 조각의 위치를 기억함  
- 그래서 vector에서 공간이 부족할때 새로운 공간을 할당하고 모든 값을 옮기는 비효율이 deque에서는 발생하지 않음  
- 그러므로 새로운 값을 추가할 때 속도가 빠름
	- vector의 경우 평소는 O(1)이지만 공간이 가득 찼다면 O(N)이 되므로
- vector의 장점을 모두 가졌으나 앞공간도 비워야 하고 각 조각의 위치를 저장할 테이블이 필요하므로 메모리 공간이 더 많이 필요  
- 즉, 실행속도를 위해 메모리를 희생하는 컨테이너라고 생각할 수도 있지만 메모리 단편화를 다소 막아 줄 수 있으니 꼭 메모리를 더 쓴다고 말하기도 애매할 듯  

- 데이터가 선형으로 메모리 상에 위치하며 원소에 iterator를 통해 접근할 수 있고, 크기 할당은 자동으로 수행한다.  
- 처음과 끝이 아닌 중간위치에 insert나 erase 하는 속도도 vector보다 빠름  
	- 하지만 당연히 list보다는 느리므로 중간 위치에 삽입/삭제가 많으면 list 사용하기
    
#### 2. deque의 장점  
- 인덱스 값을 통해 접근할 수 있다.  
- iterator로 원소를 어떠한 방향으로도 참조해 나갈 수 있다.  
- 시작과 끝에 원소의 추가/삭제가 효율적이다.  
- 기존에 할당된 공간이 가득 차도 새로운 공간할당의 속도가 빠르다.

#### 3. deque의 단점  
- 모든 원소가 메모리 상에 연속적으로 존재한다고 보장할 수 없다.
	- 즉, 포인터 연산을 통해서 데크의 원소들을 안전하게 접근할 수 없다. 
- 메모리 조각들의 정보를 저장할 테이블이 필요하므로 일반적으로 메모리 공간을 더 사용하게 된다. 

#### 4. deque의 함수 원형  
```C++
template <class T, class Allocator = allocator<T>>  
class deque;  
```
- T : (보관하려는) 원소의 타입  
- Allocator : 많은 경우 생략함  
              어떠한 방식으로 메모리를 할당할지에 관련한 할당자(allocator) 타입을 나타낸다.   
              기본값으로 T 의 할당자 클래스 템플릿을 사용하며, Heap 에 할당하게 된다.  
    
#### 5. deque의 멤버 변수들  
- reference : Allocator::reference  
- const_reference : Allocator::const_reference  
  
- iterator : 임의 접근 반복자(random access iterator)  
- const_iterator : 상수 임의 접근 반복자 (즉, 접근하는 원소의 내용을 수정 못함)  
- reverse_iterator : 역 반복자 (끝에서 부터 참조해나간다) `reverse_iterator<iterator>`
- const_reverse_iterator : 상수 역 반복자 `reverse_iterator<const_iterator>`
  
- size_type : 데크 size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)  
- difference_type : 데크 내의 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)  
- value_type : 원소 타입 (T)  
- allocator_type : 할당자  

- pointer : 포인터 (Allocator::pointer)  
- const_pointer : 상수 포인터 (Allocator::const_pointer)  

#### 6. deque의 멤버 함수들  
1) 생성자 : 데크를 생성한다.  
2) 소멸자 : 데크를 소멸한다.  
3) 연산자  
  - operator= : 데크의 내용을 복사한다.  
  
4) 반복자  
  - begin : 시작 부분 (첫번째 원소) 을 가리키는 반복자를 리턴한다.  
  - end : 끝 부분 (마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.  
  - rbegin : 역순으로 첫번째 (즉, 마지막 원소) 를 가리키는 반복자를 리턴한다.  
  - rend : 역순으로 끝 부분 (즉, 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
  - cbegin, cend, crbegin, crend도 지원  
  
5) 할당 관련  
  - 벡터와는 다르게 capacity 와 reserve 가 없음  
  - size : 데크의 size 를 리턴한다 (현재 원소의 개수)  
  - max_size : 데크 최대 크기를 리턴한다.  
  - resize : 데크가 size 개의 원소를 포함하도록 변경한다.  
  - empty : 데크가 비었는지 체크한다.
  - shrink_to_fit : Shrink to fit  
	- 할당된 공간을 현재 vector의 size만큼 줄이는 함수
  
6) 접근자
  - operator[] : 원소에 접근한다.(임의 접근)  
  - at : 원소에 접근한다.  
  - front : 첫번째 원소에 접근한다.  
  - back : 마지막 원소에 접근한다.  
  
7) 수정자 (Modifier)  
  - assign : 데크에 원소를 집어넣는다.  
    
  - push_back : 데크 끝에 원소를 집어 넣는다.  
  - push_front : 데크 맨 앞에 원소를 집어 넣는다.  
  - pop_back : 마지막 원소를 제거한다.  
  - pop_front : 첫번째 원소를 제거한다.  
  
  - insert : 데크 중간에 원소를 추가한다.  
  
  - erase : 원소를 제거한다.  
  - swap : 다른 데크와 원소를 바꿔치기 한다.  
  - clear : 원소를 모두 제거한다.

  - emplace_front : 이것 대신 push_front 사용하기
  - emplace_back : 이것 대신 push_back 사용하기  
  
8) 할당자  
  - get_allocator : 할당자(allocator) 을 얻는다.

#### 7. deque 사용 팁들
- 덱의 제일 뒤에 값을넣을땐 push_back, 앞에 값을 넣을땐 push_front사용하면 된다.
- pop_back이나 pop_front도 사용 가능  




출처 : [https://cplusplus.com/reference/deque/deque/](https://cplusplus.com/reference/deque/deque/)