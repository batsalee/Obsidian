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


## 9. shared_ptr  

일반적으로 하나의 자원은 한개의 스마트 포인터에 의해 소유되는것이 바람직하고 나머지 접근은 get함수를 이용해 일반 포인터로 접근하는 식으로 처리하는것이 좋다.  

하지만 여러 개의 스마트 포인터가 하나의 객체를 같이 소유해야 하는 경우가 발생할 수 있다.  
여러 객체에서 하나의 자원을 사용하는 경우 나중에 해당 자원을 해제하려면 해당 자원을 사용하는 모든 객체가 소멸되어야 하는데 어떤 객체가 먼저 소멸될지, 언제 소멸될지 알 수 없으므로 특정 자원을 몇개의 객체에서 가리키는지 추적해서 0이 되면 그때 해제시켜주는 방식의 스마트포인터가 필요하며 그게 shared_ptr이다.  

즉 unique_ptr과 달리 한 shared_ptr이 객체를 가리켜도 다른 shared_ptr도 그 객체를 가리킬 수 있다.  
```C++
std::shared_ptr<A> p1(new A());
std::shared_ptr<A> p2(p1);  // p2 역시 생성된 객체 A 를 가리킨다.

// 반면에 unique_ptr 의 경우
std::unique_ptr<A> p1(new A());
std::unique_ptr<A> p2(p1);  // 컴파일 오류!
```

#### 참조 개수
즉 여러개의 shared_ptr이 같은 객체를 가리킬 수 있으며 몇개의 shared_ptr이 원본 객체를 가리키는지 알아야 하므로 **제어블록**을 동적으로 할당하고 제어블록에 **참조개수**(reference count)를 저장하며 참조 개수가 0이 되면 원본객체가 소멸된다.  
그래서 shared_ptr이 복사생성될때마다 제어블록의 위치만 공유하면 신규 생성시 ++, 소멸시 --해주면서 count한다.

참조 개수가 몇개인지 알려면 아래처럼 use_count() 함수 사용하면 된다.  
```C++
std::shared_ptr<A> p1(new A());
std::cout << p1.use_count(); // 여기서 p1.use_count() 결과는 1

std::shared_ptr<A> p2(p1); // p2에 p1을 복사생성자로 초기화
std::cout << p1.use_count(); // 여기서 p1.use_count() 결과는 2

std::shared_ptr<A> p3;
p3 = p1; // 대입을 이용한 초기화, 여기서 참조개수는 3

마지막 shared_ptr의 수명이 다하면 참조횟수가 0이 되면서 delete해주면 된다.
```

shared_ptr 또한 make 함수가 지원되며 이걸 사용하는게 훨씬 좋다.  
```C++
auto p1 = std::make_shared<A>(); // std::shared_ptr<A> p1 = std::make_shared<A>();
```
왜냐하면 `std::shared_ptr<A> p1(new A());` 방식의 경우 new로 동적할당이 발생하고, shared_ptr의 제어블록을 만드는데 동적할당이 또 발생하므로 많은 리소스를 쓰는 동적할당을 두번하게 된다.  
make_shared로 한번에 하는게 더 이득이다.  


## 10. shared_ptr 생성시 주의할 점

shared_ptr은 인자로 주소값이 전달되면 자신이 해당 객체를 처음 소유하는 shared_ptr로 행동한다.  
그렇다보니 처음 만들어진 shared_ptr이 하는 행동인 제어블록을 만드는 행동을 하게되는데  
```C++
A* a = new A();
std::shared_ptr<A> pa1(a);
std::shared_ptr<A> pa2(a);
```
위처럼 두번 같은 주소값으로 shared_ptr을 생성하면 제어블록이 두개가 되어버린다.  
그럼 또 참조 count가 1이되므로 pa1이 소멸시 pa2가 살아있음에도 A를 소멸시켜버린다.  
그러므로 shared_ptr을 주소값을 통해 생성하는 것은 지양해야 한다.  

하지만 객체 내부에서 자기 자신을 가리키는 shared_ptr을 만든다면 this 포인터를 사용해야 하는데 이럴땐 주소값을 통해 생성할 수 밖에 없다.  
이 경우 `enable_shared_from_this`를 사용한다.  
클래스에 `: public std::enable_shared_from_this<A>` 를 상속받고 `return std::shared_ptr<A>(this);` 대신에 `return shared_from_this();` 함수를 통해 본인의 포인터를 리턴한다.

조건으로는 이미 해당 객체의 shared_ptr이 먼저 정의되어 있어야 한다.
```C++
#include <iostream>
#include <memory>

class A : public std::enable_shared_from_this<A> {
  int *data;

 public:
  A() {
    data = new int[100];
    std::cout << "자원을 획득함!" << std::endl;
  }

  ~A() {
    std::cout << "소멸자 호출!" << std::endl;
    delete[] data;
  }

  std::shared_ptr<A> get_shared_ptr() { return shared_from_this(); }
};

int main() {
  std::shared_ptr<A> pa1 = std::make_shared<A>();
  std::shared_ptr<A> pa2 = pa1->get_shared_ptr();

  std::cout << pa1.use_count() << std::endl;
  std::cout << pa2.use_count() << std::endl;
}
```


## 11. weak_ptr

![[Pasted image 20240604202817.png|400x150]]
만약 위처럼 서로를 참조하는 shared_ptr이 있다면 이 둘은 절대 해제될수가 없다.  
예를들어 트리 구조에서 부모 노드가 여러개의 자식 노드를 가지므로 shared_ptr을 사용하는데 자식노드도 부모노드를 기억하기 위해 shared_ptr을 사용한다면 위와 같은 문제가 생긴다.  

위와 같은 순환 참조를 해결하기 위해 존재하는게 weak_ptr이다.  
weak_ptr은 일반 포인터와 shared_ptr 사이에 위치한 스마트 포인터로, 스마트 포인터처럼 안전하게 객체를 참조할 수 있게 해주지만 shared_ptr과 달리 참조 개수를 늘리지는 않는다.  

weak_ptr이 만약 어떤 객체를 가리키고 있더라도 다른 shared_ptr이 가리키고 있지 않다면 참조개수가 0이므로 메모리에서 소멸된다.  
그러므로 weak_ptr 자체로는 원래 객체를 참조할 수 없고, 반드시 shared_ptr로 변환해서 사용해야 한다.  

weak_ptr을 shared_ptr로 변환하기 위해 lock 함수를 사용한다.(weak_ptr에 정의되어 있는 함수이다.)   
lock()의 기능은 weak_ptr이 가리키는 객체가 아직 메모리에 살아있다면(즉 참조개수가 0이 아니라면) 해당 객체를 가리키는 shared_ptr을 반환하고, 이미 해제가 되었다면 아무것도 가리키지 않는 shared_ptr을 반환한다.  

아무것도 가리키지 않는 shared_ptr은 false로 형변환되기 때문에 아래처럼 if문으로 간단히 확인 가능하다.  
```C++
void access_other() {
  std::shared_ptr<A> o = other.lock();
  if (o) {
    std::cout << "접근 : " << o->name() << std::endl;
  } else {
    std::cout << "이미 소멸됨 ㅠ" << std::endl;
  }
}
```

shared_ptr과 weak_ptr이 같이 가리키는 객체가 있을 때 shared_ptr들이 소멸되서 참조개수가 0이 된다면 weak_ptr이 아직 가리키고 있음에도 해당 객체는 메모리에서 해제되겠지만 제어블록에는 shared_ptr의 참조개수는 물론이고 weak_ptr의 참조 개수(약한 참조 개수)도 기록되며 둘 다 0이어야 해당 메모리들을 해제한다.

weak_ptr은 생성자로 shared_ptr이나 다른 weak_ptr을 받는다.  
예를들어 아래와 같은 방식으로 구현된다.  
```C++
void set_other(std::weak_ptr<A> o) { other = o; }

int main() {
  std::vector<std::shared_ptr<A>> vec;
  vec.push_back(std::make_shared<A>("자원 1"));
  vec.push_back(std::make_shared<A>("자원 2"));

  vec[0]->set_other(vec[1]); // set_other는 weak_ptr을 인자로 받지만 shared_ptr도 들어갈수있다.
  vec[1]->set_other(vec[0]);
  
  return 0;
}
```
weak_ptr은 당연히 이미 제어블록이 만들어진 shared_ptr이나 weak_ptr과 같은것을 가리키므로 평범한 포인터 주소값으로는 weak_ptr을 생성할 수 없다. 참조개수가 0이므로  

전체 참고 코드)
```C++
#include <iostream>
#include <memory>
#include <string>
#include <vector>

class A {
  std::string s;
  std::weak_ptr<A> other;

 public:
  A(const std::string& s) : s(s) { std::cout << "자원을 획득함!" << std::endl; }

  ~A() { std::cout << "소멸자 호출!" << std::endl; }

  void set_other(std::weak_ptr<A> o) { other = o; }
  void access_other() {
    std::shared_ptr<A> o = other.lock();
    if (o) {
      std::cout << "접근 : " << o->name() << std::endl;
    } else {
      std::cout << "이미 소멸됨 ㅠ" << std::endl;
    }
  }
  std::string name() { return s; }
};

int main() {
  std::vector<std::shared_ptr<A>> vec;
  vec.push_back(std::make_shared<A>("자원 1"));
  vec.push_back(std::make_shared<A>("자원 2"));

  vec[0]->set_other(vec[1]);
  vec[1]->set_other(vec[0]);

  // pa 와 pb 의 ref count 는 그대로다.
  std::cout << "vec[0] ref count : " << vec[0].use_count() << std::endl;
  std::cout << "vec[1] ref count : " << vec[1].use_count() << std::endl;

  // weak_ptr 로 해당 객체 접근하기
  vec[0]->access_other();

  // 벡터 마지막 원소 제거 (vec[1] 소멸)
  vec.pop_back();
  vec[0]->access_other();  // 접근 실패!
}
```






※ 참고 문헌
[https://modoocode.com/252](https://modoocode.com/252)
[https://modoocode.com/229](https://modoocode.com/229)