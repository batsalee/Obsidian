# 알고리즘(algorithm)


  

3. 알고리즘  
정렬알고리즘, 원소제거알고리즘, 람다함수, 원소탐색알고리즘  
  
알고리즘에 정의된 여러 함수들은 보통 아래의 두 형태를 가짐  
template <typename Iter>  
void do_something(Iter begin, Iter end);  
위는 시작과, 끝점 바로 뒤를 인자로 받고  
template <typename Iter, typename Pred>  
void do_something(Iter begin, Iter end, Pred pred)  
이건 시작과, 끝점 바로뒤를 받고 pred로 특정 조건을 받음.  
이런 pred 자리에 들어가는 특정한 조건을 서술자라고 부르며 pred자리에는 bool을 리턴하는 함수객체를 전달함

  
1) 정렬(sort, stable_sort, partial_sort) 알고리즘  
- sort : 일반적인 정렬함수  
- partial_sort : 배열의 일부분만 정렬함

- stable_sort : 정렬을 하되 원소들 간의 순서를 보존함  
 => int vector에 a=1, b=1로 있을때 sort는 a, b순서가 될수도 b, a순서가 될수도 있지만   
      stable_sort는 a, b 순서를 sort전과 똑같게 보존해줌

sort  
std::sort(v.begin(), v.end());  
sort함수는 벡터와 데크만 가능, 리스트는 불가능  
내림차순으로 하고 싶으면 세번째 인자로 서술자 greater<int>() 를 넣으면 됨

partial_sort  
std::partial_sort(start, middle, end)  
=> 정렬을 start~end 전체원소 중에서 start~middle까지 원소들이 전체 원소 중 제일 작은애들이 오게 정렬함  
무슨말이나면  
5 3 1 6 4 7 2 가 있는데 이걸 std::partial_sort(vec.begin(), vec.begin() + 3, vec.end());  
앞의 3자리에 전체 배열중 가장 작은것 3개가 오게 하고 그 뒤는 신경쓰지 않는다는 말  
즉 1 2 3 6 5 7 4 형식으로 제일 앞 3개만 정렬되고 뒤는 그냥 랜덤하게 남김  
전체 배열을 정렬할 필요가 없이 일부만 정렬할 일이 뭐가 있냐면  
100명의 학생 중 상위 10명의 성적순만 보고싶다면 sort로 다 정렬할 필요없이 10개만하면 됨  
  
  
2) 원소제거(remove, remove_if) 알고리즘  
vector에서 erase함수를 사용하면 itr이 초기화되기때문에 불편했는데 remove를 사용 가능  
  
먼저 erase의 형태는 두가지  
Iterator erase(Iterator pos); // 해당 위치의 값을 지움  
Iterator erase(Iterator first, Iterator last); // first부터 last 사이의 모든 원소를 지움  
  
remove함수는 정확히는 제거하는게 아니라 값들을 쉬프트만 해줌  
3을 찾으면 3이 아닌 수들을 한칸씩 옮겨주면서 자리만 바꿔줌  
실제 제거는 erase해줘야함  
  
vec.erase(remove(vec.begin(), vec.end(), 3), vec.end()); => 3을 제거해줌  
  
위처럼 정확히 3을 제거한다가 아니라 1~5를 제거한다처럼 특정 조건을 정하려면 remove_if를 사용  
struct is_odd {  
  bool operator()(const int& i) { return i % 2 == 1; }  
};  
vec.erase(std::remove_if(vec.begin(), vec.end(), is_odd()), vec.end());  
이런식으로 함수객체를 remove_if의 세번째 인자로 넣어서 true면 제거, 아니면 제거하지 않음  
  
remove를 쓰면 for문안에서 erase 계속 쓰면서 itr 초기화되서 처음부터 계속 찾는 비효율 없이 사용 가능  
그러니 벡터에서 값 제거할땐 remove 활용해야만 할 듯  
  
  
  
3) 람다 함수

함수객체를 편하게 만들게 해줌, 이름이 없는 함수 객체를 만들 수 있음

함수객체가 들어갈 Pred자리에 람다함수를 써주면 됨  
[capture list] (받는 인자) -> 리턴 타입 { 함수 본체 }  
[](int i) -> bool { return i % 2 == 1; }  
  
리턴타입은 생략 가능(리턴 타입일 보고 컴파일러가 판단)  
[capture list] ( 받는 인자) {함수 본체}  
[](int i) { return i % 2 == 1; }  
  
  
* 원소 수정하기(transform)  
벡터의 값들 전체를 다 1씩 더한다면 기본적으로는 for문으로 하나씩 접근해서 +1 해주겠지만  
transform함수로 더 간단하게 할 수 있음  
 std::transform(vec.begin(), vec.end(), vec.begin(),  
                 [](int i) { return i + 1; });  
transform (시작 반복자, 끝 반복자, 결과를 저장할 컨테이너의 시작 반복자, Pred) 형태  
  
* vector<int> vec(6, 0); => 6개의 0으로 초기화(6칸을 미리 할당받아서 0으로 초기화 해줌)  
  
* all_of/any_of 함수  
all_of함수는 인자로 받은 범위안의 모든 원소가 다 조건을 만족해야 true  
any_of함수는 하나라도 조건을 만족하면 true  
  
return std::all_of(users.begin(), users.end(),  
                     [](User& user) { return user.level >= 15; });  
처럼 쓰이며 user level이 모두 다 15이상이면 true를 리턴함  
  
* string_view  
문자열을 소유하지 않고 보기만 함  
문자열의 값을 바꾸지 않고 find하기만 하거나 substr하기만 할때 사용하면 좋을 듯  
만약 string에 const char*값을 넣으면 암묵적으로 string객체를 만들어서 대입하니까  
string 객체만큼 메모리 낭비가 생김  
string이나 const char* 뭐가 들어오던 값을 복사해서 소유하지 않고 읽기만 하는 string_view 사용 가능  
단 보고있는 문자열 객체나 변수가 소멸되면 볼 수 없게 되므로 소멸되기 전에만 사용 가능  
  
bool contains_very(std::string_view str) {  
  return str.find("very") != std::string_view::npos;  
}

※ 짤짤이 내용 추가정리하기

set은 언제나 O(logN)이 보장된다  
하지만 unordered_set은 평균 O(1)이지만 최악의 경우 O(N)이라 특정 케이스에서는 효율이 떨어진다  
  
set.clear()는 O(N)이다. 안에 들어있는 갯수만큼의 N  
  
unordered_map에 값을 받은 후 특정 조건으로 정렬을 하고 싶으면   
vector<pair<string,int>> v(m.begin(), m.end()); 처럼 벡터로 복사해준 후 sort해야함</pair<string,int>

set에 없는 값을 erase해도 아무 문제가 일어나지 않는듯하다.

set에 insert로 A, B, C를 넣은 후

set.erase(D);를 해도 오류 없이 그냥 아무것도 안하고 넘겨주는 듯 함

어차피 값이 있으면 지운다 라고 설정해도 되지만 값이 있는지 매번 체크하는것도 logN만큼 시간이 드므로 알아두면 좋을 듯

10x10 이중벡터는

std::vector<std::vector<int>> nums(10, std::vector<int>); 처럼 쓰는것 보다

std::vector<int> nums[10]; 이라고 쓰는게 훨씬 간결하다...

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
template <class InputIterator>  
void assign(InputIterator first, InputIterator last); // 처음부터 끝까지  
void assign(size_type n, const T& u); // u를 n회 넣음  
벡터 객체에 기존에 있던 원소를 모두 "삭제하고" 인자로 새로 받은 내용을 집어 넣음  
장점은 set이나 deque같은 객체에서 vector로 값을 옮길 수 있음  
기본적으로   
vector객체 = set객체; 식의 대입은 불가능한데 이걸   
vector.assign(set.begin(), set.end()); 형태로 대입 해 줄 수 있음