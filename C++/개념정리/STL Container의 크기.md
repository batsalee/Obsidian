# STL Container의 크기

문득 코딩을 하다가 `vector<int>`는 int보다 얼마나 오버헤드가 생길까 싶어서  
STL Container들의 크기를 확인해보고 싶어졌다.  

확인결과 값을 적게 넣던 많이 넣건 같은 컨테이너면 같은 크기가 나왔다.  
즉 값이 들어있는 전체 공간이 아니라 컨테이너 객체만의 크기를 반환해주는 듯 하다.  

그말인즉슨 vector의 오버헤드는 메모리 32byte인것으로 추정되며  
`vector<bool>`여도, `vector<int>`여도 같은걸 봐선 템플릿특수화와도 상관없는 듯 하다.  

결론적으로 int 정적배열을 크게쓰면 크게쓸수록 32byte는 

64bit운영체제에서 64bit 컴파일러로 확인한 결과이다.  
#### 결과
![[Pasted image 20240206005300.png]]

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

	vector<int> vec_bool;
	cout << "vec_bool : " << sizeof(vec_bool) << "byte\n";

	vector<int> vec_bool_10{ true };
	cout << "vec_bool_10 : " << sizeof(vec_bool_10) << "byte\n";

	vector<int> vec_bool_10000(10000);
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