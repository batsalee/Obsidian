# queue

```C++
#include <queue>

void main()
{
	std::queue<int> q;
}
```

#### 1. queue의 개념
[[자료구조/Queue|Queue]]

#### 2. queue의 함수 원형
```C++
template <class T, class Container = deque<T>>
class queue;
```
deque 기반으로 만들어진 컨테이너 어댑터

#### 3. queue의 멤버 변수들
- value_type : The first template parameter (T),	Type of the elements
- container_type : The second template parameter (Container), Type of the underlying container
- reference : container_type::reference, usually value_type&
- const_reference : container_type::const_reference,	usually const value_type&
- size_type : an unsigned integral type, usually the same as size_t

#### 4. queue의 멤버 함수들
- 생성자 : Construct queue (public member function)

- empty : Test whether container is empty (public member function)
- size : Return size (public member function)
- front : Access next element (public member function)
- back : Access last element (public member function)

- push : Insert element (public member function)
- pop : Remove next element (public member function)
 
- emplace : Construct and insert element (public member function)
- swap : Swap contents (public member function)

#### overload된 Non-멤버 함수들
- relational operators : Relational operators for queue (function)
- swap (queue) : Exchange contents of queues (public member function)

#### 특수화된 Non-멤버 클래스
- `uses_allocator<queue>` : Uses allocator for queue (class template)






※ 참고 문헌
https://cplusplus.com/reference/queue/queue/