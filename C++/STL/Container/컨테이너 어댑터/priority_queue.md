# priority_queue

```C++
#include <queue> // priority_queue가 아닌 queue헤더에 있음

void main()
{
	std::priority_queue<int> q;
}
```

#### 1. priority_queue의 개념
[[자료구조/선형 구조/priority_queue|priority_queue]]

#### 2. priority_queue의 함수 원형
```C++
template <
	class T, 
	class Container = vector<T>,  
	class Compare = less<typename Container::value_type> 
> 
class priority_queue;
```
vector 기반으로 만들어진 컨테이너 어댑터  
기본값은 less로 큰 값이 우선적으로 나오게 되어있지만 작은 값이 우선적으로 나오게 greater로 쓰고 싶다면 `priority_queue <int, vector<int>, greater<int>> pq;`처럼 생성 후 사용

#### 3. priority_queue의 멤버 변수들
- value_type : The first template parameter (T),	Type of the elements
- container_type : The second template parameter (Container), Type of the underlying container
- reference : container_type::reference, usually value_type&
- const_reference : container_type::const_reference,	usually const value_type&
- size_type : an unsigned integral type, usually the same as size_t

#### 4. priority_queue의 멤버 함수들
- 생성자 : Construct priority_queue (public member function)

- empty : Test whether container is empty (public member function)
- size : Return size (public member function)
- top : Access top element (public member function) // 가장 높은 숫자이자 첫 칸에 있는 값

- push : Insert element (public member function)
- pop : Remove next element (public member function)
 
- emplace : Construct and insert element (public member function)
- swap : Swap contents (public member function)

#### overload된 Non-멤버 함수들
- swap (queue) : Exchange contents of queues (public member function)

#### 특수화된 Non-멤버 클래스
- `uses_allocator<queue>` : Uses allocator for queue (class template)

#### 5. priority_queue 사용 팁들
1) 값을 넣는순서에 상관없이 가장 큰 값이 앞으로 간다.
```C++
priority_queue<int> pq;

pq.push(5);
pq.push(3);
pq.push(4);
pq.push(1);
pq.push(2);

while(!pq.empty()) {
	cout << pq.top();
	pq.pop();
}
// 출력결과 : 54321
```

2) push와 emplace의 차이점
- push는 오브젝트를 생성 후 queue에 값을 복사해서 넣어주는 방식으로 불필요한 복사가 발생 할 수 있음
- emplace는 오브젝트를 생성하지 않고 바로 값을 넣어주는 방식
- 다만 개인적인 생각인데 현시점에서는 컴파일러가 알아서 복사생략 해주지 않을까 싶어서 상관없지 않을까 싶기도 하고...

3) 오름차순 priority_queue 만들기
큰 값부터 나오는 것이 아니라 작은 값부터 나오게 만들 고 싶다면  
`priority_queue <int, vector<int>, greater<int> > pq;`처럼 생성 후 사용

4) 정렬 우선순위 지정하는 법
람다함수가 template 안이라서 오류가 나는듯함
그래서 함수객체를 만들어서 사용
```C++
struct cmp 
{ 
	bool operator()(int n1, int n2) 
	{ 
		if (abs(n1) > abs(n2)) return true;
		else if (abs(n1) == abs(n2)) {
			if (n1 > n2) return true; 
			else return false; 
		} 
		else return false; 
		} 
	}; 
	
std::priority_queue<int, std::vector<int>, cmp> pq3;
```



※ 참고 문헌
https://cplusplus.com/reference/queue/priority_queue/