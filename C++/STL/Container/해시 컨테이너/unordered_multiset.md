# unordered_multiset

```C++
#include <unordered_set> // unordered_multiset이 아닌것에 주의

void main()
{
    std::unordered_multiset<int> ums;
}
```

#### 1. unordered_multiset의 개념
- unordered_set과 비슷하지만 값의 중복을 허용함

#### 2. unordered_multiset의 함수원형
```c++
template < 
	class Key,                            // 키 형식
	class Hash = std::hash<Key>,          // 해시함수 개체 형식
	class Pred = std::equal_to<Key>,      // 비교함수 형식
	class Alloc = std::allocator<Key>>    // 할당자 클래스
class unordered_multiset;
```

#### 3. unorderd_multiset의 멤버 변수들
- key_type : The first template parameter (T)	
- value_type : The first template parameter (T)	

- hasher : the second template parameter (Hash), defaults to : hash<key_type>
- key_equal : the third template parameter (Pred)|defaults to : equal_to<key_type>
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

#### 4. unordered_multiset의 멤버 함수들
1) 생성자 :unordered_multiset을 생성한다.
2) 소멸자 : unordered_multiset을 소멸한다.

3) 연산자
- operator= : unordered_multiset의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (unordered_multiset의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (unordered_multiset의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- cbegin, cend도 지원
- 단, rbegin이나 rend, crbegin, crend는 지원하지 않음

5) 할당 관련
- empty : unordered_multiset이 비었는지 체크한다.
- size	: unordered_multiset의 size를 리턴한다.(현재 원소의 개수)
- max_size : 시스템상 최대로 할당할 수 있는 unordered_multiset의 최대 공간의 크기를 리턴한다.

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
- operators (unordered_multiset) : Relational operators for unordered_set (function template)
- swap (unordered_multiset) : Exchanges contents of two unordered_set containers (fun



※ 참고 문헌
https://cplusplus.com/reference/unordered_set/unordered_multiset/