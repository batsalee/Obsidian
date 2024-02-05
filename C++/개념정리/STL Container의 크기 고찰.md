# STL Container의 크기 고찰

#### 주의! 정확한 자료가 아닌 그냥 호기심 해결 기록입니다.

문득 코딩을 하다가 `vector<int>`는 int 정적배열보다 얼마나 오버헤드가 생길까 싶어서  
STL Container들의 크기를 확인해보고 싶어졌다.  

sizeof 연산자로 확인결과 값을 1개 넣던 1,000,000개 넣던 같은 컨테이너면 같은 크기가 나왔다.  
즉 값이 들어있는 전체 공간이 아니라 컨테이너 객체만의 크기를 반환해주는 듯 하다.  

`vector<int>`의 오버헤드는 메모리 32byte인것으로 추정되긴 하는데  
이게 포인터의 크기와 같아서 heap에 할당되어있는 공간을 가리키는 stack의 포인터값 size를 리턴해준건 아닌가 싶기도 했다.  
근데 `vector<bool>`의 경우 템플릿특수화 되어있어서 다르게 처리된다고 알고있었는데  
`vector<bool>`은 48byte인걸 봐선 객체의 크기로 보는게 맞지 않나 싶다.  
참고로 따로 확인해본 결과 `vector<long long>`도 32byte로 나왔다.  

또한 다른 컨테이너들은 32바이트가 아닌 각자 size가 있는 듯 해서 객체의 크기가 맞는 듯 하다.  
다만 list는 다음 node 위치 기록하려면 적어도 포인터값 하나는 가져야하니까 32byte는 있어야 하는데 그보다도 적은 24byte라 뭐가뭔지 영...

처음 예상하기로는 
1) 컨테이너 객체를 만드는데 드는 시간
2) 컨테이너 객체가 차지하는 메모리공간
	1) 객체의 멤버변수가 차지하는 스택영역의 메모리공간
	2) 객체의 멤버함수가 차지하는 코드영역의 메모리 공간
3) 보관된 데이터 자체는 heap에 할당하건 stack에 할당하건 총량은 같을테니 패스
등등이 정적배열보다는 더 소비된다고 생각하긴 했는데 다른건 그렇다쳐도 list가 32byte도 안되는것 때문에 조사해보기 전보다 오히려 더 아리송해져버렸다...  

아무튼 객체 자체의 크기로 판단한다면 일반적으로 현대의 컴퓨터수준에서는 메모리제약 때문에 int 정적배열을 고려해야 하는 일은 딱히 없는게 아닐까 싶다.  

64bit운영체제에서 64bit 컴파일러로 확인한 결과이다.  

#### 결과
![[Pasted image 20240206011149.png]]

#### 코드
```C++
#include <bits/stdc++.h>
using namespace std;

void main()
{		
	cout << "int형 크기\n";
	
	int small = 1;
	cout << "small : " << sizeof(small) << "byte\n";

	int big = 2147483647;
	cout << "big : " << sizeof(big) << "byte\n";

	cout << "\nvector 크기\n";

	vector<bool> vec_bool;
	cout << "vec_bool : " << sizeof(vec_bool) << "byte\n";

	vector<bool> vec_bool_10{ true };
	cout << "vec_bool_10 : " << sizeof(vec_bool_10) << "byte\n";

	vector<bool> vec_bool_10000(10000);
	cout << "vec_bool_10000 : " << sizeof(vec_bool_10000) << "byte\n";

	vector<int> vec_0;
	cout << "vec_0 : " << sizeof(vec_0) << "byte\n";

	vector<int> vec_10{ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	cout << "vec_10 : " << sizeof(vec_10) << "byte\n";
	
	vector<int> vec_10000(10000, 1);
	cout << "vec_10000 : " << sizeof(vec_10000) << "byte\n";

	vector<int> vec_1000000(1000000, 1);
	cout << "vec_1000000 : " << sizeof(vec_1000000) << "byte\n";

	cout << "\nlist 크기\n";

	list<int> list_0;
	cout << "list_0 : " << sizeof(list_0) << "byte\n";

	list<int> list_10{ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	cout << "list_10 : " << sizeof(list_10) << "byte\n";

	list<int> list_10000(10000, 1);
	cout << "list_10000 : " << sizeof(list_10000) << "byte\n";

	list<int> list_1000000(1000000, 1);
	cout << "list_1000000 : " << sizeof(list_1000000) << "byte\n";

	cout << "\ndeque 크기\n";

	deque<int> deque_0;
	cout << "deque_0 : " << sizeof(deque_0) << "byte\n";

	deque<int> deque_10{ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	cout << "deque_10 : " << sizeof(deque_10) << "byte\n";

	deque<int> deque_10000(10000, 1);
	cout << "deque_10000 : " << sizeof(deque_10000) << "byte\n";

	deque<int> deque_1000000(1000000, 1);
	cout << "deque_1000000 : " << sizeof(deque_1000000) << "byte\n";

	cout << "\nset 크기\n";
	set<int> s;
	cout << "set : " << sizeof(s) << "byte\n";

	multiset<int> ms;
	cout << "multiset : " << sizeof(ms) << "byte\n";

	unordered_set<int> us;
	cout << "unordered_set : " << sizeof(us) << "byte\n";

	unordered_multiset<int> ums;
	cout << "unordered_multiset : " << sizeof(ums) << "byte\n";

	cout << "\nmap 크기\n";
	map<int, int> m;
	cout << "map : " << sizeof(m) << "byte\n";

	multimap<int, int> mm;
	cout << "multimap : " << sizeof(mm) << "byte\n";

	unordered_map<int, int> um;
	cout << "unordered_map : " << sizeof(um) << "byte\n";

	unordered_multimap<int, int> umm;
	cout << "unordered_multimap : " << sizeof(umm) << "byte\n";
}
```