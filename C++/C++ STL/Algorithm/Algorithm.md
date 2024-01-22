# 알고리즘(algorithm)


## 아직 정리중인 문서


정렬알고리즘, 원소제거알고리즘, 람다함수, 원소탐색알고리즘  
  
알고리즘에 정의된 여러 함수들은 보통 아래의 두 형태를 가짐  
`template <typename Iter>`
`void do_something(Iter begin, Iter end);`  
위는 시작과, 끝점 바로 뒤를 인자로 받고  
`template <typename Iter, typename Pred>`  
`void do_something(Iter begin, Iter end, Pred pred)`  
이건 시작과, 끝점 바로뒤를 받고 pred로 특정 조건을 받음.  
이런 pred 자리에 들어가는 특정한 조건을 서술자라고 부르며 pred자리에는 bool을 리턴하는 함수객체를 전달함

  
1) 정렬(sort, stable_sort, partial_sort) 알고리즘  
- sort : 일반적인 정렬함수  
- partial_sort : 배열의 일부분만 정렬함

- stable_sort : 정렬을 하되 원소들 간의 순서를 보존함  
 => int vector에 a=1, b=1로 있을때 sort는 a, b순서가 될수도 b, a순서가 될수도 있지만   
      stable_sort는 a, b 순서를 sort전과 똑같게 보존해줌
      
sort는 퀵정렬 기반으로 구현되어 있다.

sort  
`std::sort(v.begin(), v.end());`  
sort함수는 벡터와 데크만 가능, 리스트는 불가능  
내림차순으로 하고 싶으면 세번째 인자로 서술자 `greater<int>()` 를 넣으면 됨

partial_sort  
`std::partial_sort(start, middle, end)`  
=> 정렬을 start~end 전체원소 중에서 start~middle까지 원소들이 전체 원소 중 제일 작은애들이 오게 정렬함  
무슨말이나면  
5 3 1 6 4 7 2 가 있는데 이걸 `std::partial_sort(vec.begin(), vec.begin() + 3, vec.end());`  
앞의 3자리에 전체 배열중 가장 작은것 3개가 오게 하고 그 뒤는 신경쓰지 않는다는 말  
즉 1 2 3 6 5 7 4 형식으로 제일 앞 3개만 정렬되고 뒤는 그냥 랜덤하게 남김  
전체 배열을 정렬할 필요가 없이 일부만 정렬할 일이 뭐가 있냐면  
100명의 학생 중 상위 10명의 성적순만 보고싶다면 sort로 다 정렬할 필요없이 10개만하면 됨  
  
  
2) 원소제거(remove, remove_if) 알고리즘  
vector에서 erase함수를 사용하면 itr이 초기화되기때문에 불편했는데 remove를 사용 가능  
  
먼저 erase의 형태는 두가지  
`Iterator erase(Iterator pos);` // 해당 위치의 값을 지움  
`Iterator erase(Iterator first, Iterator last);` // first부터 last 사이의 모든 원소를 지움  
  
remove함수는 정확히는 제거하는게 아니라 값들을 쉬프트만 해줌  
3을 찾으면 3이 아닌 수들을 한칸씩 옮겨주면서 자리만 바꿔줌  
실제 제거는 erase해줘야함  
  
`vec.erase(remove(vec.begin(), vec.end(), 3), vec.end());` => 3을 제거해줌  
  
위처럼 정확히 3을 제거한다가 아니라 1~5를 제거한다처럼 특정 조건을 정하려면 remove_if를 사용  
```
struct is_odd {  
  bool operator()(const int& i) { return i % 2 == 1; }  
}; 
```
`vec.erase(std::remove_if(vec.begin(), vec.end(), is_odd()), vec.end());  `
이런식으로 함수객체를 remove_if의 세번째 인자로 넣어서 true면 제거, 아니면 제거하지 않음  
  
remove를 쓰면 for문안에서 erase 계속 쓰면서 itr 초기화되서 처음부터 계속 찾는 비효율 없이 사용 가능  
그러니 벡터에서 값 제거할땐 remove 활용해야만 할 듯  
  
  
  
3) 람다 함수

함수객체를 편하게 만들게 해줌, 이름이 없는 함수 객체를 만들 수 있음

함수객체가 들어갈 Pred자리에 람다함수를 써주면 됨  
`[capture list] (받는 인자) -> 리턴 타입 { 함수 본체 }`  
`[](int i) -> bool { return i % 2 == 1; }`  
  
리턴타입은 생략 가능(리턴 타입일 보고 컴파일러가 판단)  
`[capture list] ( 받는 인자) {함수 본체}`  
`[](int i) { return i % 2 == 1; }`  
  
  
* 원소 수정하기(transform)  
벡터의 값들 전체를 다 1씩 더한다면 기본적으로는 for문으로 하나씩 접근해서 +1 해주겠지만  
transform함수로 더 간단하게 할 수 있음  
```
 std::transform(vec.begin(), vec.end(), vec.begin(),  
                 [](int i) { return i + 1; });  
```
transform (시작 반복자, 끝 반복자, 결과를 저장할 컨테이너의 시작 반복자, Pred) 형태  
  
* `vector<int> vec(6, 0);` => 6개의 0으로 초기화(6칸을 미리 할당받아서 0으로 초기화 해줌)  
  
* all_of/any_of 함수  
all_of함수는 인자로 받은 범위안의 모든 원소가 다 조건을 만족해야 true  
any_of함수는 하나라도 조건을 만족하면 true  
```  
return std::all_of(users.begin(), users.end(),  
                     [](User& user) { return user.level >= 15; });  
```
처럼 쓰이며 user level이 모두 다 15이상이면 true를 리턴함  
  
* string_view  
문자열을 소유하지 않고 보기만 함  
문자열의 값을 바꾸지 않고 find하기만 하거나 substr하기만 할때 사용하면 좋을 듯  
만약 string에 const char\*값을 넣으면 암묵적으로 string객체를 만들어서 대입하니까  
string 객체만큼 메모리 낭비가 생김  
string이나 const char\* 뭐가 들어오던 값을 복사해서 소유하지 않고 읽기만 하는 string_view 사용 가능  
단 보고있는 문자열 객체나 변수가 소멸되면 볼 수 없게 되므로 소멸되기 전에만 사용 가능  
```
bool contains_very(std::string_view str) {  
  return str.find("very") != std::string_view::npos;  
}
```
※ 짤짤이 내용 추가정리하기

set은 언제나 O(logN)이 보장된다  
하지만 unordered_set은 평균 O(1)이지만 최악의 경우 O(N)이라 특정 케이스에서는 효율이 떨어진다  
  
set.clear()는 O(N)이다. 안에 들어있는 갯수만큼의 N  
  
unordered_map에 값을 받은 후 특정 조건으로 정렬을 하고 싶으면   
`vector<pair<string,int>> v(m.begin(), m.end());` 처럼 벡터로 복사해준 후 sort해야함</pair<string,int>

set에 없는 값을 erase해도 아무 문제가 일어나지 않는듯하다.

set에 insert로 A, B, C를 넣은 후

set.erase(D);를 해도 오류 없이 그냥 아무것도 안하고 넘겨주는 듯 함

어차피 값이 있으면 지운다 라고 설정해도 되지만 값이 있는지 매번 체크하는것도 logN만큼 시간이 드므로 알아두면 좋을 듯

10x10 이중벡터는

`std::vector<std::vector<int>> nums(10, std::vector<int>);` 처럼 쓰는것 보다

`std::vector<int> nums[10];` 이라고 쓰는게 훨씬 간결하다...

즉 N x M 벡터 중 N 혹은 M이 상수라면 []로 지정하는게 깔끔하다.

std::vector보다는 std::array가 더 빠르고  
std::array보다는 일반 배열이 더 빠르다  
알고리즘에서 시간 줄일땐 일반배열을 쓰는게 이득이 될 수도 있겠다  
하지만 꼭 STL을 써야하는데 속도도 올려야 한다면  
vector나 array에 data() 멤버함수가 있고  
data멤버함수를 쓰면 메모리 주소를 포인터로 리턴해주는데  
이걸 포인터처럼 써서 값에 접근하면 일반배열과 똑같아짐  
즉 메모리에 접근만 하면 사실 어떤 방식이던 상관이 없지만  
stl은 그 전까지 처리과정이 당연히 추가될 수 밖에 없으니 느려지는듯

최빈값 구할때 map 활용했는데  
first인 키값으로 기본정렬 되는건데 second로 정렬한거라고 착각하고 삽질 오래했음  
그냥 unordered_map 활용해서 값 다 넣고  
벡터로 옮겨준 다음에 second 기준으로 sort하고 써야 함

assign도 연계해서 정리해보기

vector에 값을 넣을때 push_back 외에 assign도 사용 가능  
```
template <class InputIterator>  
void assign(InputIterator first, InputIterator last); // 처음부터 끝까지  
void assign(size_type n, const T& u); // u를 n회 넣음  
```
벡터 객체에 기존에 있던 원소를 모두 "삭제하고" 인자로 새로 받은 내용을 집어 넣음  
장점은 set이나 deque같은 객체에서 vector로 값을 옮길 수 있음  
기본적으로   
vector객체 = set객체; 식의 대입은 불가능한데 이걸   
vector.assign(set.begin(), set.end()); 형태로 대입 해 줄 수 있음



#### 정렬
- sort : 일반적인 정렬 함수
- partial_sort : 배열의 일부분만 정렬함
- stable_sort : 정렬을 하되 원소들간의 순서를 보존함

기본적으로 sort는 불안정정렬이고, 안정정렬이 필요하면 stable_sort를 사용하는 것  
불안정 정렬은 { 2, 2, 1, 3, 4}가 들어있다면 정렬 후 {1, 2, 2, 3, 4}가 되긴 하는데 두개의 2의 순서가 바뀔수도 안바뀔 수도 있는 것  
반면 안정 정렬은 두개의 2의 순서가 유지됨  

#### 함수객체와 람다함수
1) 함수객체 형태
```C++
struct is_odd {
	bool operator()(const int& i) { return i % 2 == 1; }
}

또는

struct comp {
	return a > b;
}
```

2) 람다함수
```C++
sort(numlist.begin(), numlist.end(),  
	[n](const auto& a, const auto& b) -> bool  
	{ 
		if(abs(a-n) == abs(b-n)) return a > b;  
		else return abs(a-n) < abs(b-n); 
	}  
);  
/*
위처럼 사용할때 return a > b처럼 앞이 크게 리턴하면 큰게 앞으로 가는 내림차순 정렬
return abs(a-n) < abs(b-n);처럼 뒤가 크게 하면 절대값이 더 큰게 뒤로가는 오름차순 정렬
*/
```

3) 람다함수 비교 조건문
```C++
//람다식 pred 부분에서 길이가 짧은 문자열이 앞에 오고 싶다면  
if (a.length() == b.length()) return a < b;  
return a.length() < b.length();  
//형태로 구성해야 한다.  
//그냥 a<b 하면 안되고 a.length() < b.length()
```

#### erase와 remove
``vec.erase(remove(vec.begin(), vec.end(), 3), vec.end()); => vec에 있는 모든 3 제거``  

#### transform
컨테이너 내의 모든 값에대한 변경은 for문 돌리는것보다 transform을 먼저 떠올리자  

#### any_of / all_of
둘의 존재를 잊지 않기  

#### reverse
``reverse(vec.begin(), vec.end());``  
{1, 2, 3, 4, 5}가 들어있던 vec을 reverse하면 {5, 4, 3, 2, 1}이 된다.  

``reverse(str.begin(), str.end());``  
또한 문자열 뒤집기도 가능  

#### rotate
벡터의 값들을 한칸씩 옮기며 회전시킴  
``rotate(v.begin(), v.begin() + 1, v.end()); // 한칸씩 왼쪽 이동``  
``rotate(v.begin(), v.end() - 1, v.end()); // 한칸씩 오른쪽 이동``  
매개변수는 (시작반복자, 첫 위치로 올 반복자, 종료 반복자)순이며   
두번째 매개변수 위치에 있던 값이 첫 위치로 간다는 뜻으로  
v.begin() + 1이 첫 위치로 간다는것은 왼쪽으로 움직인다는 뜻  

#### swap
``swap(a, b);`` 혹은 ``swap(객체, 객체);  

#### lower_bound와 upper_bound
```C++
lower_bound(v.begin(), v.end(), 0) - v.begin() 
// lower_bound는 x이상인 첫번째 원소의 위치 반환  
upper_bound(v.begin(), v.end(), 0) - v.begin()
// upper_bound는 x초과인 첫번째 원소의 위치 반환
```
또한 upper_bound 결과 - lower_bound 결과로 값의 갯수를 알 수 있다.  
만약 count를 사용한다면 for문 돌리는 효과라 O(N)이지만 위의 이분탐색 함수를 사용하면 O(logN)  

#### 보이어-무어 알고리즘
문자열에서 특정 문자열을 찾을때 find를 쓰는 방법도 있지만 보이어-무어 알고리즘을 사용하면 훨씬 빠르다.  
```C++
std::string s =	"I believe I can fly I believe I can fly I believe I can fly (woo)";
std::string needle = "believe";

auto it = std::search(s.begin(), s.end(),
		std::boyer_moore_searcher(needle.begin(), needle.end()));

if (it != s.end()) {
	std::cout << needle << " at : " << std::distance(s.begin(), it) << std::endl;
} 
else {
	std::cout << needle << " not found " << std::endl;
}
```

#### 문자열 맨 앞 또는 맨 뒤의 연속된 값 지우기
bitset같은걸로 2진수 만들때 유용할 듯  
```C++ 
// 문자열에서 맨 앞의 연속된 0을 지우고 싶다면   
s.remove_prefix(std::min(s.find_first_not_of("0"), s.size())); 
// 맨 뒤는  
s.remove_suffix(std::min(s.size() - s.find_last_not_of("0") - 1, s.size()));
```

#### inner_product
`` int result = inner_product(v1.begin(), v1.end(), v2.begin(), 0);``  
v2의 size는 v1의 size보다 크거나 같아야 함  
함수의 기능은 ``v1[0] * v2[0] + v1[1] * v2[1] * ... + v1[10] * v2[10]``  
처럼 같은 위치의 값들을 곱한 후 그 값들을 다 더해주는 것  

#### accumulate
이건 사실 ``#include <algorithm>``이 아니라 ``#include <numeric>``인데 일단 여기 같이 적어둠  
```C++
#include <numeric>  
int sum = std::accumulate(v.begin(), v.end(), 0);  
// 첫번째, 두번째 인자는 어디부터 어디까지  
// 3번째 인자는 sum값의 초기값  
// 만약 sum이 double이면 0.0으로 적어주면 됨
```
