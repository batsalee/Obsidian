# set

```C++
#include <set>

void main()
{
    std::set<int> s;
}
```

#### 1. set의 개념
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
```
위처럼 값을 넣어도 값을 넣을때마다 자동정렬되므로 


set은 s.insert(10);처럼 insert함수로 값을 넣을 수 있는데 넣는 위치를 지정하지 않고 정렬된 상태를 유지하면서 추가함  
그래서 만약 10 50 20 40 30 순으로 넣어도 begin~end로 출력하면 10 20 30 40 50 순으로 나옴  
set 안에는 중복된 원소가 없음  
10을 여러번 넣어도 10이 이미 있으면 insert를 무시함  
10을 여러개 넣는걸 허용한게 multiset  
  
std::cout << "20 이 s 의 원소인가요? :: ";  
auto itr = s.find(20);  
if (itr != s.end())  
{  
std::cout << "Yes" << std::endl;  
}   
else   
{  
std::cout << "No" << std::endl;  
}  
이런식으로 find해서 itr값이 끝까지 갔는지 중간에 멈췄는지로 판별  
  
set의 내부 구조는 일자로 연속적으로 있는게 아니라 이진탐색트리구조로 되어있음  
그래서 시퀀스 컨테이너들보다 특정 값을 찾아가는 속도가 빠름  
벡터의 경우 begin부터 end까지 하나씩 비교하며 값을 찾아가야했지만  
set의 경우 트리를 따라가면 되므로 훨씬 빠르게 찾을 수 있음

값이 있냐 없냐를 판별하기에 더 좋은 구조  

#### 2. set의 장점
#### 3. set의 단점
#### 4. set의 함수원형
#### 5. set의 개념

#### 6. set의 멤버 변수들
#### 7. set의 멤버 함수들


출처 : https://cplusplus.com/reference/set/set/