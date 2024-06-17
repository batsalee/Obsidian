# multimap

```C++
#include <map>

void main()
{
    std::multimap<string, int> mm;
}
```

#### 1. multimap의 개념
- map 헤더에 포함되어있음(multimap 헤더 아님)
- map과 달리 key값을 중복해서 저장할 수 있음
- map과 마찬가지로 값을 삽입하면 자동으로 정렬됨

다만 multimap의 경우 `operator[]`를 이용한 원소 추가/수정이 불가능함  
```C++
mm.insert(std::make_pair("박세웅", 2.23));  
mm.insert(std::make_pair("박세웅", 3.59));  
```
하면 두개의 값이 다 들어있으니 `m["박세웅"] = ?` 식으로 접근이 불가능함  
둘 중 어디에 접근해야 할 지 알수 없으므로 \[\]연산자를 제공하지 않음  
그럼 어떻게 접근하냐면  
```C++
auto range = mm.equal_range("박세웅");  
for (auto itr = range.first; itr != range.second; ++itr) {
	std::cout << itr->first << " : " << itr->second << " " << std::endl;  
}
```
처럼 equal_range()에 키를 인자로 주면 대응하는 키들의 범위를 pair객체로 만들어서 리턴해줌  
그럼  아래처럼 출력 가능  
```
박세웅 : 2.23  
박세웅 : 3.59  
```

#### 2. multimap의 장점
- map과 같으나 값을 중복해서 저장할 수 있음

#### 3. multimap의 단점
- map과 비슷함

#### 4. multimap의 함수원형
```C++
template < 
	class Key,                                     // map::key_type           
	class T,                                       // map::mapped_type           
	class Compare = less<Key>,                     // map::key_compare           
	class Alloc = allocator<pair<const Key,T> >    // map::allocator_type           
> class multimap;
```
만약 내림차순 multimap을 만들고 싶다면 `multimap<string, int, greater<string>> mm;`처럼 사용하면 된다.

#### 5. multimap의 멤버 변수들
- key_type : The first template parameter (T)	
- mapped_type : The second template parameter (T)
- value_type : `pair<const key_type,mapped_type>`

- key_compare : The third template parameter (Compare)
- value_compare : Nested function class to compare elements
- allocator_type : The fourth template parameter (Alloc)

- reference : value_type&	
- const_reference : const value_type&	

- pointer: 	allocator_traits<allocator_type>::pointer
- const_pointer : allocator_traits<allocator_type>::const_pointer

- iterator : a bidirectional iterator to const value_type
- const_iterator : a bidirectional iterator to const value_type
- reverse_iterator : `reverse_iterator<iterator>`
- const_reverse_iterator : `reverse_iterator<const_iterator>`

- difference_type : 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- size_type :  size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)

#### 6. multimap의 멤버 함수들
1) 생성자 : multimap을 생성한다.
2) 소멸자 : multimap을 소멸한다.

3) 연산자
- operator= : multimap의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (multimap의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (multimap의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- rbegin : 역순으로 첫번째 (즉, multimap의 마지막 원소) 를 가리키는 반복자를 리턴한다.
- rend : 역순으로 끝 부분 (즉, multimap의 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
- cbegin, cend, crbegin, crend도 지원

5) 할당 관련
- empty : multimap이 비었는지 체크한다.
- size	: multimap의 size를 리턴한다.(현재 원소의 개수)
- max_size : 시스템상 최대로 할당할 수 있는 multimap의 최대 공간의 크기를 리턴한다.

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

#### 7. multimap 사용 팁들
1) lower_bound, upper_bound
```C++
auto start = mm.lower_bound(20); // 20이상이 처음 나온 iterator를 반환
auto end = mm.upper_bound(20); // 20초과가 처음 나온 iterator를 반환
```

2) equal_range
```C++
auto itr = mm.equal_range(20); 
// pair<multimap<int>::iterator, multimap<int>:iterator>처럼 pair객체를 반환
// pair의 first는 lower_bound와 같고, second는 upper_bound와 같음
```






출처 : https://cplusplus.com/reference/map/multimap/