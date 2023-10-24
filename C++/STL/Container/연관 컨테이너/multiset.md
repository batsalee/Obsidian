
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