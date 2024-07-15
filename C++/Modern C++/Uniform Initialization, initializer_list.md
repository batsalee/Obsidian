# Uniform Initialization, initializer_list

## 1. Uniform Initialization  

균일한 초기화  
C++ 11에서 추가됨  

이미 나도 모르게 쓰고 있었던 기능인데 내부 로직에 대해 자세히 알게 됐고 다른 곳에도 쓰일 수 있다는걸 알게 됐음  
아래의 코드를 보면 간단히 이해된다.  
```C++
#include <iostream>
using namespace std;

class A {
private:
	int a;
public:
	A() { cout << "기본 생성자 호출" << '\n'; }
	A(int a) { cout << "int 생성자 호출" << '\n'; }
};

int main() {
	A a;		// 기본 생성자 호출
	A b();		// 아무것도 출력하지 않음
	A c(1);		// int 생성자 호출
	A d{};		// 기본 생성자 호출
	A e{ 1 };	// int 생성자 호출
}
```
핵심은 A b(); 인데 기본 생성자로 b를 생성할 것 같지만 그렇지 않다.  
C++ 컴파일러는 A b(); 형태를 보면 A를 return하는 b라는 이름의 함수를 선언한것이라고 판단한다.  
그래서 객체 생성이 아닌 함수 선언이므로 기본 생성자가 호출되지 않는다.  

말 그대로 사용자의 실수에 의한 문제인건데 이걸 C++ 11 이후로는 균일한 초기화가 도입되어서  
() 대신에 {}를 쓰면 문제가 없게 됐다.  
즉 객체 생성시에 {}를 붙이면 된다.  

다만 ()과 {}로 사용하는 방식에서의 한가지 차이점이 있다.  
{}의 경우는 **데이터손실이 있는 암시적 형변환을 허용하지 않는다**는 점이다.  
```C++
#include <iostream>
using namespace std;

class A {
 public:
  A(int x) { std::cout << "int 생성자 호출!" << '\n'; }
};

int main() {
  A a(3.5);  // 암시적 형변환 되어서 3으로 들어감
  A b{3.5};  // 암시적 형변환 불가능해서 컴파일오류 발생함
}
```
예를들어 double에서 int로의 변환이나 double에서 float로의 변환등은 데이터 손실이 발생할 수 있는 형변환이므로 허용하지 않는다.  
그래서 {}를 사용하면 데이터손실을 컴파일오류로 잡아내기 쉬워진다.  


## 2. 객체의 타입 생략

이 부분이 나도 모르게 사용해왔던 부분인데  
보통 난 pair를 return하게 되면 return { 1, 2 }; 같은 형식으로 리턴하곤 했었다.  
이게 만약 return ( 1, 2 );였다면 당연히 오류가 발생했을 것이다.  
이 기능이 균일한 초기화로인해 가능한 것이었다.  

또 다른 예시는 아래와 같다.  
```C++
#include <iostream>

class A {
 public:
  A(int x, double y) { std::cout << "A 생성자 호출" << std::endl; }
};

A func() {
  return {1, 2.3};  // A(1, 2.3) 과 동일
}

int main() { func(); }
```
위에서 func()안에서 return할때 A객체임을 명시하지 않아도 괜찮은것이 균일한 초기화 덕분이었다.  


## 3. initializer_list

초기화자 리스트  
이 또한 나도 모르게 사용하고 있던 부분이다. 이번기회에 내부로직을 알게됐다.  
```C++
int arr[] = { 1, 2, 3, 4 };
vector<int> v = { 1, 2, 3, 4 };
```
위에서 arr의 경우는 일반적인 배열의 형태인데 vector 객체 생성시에도 마찬가지로 사용이 가능하다.  
이게 가능한 이유가 initializer_list때문이었다.  

initializer_list는 **{}를 이용해서 생성자를 호출**할때  
클래스의 여러 생성자들 중에 **initializer_list를 인자로 받는 생성자가 있다면**  
**최우선**으로 전달되어서 사용된다.  
즉 ()를 이용해서 사용하면 전달되지 않고 또한 다른 생성자들보다 우선적으로 사용된다.  

먼저 initializer_list를 인자로 받는 생성자의 예시다.  
```C++
#include <iostream>
using namespace std;

class A {
public:
    A() {}; 
    A(int x) {}
    A(int a, int b, int c, int d, int e) {}
    
    A(initializer_list<int> l) { // initializer_list를 인자로 받는 생성자
        for (auto itr = l.begin(); itr != l.end(); ++itr) {
        std::cout << *itr << std::endl;
        }
    }
};

int main() 
{
    A a = {1, 2, 3, 4, 5};
}
```
위의 경우 a객체는 3번째 생성자에도 합당하지만 initializer_list를 인자로 받는 생성자가 있으니 4번째 생성자가 호출되게 된다.  
물론 initializer_list가 아닌 그냥 A객체를 인자로 받는 생성자를 구현해서 사용해도 비슷한 기능을 구현할 수 있다.  
하지만 초기화할때 편의성이 장점이라고 보면 될 듯 하다.  

#### initializer_list 주의점
initializer_list를 인자로 받는 생성자가 있다면 **최우선**으로 고려된다고 했다.  
예를들어 vector의 경우  
```C++
vector<int> v{10}; // 이렇게 생성하면 그냥 10이 한개 들어있는 v가 생성된다.
// 이때 선택된 생성자는 initializer_list를 인자로 받는 생성자이다.

vector<int> v(10); // 이렇게 생성하면 공간이 10칸 있는 v가 생성된다.
// 이때 선택된 생성자는 vector(size_type count); 이다.
```

또한 균일한 초기화는 **데이터 손실이 발생할 수 있는 형변환을 허용하지 않는다**.  
```C++
#include <initializer_list>
#include <iostream>

class A {
 public:
  A(int x, double y) { std::cout << "일반 생성자! " << std::endl; }

  A(std::initializer_list<int> lst) {
    std::cout << "초기화자 사용 생성자! " << std::endl;
  }
};

int main() {
  A a(3, 1.5);  // Good
  A b{3, 1.5};  // Bad!
}
```
위의 경우 ()로 생성하면 initializer_list 생성자가 호출되지 않으므로 일반생성자가 호출된다.  
반면 {}로 생성한 b는 initializer_list 생성자가 **최우선**으로 호출되므로 int만 받는 템플릿클래스니까 double값이 들어갈 수 없어서 오류가 발생한다.  

다만 아래처럼 아예 형변환이 불가능한 관계라면 initializer_list를 인자로 받는 생성자가 있어도 오류없이 사용이 가능하다.  
예를들어 int와 double을 입력하는데 string을 받는다면 형변환이 불가능하므로   
```C++
#include <initializer_list>
#include <iostream>
#include <string>

class A {
 public:
  A(int x, double y) { std::cout << "일반 생성자! " << std::endl; }

  A(std::initializer_list<std::string> lst) {
    std::cout << "초기화자 사용 생성자! " << std::endl;
  }
};

int main() {
  A a(3, 1.5);        // 일반
  A b{3, 1.5};        // 일반
  A c{"abc", "def"};  // 초기화자
}
```
위와 같은 경우는 문제없이 사용 가능하다.  


## 4. initializer_list와 auto

|                           |
| ------------------------- |
| ## initializer_list와 auto |

auto 자료형에 {}를 이용해서 객체를 생성한다면 initializer_list가 생성된다.

```
auto list = {1, 2, 3};
// 위 식은 아래와 같다.
initializer_list<int> list{1, 2, 3};
```

아래의 경우는 C++ 버전에 따라 다르게 처리된다.

auto로 사용할때의 얘기임을 명심해야한다.

= 를 붙여서 생성하는 경우와 안붙이고 생성하는 경우의 판정이 다르다.

= 를 붙이면 auto는 항상 initializer_list로 추론된다.

반면 안붙이면 값이 한개면 해당 값의 타입으로 추론된다. 값이 여러개면 오류다.

```
auto a = {1};     // 버전 상관없이 initializer_list<int>
auto c = {1, 2};  // 버전 상관없이 initializer_list<int>

auto b{1};        // C++ 11이라면 initializer_list<int>, C++ 17이라면 그냥 int
auto d{1, 2};     // C++ 11이라면 initializer_list<int>, C++ 17이라면 그냥 오류발생
```

아래의 경우는 initializer_list<const char*>가 된다.

```
auto list = {"a", "b", "c"};
```

만약 const char*가 아닌 string으로 만들고 싶다면 리터럴연산자를 붙여줘야 한다.

마치 숫자 0 쓸때 0LL쓰면 long long으로 취급되는것과 같은 기능인듯하다.

```
using namespace std::literals;  // 문자열 리터럴 연산자를 사용하기 위해 추가해야함
auto list = {"a"s, "b"s, "c"s};
```

※ 참고 문헌

[https://modoocode.com/286](https://modoocode.com/286)