# map

map은 과거에는 이진탐색트리로, 현재에는 레드블랙트리로 구현되어있다.

맵은 셋과 거의 똑같은데 셋은 키만 보관했지만 맵은 키에 대응하는 값까지 보관함
맵 또한 중복된 값의 입력은 무시함  
  
맵의 insert는 pair 객체를 인자로 받음  
m.insert(std::pair<std::string, double>("박세웅", 2.23));  
혹은 make_pair함수로 자료형 길게 안적고 가능  
m.insert(std::make_pair("박세웅", 2.23));  
혹은 insert를 안쓰고 그냥 인덱스에 대입도 가능  
m["박세웅"] = 2.23; // 이미 키가 있으면 값이 바뀌고, 없으면 새로 추가해줌  
  
값에 접근은  
itr->first와 itr->second로 키와 value에 접근  
  
[]연산자로 값에 접근할때 주의점  
cout << m["박세웅"]; 하면 알아서 2.23을 출력해줌  
근데 문제는 없는 키에 접근해서 출력을 해도 오류를 일으키지 않고 0을 출력해줌  
cout << m["류현진"]; 하면 그냥 디폴트값을 넣어서 생성해버리고 출력함  
그러니 []로 값을 접근할거면 find로 먼저 값이 있는지 확인하고 있으면 출력하는 식으로 해야함  
  
만약   
m.insert(std::make_pair("박세웅", 2.23));  
m.insert(std::make_pair("박세웅", 3.59));  
처럼 입력하면 이미 있는 값의 insert는 무시하므로 2.23에서 값이 바뀌지 않음  
값을 바꾸려면  
m["박세웅"] = 3.59;로 써야함  


```C++
#include <map>

void main()
{
    std::map<int> m;
}
```

#### 1. map의 개념
- 중복을 허용하지 않음(중복을 허용하는 map은 multimap)
- 값을 정렬해서 보관(값을 정렬하지 않는 map은 unordered_map)
- 키값에 대응하는 값이 무엇인지 확인하는 목적이 강함
- 과거에는 이진탐색트리로 구현되었지만 최근에는 레드블랙트리로 구현됨

#### 2. map의 장점

#### 3. map의 단점

#### 4. map의 함수원형
```C++
template < class T,             // set::key_type/value_type           
class Compare = less<T>,        // 기본적으로 less(오름차순)이며 내림차순을 원한다면 greater옵션을 사용
class Alloc = allocator<T>      // set::allocator_type           
> class set;
```

#### 5. map의 멤버 변수들
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

#### 6. map의 멤버 함수들
1) 생성자 : set을 생성한다.
2) 소멸자 : set을 소멸한다.

3) 연산자
- operator= : set의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (set의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (set의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- rbegin : 역순으로 첫번째 (즉, set의 마지막 원소) 를 가리키는 반복자를 리턴한다.
- rend : 역순으로 끝 부분 (즉, set의 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
- cbegin, cend, crbegin, crend도 지원

5) 할당 관련
- empty : set이 비었는지 체크한다.
- size	: set의 size를 리턴한다.(현재 원소의 개수)
- max_size : 시스템상 최대로 할당할 수 있는 set의 최대 공간의 크기를 리턴한다.

7) 수정자 (Modifier)
- insert : 원소를 삽입한다.
- erase :  원소를 삽입한다.
- swap : Swap content (public member function)
- clear : Clear content (public member function)
- emplace : Construct and insert element (public member function)
- emplace_hint : Construct and insert element with hint (public member function)

8) Observers
- key_comp : Return comparison object (public member function)
- value_comp : Return comparison object (public member function)

9) Operations
- find	: Get iterator to element (public member function)
- count : Count elements with a specific value (public member function)
- lower_bound	: Return iterator to lower bound (public member function)
- upper_bound : Return iterator to upper bound (public member function)
- equal_range : Get range of equal elements (public member function)

10) 할당자
- get_allocator : 할당자(allocator) 을 얻는다.



출처 : https://cplusplus.com/reference/map/map/