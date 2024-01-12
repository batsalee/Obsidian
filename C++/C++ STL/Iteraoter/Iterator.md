# Iterator(반복자)

#### 1. iterator의 개념
컨테이너에 보관된 원소에 접근할 수 있는 포인터같은 객체  
마치 배열의 index와 비슷하게 작동함  
컨테이너에 iterator 멤버 타입으로 정의되어 있음  

#### 2. iterator가 필요한 이유
array나 vector의 경우 컨테이너가 메모리상에 순차적으로 존재한다고 보장할 수 있지만  
그 외의 컨테이너들은 list처럼 노드간 가리키고만 있거나, 트리구조이거나 해시구조라서  
꼭 메모리에 순차적으로 존재한다고 보장할 수 없음  

그러므로 배열에서처럼 index로 순차적 접근이 아닌  
iterator객체를 이용해 컨테이너의 element들의 순서를 저장하고 컨테이너를 탐색하는 것  

다만 array나 vector는 index 사용도 가능하므로 `[]`를 이용해서 직접접근도 가능하고, iterator 사용도 가능함  

#### 3. iterator의 형태
iterator는 각 컨테이너의 멤버변수이므로 각 컨테이너마다 자료형과 제공되는 형식이 다름  

vector의 경우 `std::vector<int>::iterator iter = vec.begin();`과 같은 형태로 사용됨  
다만 대부분의 경우 너무 길어서 `auto iter = vec.begin();`으로 줄여서 사용함  

#### 4. iterator 사용팁들
1) iterator 획득법
- 반복자는 begin과 end함수로 얻을 수 있음  
- `vec.begin()`은 벡터가 시작하는 위치를 가리킴
- 다만, `vec.end()`는 벡터의 ==끝보다 한칸뒤==를 가리킴  
	- 그로인해 `vec.begin() == vec.end()`라면 벡터가 비어있다라고 판단할 수 있게 됨

- cbegin(), cend(), rbegin(), rend(), crbegin(), crend()도 제공됨
	- c는 const, r은 reverse
	- rbegin()은 컨테이너의 가장 뒤의 값의 위치를 가리키며, rend()는 가장 첫 값보다 한칸 앞을 가리킴
	- 단, forward_list나 unordered류는 reverse iterator가 지원되지 않음

2) iterator로 값 참조
`std::vector<int>::iterator itr = vec.begin();` 이라고 했을 때 itr은 포인터처럼 쓰이므로  
vec의 첫 값을 알고싶으면 `cout<< *itr;` 처럼 사용하면 됨  

3) iterator로 컨테이너 순회
for문으로 컨테이너를 순회하고 싶다면 Range Based for를 쓰면 더 편하지만  
포인터나 배열처럼 쓰고 싶다면 `itr != vec.end();` 처럼 쓰면 끝이 아니면 반복한다 처럼 쓸 수 있음  
```C++
vector<int> vec;

// ...

for(int v : vec) {
	cout << v << ' ';
}

// 또는

for(auto itr = vec.begin(); itr != vec.end(); itr++) {
	cout << *itr << ' ';
}
```

reverse iterator로 순회한다면 아래와 같이 사용  
```C++
vector<int> vec;

// ...

for(auto ritr = vec.rbegin(); ritr != vec.rend(); ritr++) {
	cout << *itr << ' ';
}
```
주의할 점은 reverse iterator 사용시에도 itr++로 사용해야 한다는 것  
배열에서 index로 값의 뒤에서 앞으로 참조한다면 `index--`로 사용했겠지만 iterator는 rbegin부터 rend로 진행하므로 `ritr++`로 사용한다.  

4) iterator로 위치 지정
`vector.insert(itr값, 넣을값);`처럼 써서 값을 중간에 넣을 수 있음  
`vector.insert(vec.begin() + 2, 10);`  
  
`vector.erase(itr값);` 처럼 써서 해당 위치의 값을 지울 수 있음  
`vector.erase(vec.begin()+3);` // `vec[3]`을 지우고 4부터 한칸씩 땡김  
  
> [!warning] vector에 insert나 erase할때의 주의점  
> vector에 값을 추가하거나 삭제할 경우 기존의 itr은 무의미해짐  
> 저절로 한칸 옮겨서 계산 안해줌  
> 
> 그러므로 vector에 insert나 erase를 연속적으로 할 땐  
> insert나 erase 바로 뒷줄에 itr = vec.begin()같은걸로 다시 값을 지정해줘야함  
> 
> 근데 애초에 insert나 erase를 많이 한다면 list를 사용하는게 맞음

> [!note] 그럼에도 vector에 꼭 erase를 사용해야겠다면?
> iterator를 계속 재설정해야 하는 불편함을 해결하기 위해 remove 사용  
> 
> 값 한개를 지우려면 `v.erase(v.begin() + 5);` 처럼 사용하면 `v[5]`가 삭제되고 한칸씩 땡겨짐
> 특정 값을 모두 지우려면 반복적으로 지워야 하므로 `vec.erase(remove(vec.begin(), vec.end(), 3), vec.end());`처럼 사용
  
5) const_iterator  
`std::vector<int>::const_iterator citr = vec.cbegin() + 2;`처럼 쓰면 `*citr = 30;`같은건 오류가 난다.  
많은 경우 반복자로 값을 참조만 하고 변경은 하지 않으니 이럴땐 const를 쓰는게 안전하다  
const_iterator는 begin end 앞에 c를 붙여서 cbegin(), cend()로 사용  

6) reverse_iterator
컨테이너의 뒤에서부터 거꾸로 가는 반복자  
`auto ritr = vec.rbegin();` 는 벡터의 마지막 원소를 가리키고 vec.rend()는 벡터의 첫번째 원소보다 한칸 앞을 가리킴  
그러므로 ritr을 \+\+하면 한칸씩 앞으로감(일반적으로 인덱스 쓸때 \-\-로 하던것과는 다르니 주의)
  
const_reverse_iterator도 있고 crbegin(), crend()로 사용가능  
  
> [!nolte] reverse_iterator를 쓰는 이유
> 
>```
>for(auto i = vec.size() - 1; i >= 0; i--)  {
>	cout << vec[i] << ' ';   
>}
>```
> 위와 같은 상황일때 잘 될거같지만
> vector의 index를 담는 변수가 unsigned 변수라서 i가 -1이 되는게 아니라 오버플로우가 일어나서 가장 큰 수가 됨  
> 그래서 무한루프에 빠지게 되므로 역순으로 참조하는 반복문은 반드시 reverse_iterator를 쓰자  
