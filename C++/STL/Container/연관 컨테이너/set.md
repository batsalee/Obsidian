# set

```C++
#include <set>

void main()
{
    std::set<int> s;
}
```

#### 1. set의 개념
- key값을 보관함
- 중복을 허용하지 않음(중복을 허용하는 set은 multiset)
- 값을 정렬해서 보관(값을 정렬하지 않는 set은 unordered_set)
- 데이터 중에 키값이 존재하는지 존재유무에 중점을 둠
- 이진탐색트리로 구현됨

```C++
set<int> s;

s.insert(10);
s.insert(50);
s.insert(20);
s.insert(40);
s.insert(30);
s.insert(10);
s.insert(10);
s.insert(10);
```
위처럼 값을 넣어도 값을 넣을때마다 자동정렬되므로 begin() ~ end()로 출력하면 10 20 30 40 50 순으로 출력됨  
또한 중복을 허용하지 않으므로 10은 한개만 들어있음  

```C++
std::cout << "s에 20이 들어는가? : ";  

auto itr = s.find(20);  
if (itr != s.end()) std::cout << "Yes" << std::endl;  
else std::cout << "No" << std::endl;  
```
위처럼 find해서 itr값이 끝까지 갔다면 값이 없는 것, 중간에 멈췄다면 값이 있는 것으로 값의 존재유무 파악에 주로 사용  
  
#### 2. set의 장점
- 값이 있냐 없냐를 판별하기에 더 좋은 구조
- 이진탐색트리구조로 되어있어 삽입/삭제/탐색이 모두 O(logN)
- 자동으로 정렬되므로 정렬기능이 필요한 경우 사용할 수 있음
- 값의 중복을 허용하지 않으므로 중복을 제거하고 싶을 때 사용할 수 있음

#### 3. set의 단점
- 값을 삽입할 때마다 트리의 구조를 변경해야 하므로 다른 컨테이너보다는 느릴 수 있음
- 값을 삽입할 때 복사 생성자를 사용하므로 큰 객체를 삽입할 경우 성능문제가 발생할 수 있음
- 중복을 허용하고 싶거나, 정렬을 원하지 않으면 다른 컨테이너를 사용하는 것이 좋음

#### 4. set의 함수원형
```C++
template < class T,             // set::key_type/value_type           
class Compare = less<T>,        // 기본적으로 less(오름차순)이며 내림차순을 원한다면 greater옵션을 사용
class Alloc = allocator<T>      // set::allocator_type           
> class set;
```
만약 내림차순 set을 만들고 싶다면 `set<int, greater<int>> s;`처럼 사용하면 된다.

#### 5. set의 멤버 변수들
- key_type : The first template parameter (T)	
- value_type : The first template parameter (T)	

- key_compare : The second template parameter (Compare)
- value_compare : The second template parameter (Compare)
- allocator_type : The third template parameter (Alloc)

- reference : value_type&	
- const_reference : const value_type&	

- pointer: 	allocator_traits<allocator_type>::pointer
- const_pointer : allocator_traits<allocator_type>::const_pointer

- iterator : a bidirectional iterator to const value_type
- const_iterator : a bidirectional iterator to const value_type
- reverse_iterator : 	`reverse_iterator<iterator>`
- const_reverse_iterator : `reverse_iterator<const_iterator>`

- difference_type : 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- size_type :  size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)

#### 6. set의 멤버 함수들
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

// 아래 3개의 함수는 사실상 multiset에서 사용되는 함수들
- lower_bound	: Return iterator to lower bound (public member function)
- upper_bound : Return iterator to upper bound (public member function)
- equal_range : Get range of equal elements (public member function)

9) 할당자
- get_allocator : 할당자(allocator) 을 얻는다.



출처 : https://cplusplus.com/reference/set/set/