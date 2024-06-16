# array

```C++
#include <array>

void main()
{
	std::array<int, 10> a;
}
```

#### 1. std::array의 개념
- C언어의 기본배열인 `array[10];` 같은것과 비슷하게 작동
- C배열처럼 {}로 초기화 및 대입 가능
- C배열에 비해 좋은점은 std::array를 이용하면 algorithm헤더의 함수들을 적용할 수 있고 `array.begin() == array.end()`같은 사용도 가능해짐
- 선언 후 초기화하지 않으면 쓰레기값이 들어있음
- 10칸짜리 배열에 숫자 30 한개만 넣으면 나머지 9칸은 0으로 변경됨

#### 2. std::array의 장점
- stl로 지원하는 기능들을 사용할 수 있게 됨
- C배열과 비교해서 오버헤드가 없음

#### 3. std::array의 단점
- 기존 배열과 동일할 듯, 예를들면 공간할당되면 크기를 변경할 수 없다던가

#### 4. std::array의 함수 원형
```C++
template <class T, std::size_t N>
struct array;
```
T자료형의 데이터를 N개 갖는 배열

#### 5. std::array의 멤버 변수들
- reference : Allocator::reference
- const_reference : Allocator::const_reference

- iterator : 임의 접근 반복자(random access iterator)
- const_iterator : 상수 임의 접근 반복자 (즉, 접근하는 원소의 내용을 수정 못함)
- reverse_iterator : 역 반복자 (끝에서 부터 참조해나간다) `reverse_iterator<iterator>`
- const_reverse_iterator : 상수 역 반복자 `reverse_iterator<const_iterator>`

- size_type : array size 를 나타내는 타입 (많은 경우 size_t 와 타입이 같으며 부호없는 정수이다)
- difference_type : array 내의 두 원소 사이의 거리를 나타내는 타입 (많은 경우 ptrdiff_t 와 타입이 같으며 부호있는 정수)
- value_type : 원소 타입 (T)

- pointer : 포인터 (Allocator::pointer)
- const_pointer : 상수 포인터 (Allocator::const_pointer)

#### 6. std::array의 멤버 함수들

1) 생성자 : array를 생성한다.
2) 소멸자 : array를 소멸한다.

3) 연산자
- operator= : array의 내용을 복사한다.

4) 반복자 (Iterators)
- begin : 시작 부분 (array의 첫번째 원소) 을 가리키는 반복자를 리턴한다.
- end : 끝 부분 (array의 마지막 원소 바로 다음) 을 가리키는 반복자를 리턴한다.
- rbegin : 역순으로 첫번째 (즉, array의 마지막 원소) 를 가리키는 반복자를 리턴한다.
- rend : 역순으로 끝 부분 (즉, array의 첫번째 원소 바로 이전) 을 가리키는 반복자를 리턴한다.
- cbegin, cend, crbegin, crend도 지원

5) 할당 관련
- size : array의 size 를 리턴한다 (현재 원소의 개수)
- max_size : array 최대 크기를 리턴한다.
- empty : array가 비었는지 체크한다.

6) 원소 접근 관련
- operator[] : 원소에 접근한다.
- at : 원소에 접근한다.
- front : 첫번째 원소에 접근한다.
- back : 마지막 원소에 접근한다.
- data : array의 시작 주소값을 리턴함

7) 수정자 (Modifier)
- fill : array를 특정 값으로 다 채움
- swap : 다른 array와 원소를 바꿔치기 한다.




출처 : [https://cplusplus.com/reference/array/array/](https://cplusplus.com/reference/array/array/)