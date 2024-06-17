# multiset

```C++
#include <set>

void main()
{
    std::multiset<int> ms;
}
```

#### 1. multiset 개념
- set 헤더에 포함되어있음(multiset 헤더 아님)
- set과 달리 key값을 중복해서 저장할 수 있음
- set과 마찬가지로 값을 삽입하면 자동으로 정렬됨

```C++
mulltiset<int> ms;

ms.insert(10);
ms.insert(50);
ms.insert(20);
ms.insert(40);
ms.insert(30);
ms.insert(10);
ms.insert(10);
ms.insert(10);
```
위처럼 값을 넣으면 set이었다면 10 20 30 40 50이 저장되었겠지만  
multiset은 10 10 10 10 20 30 40 50이 저장됨  
그러므로 단지 find뿐 아니라 lower_bound(), upper_bound(), equal_range() 멤버함수들도 유용하게 사용됨  
  
#### 2. multiset의 장점
- set과 같으나 값을 중복해서 저장할 수 있음

#### 3. multiset의 단점
- set과 비슷함

#### 4. multiset의 함수원형
```C++
template < 
	class T,                        // set::key_type/value_type           
	class Compare = less<T>,        // 기본적으로 less(오름차순)이며 내림차순을 원한다면 greater옵션을 사용
	class Alloc = allocator<T>      // set::allocator_type           
> class multiset;
```
만약 내림차순 multiset을 만들고 싶다면 `multiset<int, greater<int>> ms;`처럼 사용하면 된다.

#### 5. multiset의 멤버 변수들
- key_type : The first template parameter (T)	
- value_type : The first template parameter (T)	

- key_compare : The second template parameter (Compare)
- value_compare : The second template parameter (Compare)
- allocator_type	The third template parameter (Alloc)

- reference : value_type&	
- const_reference : const value_type&	

- pointer: 	allocator_traits<allocator_type>::pointer
- const_pointer : 	allocator_traits<allocator_type>::const_pointer

- iterator : a bidirectional iterator to const value_type
- const_iterator : a bidirectional iterator to const value_type
- reverse_iterator : 	`reverse_iterator<iterator>`
- const_reverse_iterator : `reverse_iterator<const_iterator>`

- difference_type : 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- size_type :  size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)

#### 6. multiset의 멤버 함수들
1) 생성자 : multiset을 생성한다.
2) 소멸자 : multiset을 소멸한다.

3) 연산자
- operator= : multiset의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (multiset의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (multiset의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- rbegin : 역순으로 첫번째 (즉, multiset의 마지막 원소) 를 가리키는 반복자를 리턴한다.
- rend : 역순으로 끝 부분 (즉, multiset의 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
- cbegin, cend, crbegin, crend도 지원

5) 할당 관련
- empty : multiset이 비었는지 체크한다.
- size	: set의 size를 리턴한다.(현재 원소의 개수)
- max_size : 시스템상 최대로 할당할 수 있는 set의 최대 공간의 크기를 리턴한다.

6) 수정자 (Modifier)
- insert : 원소를 삽입한다.
- erase :  원소를 삭제한다.
- swap : Swap content (public member function)
- clear : Clear content (public member function)
- emplace : Construct and insert element (public member function)
- emplace_hint : Construct and insert element with hint (public member function)

7) Observers
- key_comp : Return comparison object (public member function)
- value_comp : Return comparison object (public member function)

8) Operations
- find	: Get iterator to element (public member function)
- count : Count elements with a specific value (public member function)
- lower_bound	: Return iterator to lower bound (public member function)
- upper_bound : Return iterator to upper bound (public member function)
- equal_range : Get range of equal elements (public member function)

9) 할당자
- get_allocator : 할당자(allocator) 을 얻는다.

#### 7. multiset 사용 팁들
1) lower_bound, upper_bound
```C++
auto start = ms.lower_bound(20); // 20이상이 처음 나온 iterator를 반환
auto end = ms.upper_bound(20); // 20초과가 처음 나온 iterator를 반환
```

2) equal_range
```C++
auto itr = ms.equal_range(20); 
// pair<multiset<int>::iterator, multiset<int>:iterator>처럼 pair객체를 반환
// pair의 first는 lower_bound와 같고, second는 upper_bound와 같음
```






출처 : https://cplusplus.com/reference/set/multiset/