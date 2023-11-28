해쉬컨테이너로 분리해야 하나?

4) unordered_set과 unordered_map  
- 정렬되지 않은 셋과 맵  
말그대로 정렬되어있지 않음. 값들을 넣고 출력해보면 순서가 막 뒤섞여서 랜덤하게 나옴  
unordered의 장점은 insert erase find가 엄청 빠름  
값을 해시함수로 해시해서 보관하는 방식이기 때문  
보통 안전하게 그냥 셋 맵을 쓰고, 최적화가 매우 중요한 경우에만 해시함수를 잘 설계해서 unordered를 사용  
- 해시키를 이용하는 자료구조
- 연관 컨테이너와 달리 데이터를 넣을때 정렬하지 않음
- 연관 컨테이너에 비해 성능이 월등히 좋음
- 단 기본 라이브러리로 제공되는 자료형이 아닌, 직접 구현하는 상황에서는 해시키를 만드는데 주의해야 함
- 속도가 아주 빨라야 하는경우 사용
set과 비슷하지만 값을 정렬하지 않고, 해시구조이므로 속도가 월등함


# unordered_set

```
#include <unordered_set>

void main()
{
    std::unordered_set<int> us;
}
```

#### 1. unordered_set의 개념
- 자동으로 정렬하지 않는다는 것 외에는 set과 비슷함
- 마찬가지로 중복을 허용하지 않으므로 같은 값이 여러번 insert되면 뒤의 입력은 무시됨
- 단, 착각할 수 있는데 insert된 순서대로 저장되는 것이 아님
	- 거의 랜덤한 순서로 저장됨(해싱해서 저장하므로)


#### 2. unordered_set의 장점
- set은 이진탐색트리 구조였지만 unordered_set은 해시구조이므로 탐색 및 삽입삭제가 O(1)의 속도로 빠름
- 속도가 매우매우 중요한 상황이라면 사용하기 좋음

#### 3. unordered_set의 단점
- 해싱의 개념을 사용하므로 해시중돌이 발생할 수 있음
	- 이로 인해 기본적으로 탐색이 O(1)이겠지만 테이블이 가득찰수록 O(N)에 가까워짐
- 만약 할당된 공간에 값이 가득 채워져서 새로운 공간을 할당해야 하면 새로운 해시함수가 필요함
	- 더 많은 값들을 해시값으로 반환해야 하므로
	- 그 결과 기존의 모든 원소들도 새로운 해시함수로 전부 다시 insert해야함(rehash라고 부름)
	- rehash는 vector의 새로운 공간 할당처럼 O(N)의 시간이 걸리는 비효율이 발생할 수 있음

#### 4. unordered_set의 함수원형
```c++
template < 
	class Key,                            // 키 형식
	class Hash = std::hash<Key>,          // 해시함수 개체 형식
	class Pred = std::equal_to<Key>,      // 비교함수 형식
	class Alloc = std::allocator<Key>>    // 할당자 클래스
class unordered_set;
```

#### 5. unorderd_set의 멤버 변수들
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
- const_iterator : a bidirectional iterator to const value_type
- reverse_iterator : 	`reverse_iterator<iterator>`
- const_reverse_iterator : `reverse_iterator<const_iterator>`

- difference_type : 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- size_type :  size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)