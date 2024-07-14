# type_traits

## 1. type_traits 라이브러리  

C++에서 타입 관련 연산을 위한 템플릿 함수들을 제공해주는 라이브러리  

#### 템플릿 메타 함수
메타 함수란 타입에 대한 연산을 수행함  
엄밀히 말하면 함수는 아니고 마치 함수처럼 동작하는 템플릿 클래스  

#### 예시코드)
```C++
#include <iostream>
#include <type_traits>

template <typename T>
void tell_type() {
    if (std::is_void<T>::value) {
        std::cout << "T 는 void ! \n";
    }
    else {
        std::cout << "T 는 void 가 아니다. \n";
    }
}

int main() {
    tell_type<int>();  // void 아님!
    tell_type<void>();  // void!
}
```

위의 `is_void<T>::value`를 템플릿 메타함수라고 부르는데 사실 ()로 호출하는 것도 아니라서 함수는 아니다.  
왜 함수라고 부르는지 모르겠다. 그냥 클래스다.  

내부 구현 방식은 아래와 유사하다.  
`<void>`에 대해서만 템플릿 특수화되어 `<void>`일때만 value가 true이고 나머지 타입은 value가 false이다.  
```C++
template <typename T>
struct is_void {
  static constexpr bool value = false;
};

template <>
struct is_void<void> {
  static constexpr bool value = true;
};
```

type_traits 라이브러리는 is_void같은 메타함수들을 제공한다.  
다시 말하지만 메타함수란 타입에 대한 연산을 수행하는 함수다.  
예를들면 int 타입인지 확인해주는 is_integral::value, 클래스인지 확인해주는 is_class가 있다.  


## 2. static_assert

C++11에 추가된 키워드이다. 함수가 아니다.  
인자로 전달된 식이 true인지 false인지 컴파일타임에 확인한다.  
인자로는 constexpr bool 형태만 전달할 수 있고  
결과가 true라면 해당 키워드는 없는셈치게 되고 false라면 컴파일 오류를 발생시킨다.  

즉 위에서 예시로 든 is_integral::value와 조합해서 사용한다면  
```C++
#include <iostream>
#include <type_traits>

class A {};

// 정수 타입만 받는 함수
template <typename T>
void only_integer(const T& t) {
    static_assert(std::is_integral<T>::value);
    std::cout << "T is an integer \n";
}

int main() {
    int n = 3;
    only_integer(n);

    A a;
    only_integer(a);
}
```
위의 경우 a가 정수가 아니므로 컴파일 오류가 발생한다.  
static_assert와 type_traits의 메타함수를 조합해 특정 타입만 받는 함수를 작성하기 좋다.  


## 3. SFINAE 원칙

치환 오류는 컴파일 오류가 아니다.  
`Substitution failure is not an error`

```C++
#include <iostream>

template <typename T>
void test(typename T::x a) { // A::x라는 타입이 있으니 문제없음
  typename T::y b;
}

template <typename T>
void test(typename T::y b) {
  std::cout << "T::y \n"; // A::y라는 타입은 없음, 오류가 발생함
}

struct A {
  using x = int;
};

int main() { test<A>(11); }
```

함수들이 오버로딩 되어있을때 템플릿 인자가 어느 함수로 들어가야 할 지 정해야한다.  
즉 어디로 들어갈지 함수들의 후보군이 있는데 템플릿이 해당 타입으로 치환된 후 만들어진 식이 문법적으로 맞지 않는다면 오버로딩 후보군에서 제외하게 된다.
이 과정에서 컴파일 오류를 발생시키지는 않는다.

근데 문제는 컴파일러가 해당 함수가 적합한지 검토하는 방식이 리턴형과 함수의 인자들만으로 판단을 할 뿐 함수 내부의 내용 전체까지 판단하는 것이 아니다.  
그래서 리턴타입과 매개변수에는 잘 대응되지만 함수 내부에서 오류가 발생할 상황인 함수가 후보군에 남아있는 문제가 생길 수 있다.  

그래서 SFINAE를 통해 조건에 맞지 않는 함수들을 오버로딩 후보군에서 빼줘야 한다.  
이 기능을 enable_if를 이용해서 구현할 수 있다.  


## 4. enable_if

enable_if는 SFINAE를 통해 조건에 맞지 않는 함수들을 오버로딩 후보군에서 제외할 수 있게 만들어주는 템플릿 메타함수이다.  

예를들어 T가 정수타입일때만 오버로딩을 하고 싶다면 아래와 같이 사용한다.  
```C++
std::enable_if<std::is_integral<T>::value>::type
```

#### 사용예시)
```C++
#include <iostream>
#include <type_traits>

template <typename T,
          typename = typename std::enable_if<std::is_integral<T>::value>::type>
void test(const T& t) {
  std::cout << "t : " << t << std::endl;
}

int main() {
  test(1);      // int
  test(false);  // bool
  test('c');    // char
}
```
위의 경우 `1, false, 'c'` 모두 정수로 대응 가능하므로 문제가 없다.  
반면 정수로 대응 불가능한 클래스의 객체를 넣는다면  
```C++
struct A {};

int main() { test(A{}); }
```
이런경우 컴파일 오류가 발생한다.  

C++ 14부터는 아래의 코드처럼 alia된 형태로 사용 가능하다. 코드가 너무 길어서 구현한듯 하다.  
```C++
typename = typename std::enable_if<std::is_integral<T>::value>::type>
// 위의 enable_if ~~ ::type과 아래의 enable_if_t는 동일한 기능을 한다.
typename = std::enable_if_t<std::is_integral_v<T>>
```

#### 특정 멤버 함수가 존재하는 타입을 인자로 받게 만든다면?
예를들어 A클래스에는 func()가 있는데 B클래스에는 func()가 없다.  
그래서 func()가 있는 클래스만 인자로 받을 수 있게 만든다면 아래처럼 사용하면 된다.  
```C++
#include <iostream>
#include <type_traits>

template <typename T, typename = decltype(std::declval<T>().func())>
void test(const T& t) {
  std::cout << "t.func() : " << t.func() << std::endl;
}

struct A {
  int func() const { return 1; }
};

int main() { test(A{}); }
```

만약 func의 return타입까지 정하고 싶다면 아래처럼 사용한다.  
```C++
#include <iostream>
#include <type_traits>

// T 는 반드시 정수 타입을 리턴하는 멤버 함수 func 을 가지고 있어야 한다.
template <typename T, typename = std::enable_if_t<
                        std::is_integral_v<decltype(std::declval<T>().func())>>>
void test(const T& t) {
  std::cout << "t.func() : " << t.func() << std::endl;
}

struct A {
  int func() const { return 1; }
};

struct B {
  char func() const { return 'a'; }
};

int main() {
  test(A{});
  test(B{});
}
```

함수 조건을 여러개 걸고 싶다면 아래처럼 사용한다.  
```C++
template <typename Cont, typename = decltype(std::declval<Cont>().begin()),
          typename = decltype(std::declval<Cont>().end())>
```

다만 위처럼 사용하면 함수가 너무 길어져서 복잡해진다.  
이를 해결하기 위해 void_t라는 메타함수를 사용할 수 있다.  


## 5. void_t

C++ 17부터 제공되는 메타함수이다.  
```C++
template <class...>
using void_t = void;
```
가변길이 템플릿으로 여러개를 받을 수 있는데 결국 void다.  
```C++
void_t<A, B, C, D>  // --> 결국 void
```

그래서 void_t에 전달된 인자들 중 문법적으로 맞지않는 템플릿 인자가 있으면 SFINAE에 의해 오버로딩 목록에서 제외된다.  
그래서 아래와 같이 좀 정리해서 사용이 가능하다.  
```C++
template <typename Cont, typename = decltype(std::declval<Cont>().begin()),
          typename = decltype(std::declval<Cont>().end())>
          
 // 위와 아래가 같은 역할을 한다.          
          
template <typename Cont,
          typename = std::void_t<decltype(std::declval<Cont>().begin()),
                                 decltype(std::declval<Cont>().end())>>
```

다만 위의 템플릿 형식은 사용자가 정상적인 `func<cont>();`형태가 아닌` func<cont, void>();` 형태로 사용하면 항상 void가 되니 사용자의 실수에 따라 문제가 생길 수 있다.  
그래서 이를 잡아주기 위해 타입 체크하는 부분을 다른곳으로 빼서 아래처럼 사용하는것에 최종 완성형 코드라고 한다.  
```C++
template <typename Cont>
std::void_t<decltype(std::declval<Cont>().begin()),
            decltype(std::declval<Cont>().end())>
print(const Cont& container)
```
타입을 체크하는 부분을 템플릿의 디폴트 인자가 아닌 함수의 리턴타입으로 옮겼다.  
함수의 리턴타입은 오버로딩 확인시 SFINAE가 확인하는 부분이므로 동일한 효과를 낼 수 있다.  






※ 참고 문헌  
[https://modoocode.com/295](https://modoocode.com/295)