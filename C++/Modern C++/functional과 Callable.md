# functional과 Callable  

## 1. Callable이란

Callable이란 말 그대로 호출(Call)할 수 있는 모든 것을 의미한다.  
C++에서는 ()를 붙일 수 있는 모든것을 Callable이라고 한다.  

#### Callable 예시
- 함수
- 구조체의 () operator
- 람다함수

아래의 경우 some_obj()는 함수가 아니다.  
S클래스의 객체에 () operator를 구현했을 뿐이다.  
하지만 ()로 호출할 수 있으니 Callable이다.  
```C++
#include <iostream>

struct S {
    void operator()(int a, int b) {
        std::cout << "a + b = " << a + b;
    }
};

int main() {
  S some_obj;
  some_obj(3, 5);
}
```

아래의 경우도 일반적인 함수의 꼴은 아니지만 ()를 통해 호출할 수 있으니 Callable이다.  
```C++
auto f = [](int a, int b) { std::cout << "a + b = " << a + b << std::endl; };
f(3, 5);
```


## 2. function 클래스

C++에서는 Callable들을 객체의 형태로 보관할 수 있는 std::function 클래스를 지원한다.  
C에서의 함수포인터는 정말로 함수만 보관 가능하지만 function 객체는 함수 뿐만 아니라 모든 Callable을 보관할 수 있다.  
그러니까 C++버전의 함수포인터인데 기능이 더 확장된것이라고 생각해도 될듯하다.  

#### 사용 예시
`#include <functional>` 라이브러리를 추가하고 사용한다.  
function<> 템플릿에 리턴형(매개변수형)을 넣으면 된다.  
```C++
#include <functional>
#include <iostream>
#include <string>

int some_func1(const std::string& a) {
  std::cout << "Func1 호출! " << a << std::endl;
  return 0;
}

struct S {
  void operator()(char c) { std::cout << "Func2 호출! " << c << std::endl; }
};

int main() {
  std::function<int(const std::string&)> f1 = some_func1;
  std::function<void(char)> f2 = S();
  std::function<void()> f3 = []() { std::cout << "Func3 호출! " << std::endl; };

  f1("hello");
  f2('c');
  f3();
}
```

#### 만약 멤버함수를 function에 넣는다면?
일반함수는 위처럼 쓰면 쉽게 사용 가능하다.  
반면 멤버함수는 해당 멤버함수를 호출하는 객체에 대한 정보가 필요하다.  
```C++
class A {
    int c;

public:
    A(int c) : c(c) {}
    int some_func() { std::cout << "내부 데이터 : " << c << std::endl; }
};

int main() {
    A a(5);
    std::function<int()> f1 = a.some_func;
}
```
위처럼 사용한다면 f1은 어느 객체의 함수인지 알 수 없어서 오류가 난다고 한다.  
그러므로 function 객체로 만들때 어떤 객체의 함수인지 정보를 함께 넘겨줘야 한다.  
```C++
std::function<int(A&)> f1 = &A::some_func;
```
만약 함수가 const함수라면 아래처럼 const A& 형태로 function객체를 생성해야 한다.  
```C++
int some_const_function() const {
    std::cout << "상수 함수: " << c << std::endl;
    return c;
}

std::function<int(const A&)> f2 = &A::some_const_function;
```

그 후 사용은 아래와 같이 매개변수에 해당 객체를 명시하면서 사용한다.  
```
f1(a);
f2(a);
```

혹은 mem_fn함수를 사용할 수 있다.  
mem_fn함수는 전달된 멤버함수를 function객체로 만들어서 return해준다.  
```C++
std::mem_fn(&vector<int>::size)
```
하지만 보통 람다함수로 구현 가능하면 람다로 구현한다고 한다.  
굳이 라이브러리 추가할 필요도 없고 간단하게 구현 가능하기 때문  


## 3. bind 함수

함수 객체 생성시에 인자를 지정할 수 있다.  
마치 매개변수에 default값을 부여한것과 유사한 느낌이다.  
```C++
#include <functional>
#include <iostream>

void add(int x, int y) {
    std::cout << x << " + " << y << " = " << x + y << std::endl;
}

void subtract(int x, int y) {
    std::cout << x << " - " << y << " = " << x - y << std::endl;
}

int main() {
    auto add_with_2 = std::bind(add, 2, std::placeholders::_1);
    // 위처럼 사용하면 add함수의 매개변수를 (2, placeholders::_1)로 지정한다는 뜻이다.
    add_with_2(3);
    // 그럼 이렇게 호출하면 매개변수 첫번째 자리에 3이 들어있으니 placeholders::_1 = 3이다.
    // 즉 위의 함수호출은 add(2, 3);과 같다.

    // 만약 placeholders 자리가 이미 다 찼으면 그 이후에 입력된 인자는 무시된다.
    add_with_2(3, 4); // 이건 그냥 3만 placeholders::_1로 들어가고 4는 무시된다.

    auto subtract_from_2 = std::bind(subtract, std::placeholders::_1, 2);
    // 위처럼 사용하면 subtract함수에 매개변수를 (placeholders::_1, 2)로 지정한다는 뜻이다.
    subtract_from_2(3);  // 이 함수 호출은 subtract(3, 2)가 되므로 3 - 2 를 계산한다.
    
    auto negate = std::bind(subtract, std::placeholders::_2, std::placeholders::_1);
    // 위의 경우는 subtract의 매개변수를 (placeholders::_2, placeholders::_1)로 사용한다는 뜻이다.
    negate(4, 2); // 즉 매개변수 둘의 위치가 바뀌었으므로 2 - 4 를 계산한다
}
```

placeholders의 `_1`, `_2`는 bind쪽에 정의된 객체들이고 `_1`부터 `_29`까지 정의되어 있다고 한다.  

다만 주의할점은 bind로 지정한 값이 상수가 아니라 변수라면 레퍼런스가 아닌 밸류로 복사된다.  
그래서 레퍼런스로 전달하고 싶다면 `std::ref()`로 전달해줘야 한다.  
ref함수는 전달받은 인자 변수를 복사 가능한 레퍼런스로 변환해준다.  
그냥 &s1로 해봤더니 복사과정에서 오류가 나는듯했다. 이동생성자로 추론되는것 같았다.  
```C++
auto do_something_with_s1 = std::bind(do_something, std::ref(s1), std::placeholders::_1);
do_something_with_s1(s2);
```
만약 const 레퍼런스로 변환하고 싶다면 cref()를 사용하면 된다.  






※ 참고 문헌  
[https://modoocode.com/254](https://modoocode.com/254)