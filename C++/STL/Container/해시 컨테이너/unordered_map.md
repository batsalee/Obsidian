# unordered_map

```C++
#include <unordered_map>

void main()
{
    std::unordered_map<std::string, int> um;
}
```

#### 1. unordered_map의 개념
- 정렬되지 않은 map
	- 단, insert된 순서대로 들어가는 것이 아닌 뒤죽박죽 저장됨
	- 해싱해서 저장하므로
- 트리구조가 아닌 해시로 구현됨
- 자동으로 정렬하지 않는다는 것 외에는 map과 비슷함
- 마찬가지로 중복을 허용하지 않으므로 같은 값이 여러번 insert되면 뒤의 입력은 무시됨
- 기본적으로는 map을 사용하되, 최적화가 정말 중요한 경우에 사용
	- 기본 라이브러리로 제공되는 자료형이 아닌, 직접 구현하는 상황에서는 해시키를 만드는데 주의해야 함
	- 많은 자료를 저장해야 하되 검색속도가 빨라야 할 경우(단 삽입, 삭제는 빈번하지 않을 때)

#### 2. unordered_map의 장점
- insert erase find가 엄청 빠름  
	- map은 이진탐색트리 구조였지만 unordered_map은 해시구조
	- insert erase find가 O(1)의 속도
- 속도가 매우매우 중요한 상황이라면 사용하기 좋음

#### 3. unordered_map의 단점
- 저장한 데이터가 적다면 오히려 해시함수를 수행하는 시간이 오버헤드일 수 있음
	- 그러므로 성능이 중요한 경우 사용해야 함에도 더 느릴 수 있으니 데이터가 수천개 이상일 때 사용할 것
- 해싱의 개념을 사용하므로 해시중돌이 발생할 수 있음
	- 이로 인해 기본적으로 탐색이 O(1)이겠지만 테이블이 가득찰수록 O(N)에 가까워짐
- 만약 할당된 공간에 값이 가득 채워져서 새로운 공간을 할당해야 하면 새로운 해시함수가 필요함
	- 더 많은 값들을 해시값으로 반환해야 하므로
	- 그 결과 기존의 모든 원소들도 새로운 해시함수로 전부 다시 insert해야함(rehash라고 부름)
	- rehash는 vector의 새로운 공간 할당처럼 O(N)의 시간이 걸리는 비효율이 발생할 수 있음

#### 4. unordered_map의 함수원형
```c++
template < 
	class Key,                            // 키 형식
	class Hash = std::hash<Key>,          // 해시함수 개체 형식
	class Pred = std::equal_to<Key>,      // 비교함수 형식
	class Alloc = std::allocator<Key>>    // 할당자 클래스
class unordered_map;
```

#### 5. unorderd_map의 멤버 변수들
- key_type : The first template parameter (Key)	
- mapped_type : the second template parameter (T)
- value_type :pair<const key_type,mapped_type>

- hasher : the third template parameter (Hash), defaults to : hash<key_type>
- key_equal : the fourth template parameter (Pred)|defaults to : equal_to<key_type>
- allocator_type : The fourth template parameter (Alloc), defaults to : allocator<value_type>

- reference : value_type&	
- const_reference : const value_type&	

- pointer: 	allocator_traits<allocator_type>::pointer, for the default allocator : value_type*
- const_pointer : allocator_traits<allocator_type>::const_pointer, 
			     for the default allocator : const value_type*

- iterator : a forward iterator to const value_type
- const_iterator : a forward iterator to const value_type
- local_iterator : a forward iterator to const value_type
- const_local_iterator : a forward iterator to const value_type

- difference_type : 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- size_type :  size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)

#### 6. unordered_set의 멤버 함수들
1) 생성자 : unordered_set을 생성한다.
2) 소멸자 : unordered_set을 소멸한다.

3) 연산자
- operator= : unordered_set의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (unordered_set의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (unordered_set의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- cbegin, cend도 지원
- 단, rbegin이나 rend, crbegin, crend는 지원하지 않음

5) 할당 관련
- empty : unordered_set이 비었는지 체크한다.
- size	: unordered_set의 size를 리턴한다.(현재 원소의 개수)
- max_size : 시스템상 최대로 할당할 수 있는unordered_set의 최대 공간의 크기를 리턴한다.

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
- equal_range : Get range of elements with a specific key (public member function)

9) Buckets
- bucket_count : Return number of buckets (public member function)
- max_bucket_count : Return maximum number of buckets (public member function)
- bucket_size : Return bucket size (public member type)
- bucket : Locate element's bucket (public member function)

10) Hash policy
- load_factor : Return load factor (public member function)
- max_load_factor : Get or set maximum load factor (public member function)
- rehash : Set number of buckets (public member function)
- reserve : Request a capacity change (public member function)

11) Observers
- hash_function : Get hash function (public member type)
- key_eq : Get key equivalence predicate (public member type)
- get_allocator : Get allocator (public member function)

#### Non-member function overloads
- operators (unordered_set) : Relational operators for unordered_set (function template)
- swap (unordered_set) : Exchanges contents of two unordered_set containers (fun



※ 참고 문헌
https://cplusplus.com/reference/unordered_map/unordered_map/