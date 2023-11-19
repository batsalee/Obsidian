# vector  

```C++
#include <vector>

void vector()
{
	std::vector<int> v;
}
```

#### 1. vector의 개념
- 동적 배열로 구현되어 있음
- 정적배열과 동적배열의 차이
	1) 정적배열 : 생성할때 공간의 크기를 지정해줘야하고, 한번 할당하면 크기를 변경할 수 없음
	2) 동적배열 : 스스로 공간을 할당하고, 크기를 확장할 수 있고, 또 줄일수도 있음

- 각각의 원소들이 메모리에 연속적으로 선형으로 배열되어 있음
- 메모리에 연속적으로 존재하므로 iterator를 이용해 순차적으로도, 직접적으로도 접근 가능

- vector 객체를 생성시 먼저 큰 공간을 할당하고 시작함, 새로운 값이 들어오면 그 공간에 넣어줌
- 만약 공간이 가득 차면 아예 새로운 더 큰 공간을 할당하고, 해당 공간에 기존 값들을 모두 복사하고, 새로운 값을 추가함
- 위와 같은 이유로 공간이 가득 차면 새로운 공간할당, 값들을 모두 복사까지 해주는 비효율이 발생할 수 있음
- 또한 미리 큰 공간을 할당하고 시작하므로 array보다 메모리를 더 많이 사용하게 됨
- 때문에 많은 값이 들어올 예정이라면 resize함수를 이용해 미리 큰 공간을 할당하고 시작하면 좋음

#### 2. vector의 장점
- 각각의 원소를 index값으로 바로 참조 가능, O(1)의 시간복잡도
- 원소들을 임의의 순서로 접근할 수 있음, O(n)의 시간복잡도
- vector 끝에 새로운 원소를 추가하거나 제거할때 빠름(공간이 남아있다면 O(1)이지만, 아니라면 O(n))

#### 3. vector의 단점
- 끝이 아닌 시작이나 중간에 새로운 원소를 삽입/삭제하는 작업은 deque나 list보다 느림
- 먼저 큰 공간을 할당하고 시작하므로 array보다 메모리 공간을 더 많이 사용함
- 사전에 할당된 공간이 가득차면 새로운 공간할당, 기존 내용 복사 등의 비효율이 발생할 수 있음
- vector에 insert나 remove등을 하면 iterator값이 무효화됨, 계속 새로 구하면서 써야함
	※ 하지만 애초에 insert나 remove를 자주 사용해야 한다면 vector를 채택하지 않는것이 맞음
- 공간이 부족해서 새로운 공간을 할당받으면 기존에 사용하던 iterator들이 모두 무효화 됨

#### 4. vector의 함수 원형
````C++
template <class T, class Allocator = allocator<T>>  
class vector;  
````
- T : (보관하려는) 원소의 타입  
- Allocator : 많은 경우 생략함  
	어떠한 방식으로 메모리를 할당할지에 관련한 할당자(allocator) 타입을 나타낸다.   
	기본값으로 T 의 할당자 클래스 템플릿을 사용하며, Heap 에 할당하게 된다.

#### 5. vector의 멤버 변수들
- reference : Allocator::reference
- const_reference : Allocator::const_reference

- iterator : 임의 접근 반복자(random access iterator)
- const_iterator : 상수 임의 접근 반복자 (즉, 접근하는 원소의 내용을 수정 못함)
- reverse_iterator : 역 반복자 (끝에서 부터 참조해나간다) `reverse_iterator<iterator>`
- const_reverse_iterator : 상수 역 반복자 `reverse_iterator<const_iterator>`

- size_type : 벡터 size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)
- difference_type : 벡터 내의 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptfdiff_t 와 타입이 같으며 부호있는 정수)
- value_type : 원소 타입 (T)
- allocator_type : 할당자

- pointer : 포인터 (Allocator::pointer)
- const_pointer : 상수 포인터 (Allocator::const_pointer)

#### 6. vector의 멤버 함수들
1) 생성자 : 벡터를 생성한다.
2) 소멸자 : 벡터를 소멸한다.

3) 연산자
- operator= : 벡터의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (벡터의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (벡터의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- rbegin : 역순으로 첫번째 (즉, 벡터의 마지막 원소) 를 가리키는 반복자를 리턴한다.
- rend : 역순으로 끝 부분 (즉, 벡터의 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
- cbegin, cend, crbegin, crend도 지원

5) 할당 관련
- size : 벡터의 size 를 리턴한다 (현재 원소의 개수)
- capacity : 현재 벡터를 위해 할당된 전체 공간의 크기를 리턴한다.
- max_size : 시스템상 최대로 할당할 수 있는 벡터의 최대 공간의 크기를 리턴한다.
 
- resize : 벡터가 size 개의 원소를 포함하도록 변경한다.
- reserve : 벡터에 할당된 크기를 변경한다.

- empty : 벡터가 비었는지 체크한다.
- shrink_to_fit : Shrink to fit
	- 할당된 공간을 현재 vector의 size만큼 줄이는 함수

6) 원소 접근 관련
- operator[] : 원소에 접근한다.
- at : 원소에 접근한다.
- front : 첫번째 원소에 접근한다.
- back : 마지막 원소에 접근한다.
- data : 벡터의 시작 주소값을 리턴함

7) 수정자 (Modifier)
- assign : 벡터에 원소를 집어넣는다.
- push_back : 벡터 끝에 원소를 집어 넣는다.
- pop_back : 마지막 원소를 제거한다.

- insert : 벡터 중간에 원소를 추가한다.
- erase : 원소를 제거한다.
- clear : 원소를 모두 제거한다.

- swap : 다른 벡터와 원소를 바꿔치기 한다.

- emplace : 원소를 insert함, 다만 emplace는 옛날거고 push_back과 insert가 요즘꺼니 이걸 쓰라고 하던데...
- emplace_back : 제일 뒤에 값을 추가함, 이거보다 push_back 사용을 권장한다고 들었음

8) 할당자
- get_allocator : 할당자(allocator) 을 얻는다.

#### 7. vector 사용 팁들
1) 벡터 생성시
- default 생성자 : `std::vector<int> v;` 처럼 생성하면 size 0짜리 vector가 생성됨
- 갯수 지정 : `std::vetor<int> v(10);` 처럼 생성하면 size 10짜리 vector가 생성됨
- 순회 복사 : `std::vector<int> v2(v1.begin(), v1.end());` 처럼 생성하면 v1의 시작부터 끝까지 순회하며 복사
- 복사생성자 : `std::vector<int> v1 = v2;` 처럼 생성하면 v1이 v2와 동일한 원소를 복사본으로 가지는 벡터를 생성

2) 벡터에 대입
- 정적배열을 벡터로 대입하려면 아래처럼 사용
```C++
int nums[] = {1, 2, 3, 4}; 
std::vector<int> v(nums, nums + sizeof(nums) / sizeof(int));
```

- assign 함수 사용 시
	- 벡터객체에 이전에 있던 내용을 모두 삭제하고 인자로 받은 새로운 내용을 넣음
	- `v.assign(v2.begin(), v2.end());` 처럼 하면 v2의 처음부터 끝까지
	- `v.assign(n, u);` 처럼 하면 n개의 u를 넣음

3) 벡터에 삽입
- vector의 size가 capacity보다 커지면 재할당을 받게 되고, 재할당을 하면 이전에 얻은 모든 iterator가 무효화됨

- vector의 중간에 삽입을 하게 되면 해당 위치 뒤에 있는 값들을 모두 한칸씩 밀어야 하니 비효율적
- 애초에 vector에 insert하는건 효율적이지 못하니 중간위치에 삽입이 많이 필요하다면 list를 사용하는게 맞음

- 그럼엗
- `v.insert(itr, 100);` // itr위치에 100값을 insert
- `v.insert(itr, 2, 200);` // itr위치에 2개의 200을 넣음
- `v.insert(itr, v2.begin(), v2.end());` // itr위치에 v2의 시작부터 끝까지 삽입

- itr자리에 v.begin()같은걸 쓸 수도 있음

4) 이중벡터 크기 지정

- std::vector<std::vector<int>> v(행크기, std::vector<int>(열크기));

8. vector 사용시 주의사항

1) 벡터에 많은 값들이 들어갔다가 나오게 되면 capacity가 아주 커진채로 저절로 줄어들지 않음

- clear나 resize해줘도 내부 원소만 날리는거고 메모리는 줄어들지 않는다고 함

- 해결법은 swap방식과 shrink_to_fit이 있음

- swap방식은 resize나 clear한 후에 vector<int> (v).swap(v); 해주면 됨

- shrink_to_fit()은 C++11부터 제공되는데 이게 약간 문제가 있다고 함

- 해당 사이즈만큼 새로운 벡터를 만들어서 기존 원소들을 copy하는 방식이라 큰 vector라면 애매함

- 어쨋든 사용하려면 resize나 clear한 후에 shrink_to_fit()하면 됨

- 호환성 걱정되면 기존버전인 swap 쓰면 될 듯

2) vector<bool>은 다른 자료형과 다르게 사용됨

- 메모리 공간을 아끼기 위해 bool만 템플릿 특수화 되었다고 함

- 결과적으로 bool의 레퍼런스는 bool&가 아니라 vector<bool>내의 멤버변수를 통해 처리된다고 함

- 이게 문제가 된다고 하는데 operator[]가 std::vector<int>에 비해 2배이상 느리다고 함

- 그래서 std::vector<bool> 쓸바에는 std::vector<int>써서 0, 1로 처리하는게 더 낫다는 말이 있는 듯함

- bool이 int보다 메모리를 아낄 수 있지만 어차피 scope 벗어나면 자동 해제되니까 메모리 손해는 작은 반면 속도는 차이가 큰가봄

3) vector에 insert나 erase를 할 경우 기존의 itr은 무의미해짐(저절로 한칸 옮겨서 계산 안해주나봄)  
그러므로 insert나 erase 바로 뒷줄에 itr = vec.begin()같은걸로 다시 값을 지정해줘야함

erase의 경우 이런 불편함을 해결하기 위해 remove 사용

출처 : [https://cplusplus.com/reference/vector/vector/](https://cplusplus.com/reference/vector/vector/)