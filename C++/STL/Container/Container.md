# 컨테이너(Container)

임의 타입의 객체를 보관

C++ STL 컨테이너에는 크게 두종류가 있음  
1. [[시퀀스 컨테이너]] : 배열처럼 값을 연속적으로 보관  
2. [[연관 컨테이너]] : 키를 바탕으로 대응하는 값을 찾아주는 방식  
  



- 연관 컨테이너  
- 시퀀스 컨테이너인 vector, list, deque와 다르게 연관컨테이너는 key-value 구조를 가짐  
특정 키를 넣으면 이에 대응하는 값을 돌려줌  
- set, map, multiset, multimap, unordered_set, unordered_map이 있음  
  
- set은 키값이 데이터에 존재하는가  
- map은 존재한다면 그에 대응하는 값이 무엇인가  
맵을 셋처럼 쓸 수는 있지만 맵이 크기가 더 크기때문에 구분해서 사용하기  
  
1) set  
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
  
set의 내부 구조는 일자로 연속적으로 있는게 아니라 트리구조로 되어있음  
그래서 시퀀스 컨테이너들보다 특정 값을 찾아가는 속도가 빠름  
벡터의 경우 begin부터 end까지 하나씩 비교하며 값을 찾아가야했지만  
set의 경우 트리를 따라가면 되므로 훨씬 빠르게 찾을 수 있음

값이 있냐 없냐를 판별하기에 더 좋은 구조  
  
2) map  
맵은 셋과 거의 똑같은데 셋은 키만 보관했지만 맵은 키에 대응하는 값까지 보관함  
맵 또한 중복된 값의 입력은 무시함  
  
맵의 insert는 pair 객체를 인자로 받음  
m.insert(std::pair<std::string, double>("박세웅", 2.23));  
혹은 make_pair함수로 자료형 길게 안적고 가능  
m.insert(std::make_pair("박세웅", 2.23));  
혹은 insert를 안쓰고 그냥 인덱스에 대입도 가능  
m["박세웅"] = 2.23; // 이미 키가 있으면 값이 바뀌고, 없으면 새로 추가해줌  
  
값에 접근은  
itr->first와 itr->second로 키와 value에 접근  
  
[]연산자로 값에 접근할때 주의점  
cout << m["박세웅"]; 하면 알아서 2.23을 출력해줌  
근데 문제는 없는 키에 접근해서 출력을 해도 오류를 일으키지 않고 0을 출력해줌  
cout << m["류현진"]; 하면 그냥 디폴트값을 넣어서 생성해버리고 출력함  
그러니 []로 값을 접근할거면 find로 먼저 값이 있는지 확인하고 있으면 출력하는 식으로 해야함  
  
만약   
m.insert(std::make_pair("박세웅", 2.23));  
m.insert(std::make_pair("박세웅", 3.59));  
처럼 입력하면 이미 있는 값의 insert는 무시하므로 2.23에서 값이 바뀌지 않음  
값을 바꾸려면  
m["박세웅"] = 3.59;로 써야함  
  
3) 멀티셋과 멀티맵  
멀티셋 멀티맵은 중복된 insert도 받음  
insert a b c d a b c 하면 내부에 a a b b c c d 로 저장됨  
멀티 맵의 경우   
m.insert(std::make_pair("박세웅", 2.23));  
m.insert(std::make_pair("박세웅", 3.59));  
하면 두개의 값이 다 들어있으니 m["박세웅"] = ? 식으로 접근이 불가능함  
둘 중 어디에 접근해야 할 지 알수 없으니  
그래서 []연산자를 제공하지 않음  
그럼 어떻게 접근하냐면   
auto range = m.equal_range("박세웅");  
for (auto itr = range.first; itr != range.second; ++itr) {  
std::cout << itr->first << " : " << itr->second << " " << std::endl;  
}  
처럼 equal_range에 키를 인자로 주면 대응하는 키들을 pair객체로 만들어서 리턴해줌  
그럼   
박세웅 : 2.23  
박세웅 : 3.59  
처럼 출력 가능  
  
4) unordered_set과 unordered_map  
- 정렬되지 않은 셋과 맵  
말그대로 정렬되어있지 않음. 값들을 넣고 출력해보면 순서가 막 뒤섞여서 랜덤하게 나옴  
unordered의 장점은 insert erase find가 엄청 빠름  
값을 해시함수로 해시해서 보관하는 방식이기 때문  
보통 안전하게 그냥 셋 맵을 쓰고, 최적화가 매우 중요한 경우에만 해시함수를 잘 설계해서 unordered를 사용  
  
5) 연관컨테이너 정리  
데이터의 존재 유무만 알면 된다 -> set  
중복 데이터 허용시 -> multiset  
  
키와 데이터를 모두 저장한다 -> map  
중복키를 허용할 경우 -> multimap  
  
속도가 매우 중요해서 최적화를 해야 할 경우 -> unordered set/map

  