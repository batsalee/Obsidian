# unordered_multimap

```C++
#include <unordered_map>  // unordered_multimap이 아닌것에 주의

void main()
{
    std::unordered_multimap<std::string, int> umm;
}
```

#### 1. unordered_multimap의 개념
- unordered_map과 비슷하지만 값의 중복을 허용함

#### 2. unordered_multimap의 함수원형
```c++
template < 
	class Key,                                    // 키 형식
	class T,                                      // unordered_map::mapped_type         
	class Hash = std::hash<Key>,                  // 해시함수 개체 형식
	class Pred = std::equal_to<Key>,              // 비교함수 형식
	class Alloc = allocator< pair<const Key,T> >  // 할당자 클래스
class unordered_multimap;
```

#### 3. unorderd_multimap의 멤버 변수들
- key_type : The first template parameter (Key)	
- mapped_type : the second template parameter (T)
- value_type :pair<const key_type,mapped_type>

- hasher : the third template parameter (Hash), defaults to : hash<key_type>
- key_equal : the fourth template parameter (Pred)|defaults to : equal_to<key_type>
- allocator_type : The fifth template parameter (Alloc), defaults to : allocator<value_type>

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

#### 6. unorderd_multimap의 멤버 함수들
1) 생성자 : unordered_multimap을 생성한다.
2) 소멸자 : unordered_multimap을 소멸한다.

3) 연산자
- operator= : unordered_multimap의 내용을 복사한다.

4) Capacity
- empty : Test whether container is empty (public member function)
- size : Return container size (public member function)
- max_size : Return maximum size (public member function)

5) 반복자 (Iterators)
- begin : 시작 부분 (unordered_map의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (unordered_map의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- cbegin, cend도 지원
- 단, rbegin이나 rend, crbegin, crend는 지원하지 않음

6) Element access
- operator[] : Access element (public member function)
- at : Access element (public member function)

7) Element lookup
- find	: Get iterator to element (public member function)
- count : Count elements with a specific key (public member function)
- equal_range : Get range of elements with a specific key (public member function)

8) Modifiers
- emplace : Construct and insert element (public member function)
- emplace_hint : Construct and insert element with hint (public member function)
- insert : Insert elements (public member function)
- erase : Erase elements (public member function)
- clear : Clear content (public member function)
- swap : Swap content (public member function)

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
- operators (unordered_multimap) : Relational operators for unordered_multimap (function template)
- swap (unordered_multimap) : Exchanges contents of two unordered_multimap containers (function template)


※ 참고 문헌
https://cplusplus.com/reference/unordered_map/unordered_multimap/