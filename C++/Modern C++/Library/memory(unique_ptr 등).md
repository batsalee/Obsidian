# memory 라이브러리(unique_ptr, shared_ptr, weak_ptr)  


## 1. RAII 디자인 패턴

Resource Acquisition Is Initialization의 약자로 **자원의 획득은 초기화다** 라는 뜻이며  
자원의 관리를 스택에 할당한 객체를 이용해서 수행하는 패턴이다.  

프로그램 진행 중 만약 예외가 발생해서 함수를 빠져나가도 스택에 있는 객체들은 모두 소멸자가 호출된다.  
그냥 자연스레 사라지는게 아니라 소멸자가 호출되는 방식이다.  
반면 힙에 있는 객체들은 예외발생시 정상적으로 소멸자가 호출되지 않아서 메모리릭이 일어나게 된다.  

그래서 힙에 있는 데이터들을 스택에 있는 객체가 관리하게 만들어서 메모리릭을 방지하는 방법론이다.


## 2. 스마트포인터

C++에서 메모리를 잘못된 방식으로 관리하면 두가지 문제점이 생길 수 있다.  
1) 메모리 릭(RAII패턴으로 해결가능)  
2) 이미 해제된 메모리를 다시 참조하는 경우(소유권으로 해결가능)

예를들어 만약 생성자 내에서 예외가 발생해서 생성자가 정상적으로 마무리 되지 않으면 소멸자가 호출되지 않으므로 소멸자에 있는 delete가 동작하지 않는다.  
또는 delete문을 작성해뒀어도 delete문 이전에 예외가 발생하는 경우 delete가 이루어지지 않는다.  

즉 개발자가 아무리 동적할당에 대해 delete/free를 잘 작성해준다고 해도 예상치 못하는곳에서 메모리릭이 발생할 수 있다.  
이런 상황에 대비하기 위해 힙에 있는 데이터들을 스택에 있는 객체가 관리하게 하는 RAII패턴을 적용하여 만든 스택에 있는 객체가 스마트포인터다.  

스마트포인터 구현 방법은 일반자료형의 포인터변수에 동적할당을 사용하는것이 아니라 포인터 변수를 멤버변수로 갖는 객체를 만들어서 사용하는 것이다.  
이런 객체를 포인터객체라고 부르고 이 객체가 스마트포인터의 역할을 한다.  
해당 객체가 소멸될때 이 객체는 스택에 있는 존재이므로 반드시 소멸자가 호출 되고 그럼 소멸자에서 delete를 사용하게 작성해서 메모리릭을 막아줄 수 있다.

다만 이를 통해 RAII패턴을 적용해 메모리릭을 막는다고 해도 아래와 같은 double free문제가 생길 여지가 남아있다.  
```C++
Data* data = new Data();
Data* data2 = data;

delete data;
delete data2; // 이미 할당 해제된 객체를 또 소멸시키려 함
```
data변수와 data2변수는 같은 곳을 가리키고 있는데 data와 data2를 모두 delete하면 같은곳을 두번 delete하는꼴이 된다.  
이런 버그를 double free 버그라고 부르며 이미 소멸된 객체를 또 소멸시키려 하면 메모리 오류가 나면서 프로그램이 죽게 된다.  

double free가 발생하는 이유는 만들어진 객체의 소유권이 명확하지 않기 때문이다.  
만약 Data객체의 할당해제를 data 또는 data2 둘중 하나의 포인터만 할 수 있는 권한이 있었다면 double free문제는 생기지 않았을 것이다.  

이러한 문제점들을 보완해서 modern C++에서 제공하는 스마트 포인터가 바로 unique_ptr, shared_ptr, weak_ptr이다.  


## 3. unique_ptr

유일한 소유권이 부여된 스마트포인터를 unique_ptr이라고 한다.  
아래처럼 unique_ptr을 이용하면 RAII패턴을 적용해서 메모리릭도 막을 수 있고, 유일한 소유권도 가지므로 double free문제도 일어나지 않는다.
```C++
#include <iostream>
#include <memory>

class A 
{
	int *data;

 public:
	A() 
	{  
		data = new int[100];
	}

	void some() { std::cout << "일반 포인터처럼 사용\n"; }

	~A() 
	{
		std::cout << "자원을 해제함!" << std::endl;
		delete[] data;
	}
};

void do_something() {
	std::unique_ptr<A> pa(new A()); // A pa = new A(); 와 동일
	pa->some();
}

int main() 
{
	do_something(); 
}​
```


## 4. unique_ptr의 소유권 이전

만약 아래처럼 유일한 소유권을 가져가려 하면 **삭제된 함수**를 사용했다는 컴파일 오류가 발생한다.  
```C++
void do_something() {
  std::unique_ptr<A> pa(new A());
  std::unique_ptr<A> pb = pa; // 오류발생
}
```
삭제된 함수란 개발자가 사용을 원하지 않는 함수를 삭제시키는 개념이며 아래처럼 사용한다.  
명시적으로 해당 함수를 사용하지 못하도록 금지하는 것이다.  
unique_ptr은 유일하게 소유권을 가져야 하므로 당연히 복사생성자 사용을 금지해야 하고 복사생성자가 삭제되어있다.  
```C++
#include <iostream>


class A {
 public:
  A(int a){};
  A(const A& a) = delete;
};

int main() {
  A a(3);  // 가능
  A b(a);  // 불가능 (복사 생성자는 삭제됨)
}
```

다만 unique_ptr의 복사생성자는 삭제되어있지만 이동생성자는 허용되어 있어서 이동생성자를 이용해 소유권 이전이 가능하다.  
```C++
void do_something() {
  std::unique_ptr<A> pa(new A());
  std::unique_ptr<A> pb = std::move(pa);  // pb 에 소유권을 이전.
}
```

★ 하지만 소유권을 이전 후 pa는 nullptr을 가리키고 있으므로 pa->some()같은걸 하면 런타임 오류가 발생한다.  
그러므로 소유권 이전을 하려면 이전하기 전의 포인터는 다시는 참조하지 않는다는 확신이 있어야 한다.  


## 5. 함수의 인자로 unique_ptr을 전달하는 방법

```C++
// 올바르지 않은 전달 방식
void do_something(std::unique_ptr<A>& ptr) { ptr->do_sth(3); }

int main() {
  std::unique_ptr<A> pa(new A());
  do_something(pa);
}
```
만약 위코드처럼 레퍼런스로 전달하면 전달은 오류없이 잘 되지만 해당 객체에 pa로도 접근 가능하고 ptr로도 접근 가능하므로 유일하게 소유권을 가진다는 개념을 위배하게 된다.  

그러므로 레퍼런스로 전달하면 안되고 unique_ptr가 가리키는 포인터 주소값을 전달하도록 해야 한다.  
```C++
void do_something(A* ptr) { ptr->do_sth(3); }

int main() {
  std::unique_ptr<A> pa(new A());
  do_something(pa.get());
}
```
위처럼 unique_ptr 객체에 get함수를 사용하면 실제 객체의 주소값을 리턴해준다.  


## 6. unique_ptr을 쉽게 생성하기

C++ 14부터 unique_ptr을 쉽게 만들 수 있는 std::make_unique 함수를 제공한다.  
```C++
int main() {
  auto ptr = std::make_unique<Foo>(3, 5);
  ptr->print();
}
```
`std::unique_ptr<Foo> ptr(new Foo(3, 5));`와 같은 기능을 한다.  
근데 뭐 더 쉬운건진 잘 모르겠다.  
사실상 unique_ptr보단 shared_ptr쪽에서 필요해서 만든 기능인데 만드는김에 make_unique도 만든게 아닐까...?


## 7. unique_ptr 사용시 주의사항

unique_ptr을 원소로 가지는 STL 컨테이너 사용시 unique_ptr은 복사생성자가 없으므로 주의해야 한다.
```C++
int main() {
  std::vector<std::unique_ptr<A>> vec;
  std::unique_ptr<A> pa(new A(1));

  vec.push_back(pa);  // 복사생성자가 없어서 사용 불가능
}
```
위처럼 사용하면 pa를 복사해서 vector에 넣어야 하는데 복사생성자가 delete 되어있으니 오류가 발생하므로 pa를 벡터에 넣어주려면 이동생성자를 사용하기 위해 move를 사용해야 한다.
```C++
int main() {
  std::vector<std::unique_ptr<A>> vec;
  std::unique_ptr<A> pa(new A(1));

  vec.push_back(std::move(pa));  // 잘 실행됨
}
```
이동생성자는 noexcept로 잘 구현되어있나보다.


## 8. 강제로 할당 해제 하는 방법

만약 스코프가 끝나기 전에 원하는 시점에 delete처럼 할당을 해제 하고 싶다면  
```C++
ptr.reset(nullptr);
```
을 사용하면 된다.  







※ 참고 문헌
[https://modoocode.com/229](https://modoocode.com/229)