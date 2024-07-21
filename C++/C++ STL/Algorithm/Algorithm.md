# Algorithm

## 1. 함수객체와 람다함수

알고리즘에 정의된 여러 함수들은 보통 아래의 두 형태를 가짐
```C++
template <typename Iter>
void do_something(Iter begin, Iter end); // 시작점과 끝점의 바로 뒤를 인자로 받음

template <typename Iter, typename Pred>
void do_something(Iter begin, Iter end, Pred pred) // 시작점과 끝점의 바로 뒤를 받고 pred로 특정 조건을 받음
```
즉 해당 알고리즘 함수를 사용할 범위뿐 아니라 조건도 추가할 수 있음  
예를들어 정렬의 경우 기본적으로 오름차순이지만 내림차순으로 정렬하게 바꿀 수 있음  
  
위의 pred 자리에 들어가는 특정한 조건을 서술자라고 부르며 pred자리에는 bool을 리턴하는 함수객체를 전달함  
혹은 람다함수를 작성해서 함수객체를 대신할 수 있음  

#### 1) 함수 객체

```C++
struct is_odd {
	bool operator()(const int& i) { return i % 2 == 1; }
}

// 또는

struct comp {
	return a > b;
}
```
이렇게 작성하고 pred자리에 is_odd() 또는 comp 라고 작성하면 됨  

#### 2) 람다 함수
함수객체를 편하게 만들게 해줌, 임시 함수객체를 만든다는 느낌으로 이름이 없는 함수 객체를 만들 수 있음  
```C++
/*
[capture list] (받는 인자) -> 리턴 타입 { 함수 본체 }
*/
[](int i) -> bool { return i % 2 == 1; }

/*
리턴타입은 컴파일러가 판단 가능하므로 생략할 수 있음
[capture list] (받는 인자) {함수 본체}
*/
[](int i) { return i % 2 == 1; }
```

```C++
/*
a와 b의 n과의 차이의 절대값이 작은게 앞으로 가도록,
만약 차이의 절대값이 같다면 더 큰게 앞으로 가도록
*/

sort(numlist.begin(), numlist.end(),  
	[](const auto& a, const auto& b) {
		if(abs(a - n) == abs(b - n)) return a > b;  
		else return abs(a - n) < abs(b - n); 
	});  
    
/*
위처럼 사용할때 return a > b처럼 앞이 크게 리턴하면 큰게 앞으로 가는 내림차순 정렬
return abs(a-n) < abs(b-n);처럼 뒤가 크게 하면 절대값이 더 큰게 뒤로가는 오름차순 정렬
*/
```

람다함수 비교 조건문 예시  
```C++
/*
길이가 짧은 문자열이 앞에 오고 싶되 길이가 같다면 사전순 빠른 문자가 앞으로 가도록
*/
if (a.length() == b.length()) return a < b;  
return a.length() < b.length();  

// 그냥 a < b 하면 말 그대로 a와 b의 사전순 비교
// 길이가 더 짧은게 앞으로 가고 싶다면 a.length() < b.length()로 비교
```

## 2. 정렬 알고리즘

#### 1) sort 
일반적인 정렬함수  
퀵소트 기반으로 구현되어 있지만 pivot을 정하는 조건을 최적화해서 항상 O(nlogn)으로 처리된다고 함  
```C++
sort(vec.begin(), vec.end()); // 오름차순 정렬
sort(vec.begin(), vec.end(), greater<>()); // 내림차순 정렬
sort(vec.begin(), vec.end(), comp); // comp에 맞는 조건에 따라 정렬
```

#### 2) partial_sort
배열의 일부분만 정렬  
예를들어 100명의 학생 중 상위 10명의 성적순만 보고싶다면 sort로 다 정렬할 필요없이 10개만하면 됨  
```C++
partial_sort(start, middle, end);    // start ~ end 전체원소 중에서 start ~ middle까지만 정렬함

partial_sort(vec.begin(), vec.begin() + 3, vec.end());
/*
{ 5 3 1 6 4 7 2 }가 있을때 middle이 vec.begin() + 3이면
앞의 3자리만 전체 배열중 가장 작은것 3개가 오게 하고 그 뒤는 신경쓰지 않음
즉 { 1 2 3 6 5 7 4 } 형식으로 제일 앞 3개만 정렬되고 뒤는 그냥 랜덤하게 남김
*/
```

#### 3) stable_sort 
안정 정렬,  정렬을 하되 원소들간의 순서는 보존함  
그냥 sort는 불안정 정렬임  
안정 정렬이란 vector에 a = 1, b = 1로 있을때 sort는 a, b순서가 될수도 b, a순서가 될수도 있지만 stable_sort는 a, b 순서를 sort전과 똑같게 보존해줌  

## 3. transform

원소 수정하기  
벡터의 값들 전체를 다 1씩 더한다면 기본적으로는 for문으로 하나씩 접근해서 +1 해주겠지만 transform함수로 더 간단하게 할 수 있음  
```C++
/*
transform (시작 반복자, 끝 반복자, 결과를 저장할 컨테이너의 시작 반복자, Pred);
*/

std::transform(vec.begin(), vec.end(), vec.begin(), [](int i){ return i + 1; });
```

문장의 문자들을 모두 소문자로 바꾸고 싶다면
```C++
transform(my_string.begin(), my_string.end(), my_string.begin(), ::tolower);
```

## 4. all_of / any_of

all_of 함수는 인자로 받은 범위안의 모든 원소가 다 조건을 만족해야 true  
any_of 함수는 하나라도 조건을 만족하면 true  
```C++
return std::all_of(users.begin(), users.end(), [](User& user) { return user.level >= 15; });

/*
user level이 모두 다 15이상이면 true를 리턴함
*/
```

## 5. reverse

array나 vector나 string을 뒤집는 함수  
```C++
reverse(vec.begin(), vec.end());

/*
{1, 2, 3, 4, 5}가 들어있던 vec을 reverse하면 {5, 4, 3, 2, 1}이 된다.
또한 문자열 뒤집기도 가능
*/
```

## 6. rotate

벡터의 값들을 옮겨서 회전시킴  
```C++
rotate(v.begin(), v.begin() + 1, v.end()); // 한칸씩 왼쪽 이동
rotate(v.begin(), v.end() - 1, v.end()); // 한칸씩 오른쪽 이동

/*
매개변수는 (시작반복자, 누가 시작위치로 올지, 종료 반복자) 순으로
두번째 매개변수 위치에 있던 값이 첫 위치로 간다는 뜻
*/
```

## 7. swap

두 변수나 객체의 값 교환  
```C++
swap(a, b);
또는
swap(객체, 객체);
```

## 8. 이분탐색

정렬된 벡터에서만 사용 가능  
- lower_bound : x 이상인 값이 처음 나오는 iterator 반환
- upper_bound : x 초과인 값이 처음 나오는 iterator 반환  
- equal_range : { lower_bound, upper_bound }로 구성된 pair 반환
- binary_search : x가 존재하는지 아닌지 true/false 반환
```C++
auto itr1 = lower_bound(v.begin(), v.end(), x); // x이상인 첫번째 원소의 위치 반환

auto itr2 = upper_bound(v.begin(), v.end(), x); // x초과인 첫번째 원소의 위치 반환

auto itr3 = equal_range(v.begin(), v.end(), x); // itr1과 itr2로 이루어진 pair 반환

bool isthere = binary_search(v.begin(), v.end(), x); // x가 있는지 없는지 true/false 반환
```
또한 upper_bound 결과 - lower_bound 결과로 값의 갯수를 알 수 있다.  
count의 경우 O(N)이지만 이분탐색 함수를 사용하면 O(logN)이므로 정렬된 벡터라면 이분탐색으로 count하는게 유리하다.

## 9. remove / remove_if

vector의 경우 erase할때마다 iterator가 무효화돼서 불편한데 이를 remove를 이용해서 해결  

먼저 erase의 형태는 두가지가 있음  
```C++
erase(Iterator pos); // 해당 위치의 값을 지움
erase(Iterator first, Iterator last); // first부터 last 사이의 모든 원소를 지움
```

remove는 위에서 두번째 형식과 연계해서 쓰는데 아래처럼 사용 가능  
```C++
vec.erase(remove(vec.begin(), vec.end(), 3), vec.end()); 
// vec에 있는 모든 3 제거
```

remove함수의 역할은 정확히는 제거하는게 아니라 값들을 뒤쪽으로 쉬프트만 해줌  
3을 찾으면 3이 아닌 수들을 한칸씩 옮겨주면서 자리만 바꿔줌  
실제 제거는 erase해줘야함  

특정 조건에 맞을때 제거하고 싶다면 remove_if를 사용  
remove_if의 세번째 인자로 조건을 넣어서 true면 제거, 아니면 제거하지 않음  
```C++
struct is_odd {  
  bool operator()(const int& i) { return i % 2 == 1; }  
}; 

vec.erase(remove_if(vec.begin(), vec.end(), is_odd()), vec.end());
```

## 10. inner_product

두 벡터의 값들을 같은 인덱스끼리 곱한 후 총합해서 return  
`v1[0] * v2[0] + v1[1] * v2[1] * ... + v1[10] * v2[10]`

v2의 size는 v1의 size보다 크거나 같아야 함  
```C++
int result = inner_product(v1.begin(), v1.end(), v2.begin(), 0);
```

## 11. 문자열 맨 앞 또는 맨 뒤의 연속된 값 지우기

이건 algorithm 헤더 기능은 아니고 string 클래스 기능인듯한데 같이 쓰기 좋아서 적어둠  
- find_first_not_of("x") : 처음으로 "x"가 아닌 index 반환
- find_last_not_of("x") : 뒤에서부터 처음으로 "x"가 아닌 index 반환

- remove_prefix(size)는 앞 부분부터 size 만큼 삭제
- remove_suffix(size)는 뒷 부분부터 size 만큼 삭제
```C++
// 문자열에서 맨 앞의 연속된 0을 지우고 싶다면   
s.remove_prefix(std::min(s.find_first_not_of("0"), s.size())); 

// 맨 뒤는  
s.remove_suffix(std::min(s.size() - s.find_last_not_of("0") - 1, s.size()));
```

bitset같은걸로 2진수 만들때 유용할 듯
```C++
string binary = bitset<8 * sizeof(N)>(N).to_string();
cout << binary.substr(binary.find_first_not_of('0'));
```

## 12. accumulate

이건 algorithm 헤더 기능은 아니고 numeric 헤더 기능인듯한데 같이 쓰는 경우가 많아서 적어둠
#### 1) 벡터 내의 모든 원소의 합
```C++
#include <numeric>  

/*
첫번째, 두번째 인자는 어디부터 어디까지, 세번째 인자는 sum의 초기값
만약 sum이 double이면 0.0으로 적어주면 됨
*/
int sum = accumulate(v.begin(), v.end(), 0);
```

#### 2) 벡터 내의 모든 원소의 곱
```C++
int mul = accumulate(vec.begin(), vec.end(), 1, multiplies<int>());
```

#### 3) string 벡터 이어붙이기
```C++
vector<string> v{"abc", "de", "f", "ghijk"};
string answer = accumulate(v.begin(), v.end(), string{});

// answer = "abcdefghijk"
```

## 13. 보이어-무어 알고리즘

문자열에서 특정 문자열을 찾을때 find를 쓰는 방법도 있지만 보이어-무어 알고리즘을 사용하면 훨씬 빠르다.  
아래의 s에서 needle을 탐색해서 첫번째로 나온 iterator를 반환한다.  
```C++
string s = "I believe I can fly I believe I can fly I believe I can fly";
string needle = "believe";

auto itr = search(s.begin(), s.end(), boyer_moore_searcher(needle.begin(), needle.end()));

if (itr != s.end()) cout << needle << " at : " << distance(s.begin(), it);
else cout << needle << " not found ";
```


※ 이후 내용들 아래 사이트 확인하고 또 정리해보기
https://modoocode.com/256