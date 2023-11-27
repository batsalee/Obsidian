# stack

```C++
#include <stack>

void main()
{
	std::stact<int> s;
}
```

#### 1. stack의 개념
[[자료구조/Stack|Stack]]

#### 2. stack의 함수 원형
```C++
template <class T, class Container = deque<T> > class stack;
```
deque 기반으로 만들어진 컨테이너 어댑터

#### 3. stack의 멤버 변수들
- value_type : The first template parameter (T),	Type of the elements
- container_type : The second template parameter (Container), Type of the underlying container
- reference : container_type::reference, usually value_type&
- const_reference : container_type::const_reference,	usually const value_type&
- size_type : an unsigned integral type, usually the same as size_t

#### 4. stack의 멤버 함수들
- 생성자 : Construct stack (public member function)

- empty : Test whether container is empty (public member function)
- size : Return size (public member function)
- top	: Access next element (public member function)

- push : Insert element (public member function)
- pop : Remove top element (public member function)
 
- emplace : Construct and insert element (public member function)
- swap : Swap contents (public member function)

#### overload된 Non-멤버 함수들
- relational operators : Relational operators for stack (function)
- swap (stack)	: Exchange contents of stacks (public member function)