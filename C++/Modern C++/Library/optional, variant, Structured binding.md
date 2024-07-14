# optional, variant, Structured binding


## 1. optional

C++ 17부터 지원  
`#include <optional>` 라이브러리 필요  
optional은 어떤 객체를 보관하거나/말거나 둘 중 하나의 상태를 가지는 객체이다.  

map에서 특정 값이 있는지 확인하고 있을때는 해당 값을 가져오고, 없다면 빈 문자열을 보내게 만든다면  
```C++
  auto itr = m.find(key);
  if (itr != m.end()) {
    return itr->second;
  }

  return std::string();
```
위처럼 사용이 가능하다.  

근데 문제는 값이 없는게 아니라, 있는데 하필 그 값이 빈 문자열이었다면? 그럼 값이 있는데 빈문자열인지 없어서 빈 문자열인지 확인이 어렵다.  

이럴때 optional을 사용한다. 즉 optional이란 원하는 값을 보관할수도, 안할수도 있는 클래스이다.  
```C++
std::optional<std::string> GetValueFromMap(const std::map<int, std::string>& m, int key) {
    auto itr = m.find(key);
    if (itr != m.end()) {
        return itr->second;
    }

    // nullopt 는 <optional> 에 정의된 객체로 비어있는 optional 을 의미한다.
    return std::nullopt;
}
```
optinal객체를 반환하도록 하고 위의 optional 객체는 string을 보관하던지/안하던지 둘 중 하나의 상태만 가진다.  
값이 있다면 그 값을 보관하는 상태를, 없다면 보관하지 않는 상태인 nullopt를 반환한다.  

다만 위에서 보면 보관하는 상태를 리턴한게 아니라 그냥 그 값을 리턴하는데 이 경우에도 알아서 optional 객체로 만들어져서 리턴된다고 한다. optional 자체에 객체를 포함해서 보낸다고 보면 되고 받는 쪽에선 값을 확인하고 싶다면 .value()를 사용하면 된다.  

위의 경우는 `GetValueFromMap(m, 2).value()`를 하거나 \*로 참조하면
가지고 있는 객체가 있다면 그 값을 확인할 수 있고  
가지고 있는 객체가 없다면 std::bad_optional_access 예외를 던진다.  

즉 예외를 발생시키지 않으려면 값을 가지고 있는지 없는지 미리 판단을 해야한다.  
if문으로 그냥 확인해도 되고 has_value()로 확인해도 된다.  
```C++
// 아래의 3개의 문장은 모두 같게 적용된다.

if (GetValueFromMap(data, 4)) GetValueFromMap(data, 4).value();

if (GetValueFromMap(data, 4).has_value()) GetValueFromMap(data, 4).value();

if (GetValueFromMap(data, 4).has_value()) *GetValueFromMap(data, 4); // *로도 참조 가능하다.
```

#### 주의점
optional은 일반적으로 레퍼런스를 가질 수 없다.  
이를 해결하기 위해 reference_wrapper를 사용해서 레퍼런스처럼 동작하는 wrapper 객체를 사용한다.  
```C++
std::optional<std::reference_wrapper<A>> maybe_a = std::ref(a);

// maybe_a.get() 으로 참조할 수 있다.
```


## 2. variant

C++ 17부터 지원  
one-of를 구현한 클래스  

컴파일 타임에 여러개의 타입 중 한가지를 보관할 수 있는 클래스이다.  
```C++
// v 는 이제 int
std::variant<int, std::string, double> v = 1;

// v 는 이제 std::string
v = "abc";

// v는 이제 double
v = 3.14;
```
포함하고자 하는 타입들을 명시해주고 그 중 하나의 타입을 가질 수 있다.  

다만 variant는 반드시 값을 가지고 있어야 한다.  
그래서 만약 v에 초기값을 넣지 않는다면 첫번째 인자인 int로 생성되며 0이 들어간다.  
다만 객체의 크기는 나열한 객체들 중 가장 큰 타입의 크기를 따라간다.  

#### 사용 예시
```C++
class A {};
class B {};

/* ?? */ GetDataFromDB(bool is_a) {
  if (is_a) return A();
  else return B();
}
```
variant가 없다면 위의 형태를 리턴하기 위해선 A와 B의 부모클래스를 만들어서 해당 형태로 리턴해야 한다.  
하지만 int나 flota 중 하나라면 둘은 기본 자료형이라 부모를 구현할수가 없다.  

이럴 때 variant가 있다면  
```C++
std::variant<A, B> GetDataFromDB(bool is_a) {
  if (is_a) {
    return A();
  }
  return B();
}
```
처럼 사용이 가능해진다.  

받는곳에선 아래처럼 사용하면 index()에는 몇번째 인자의 값이 들어있는지를, get으로 그 값을 확인 가능하다.  
```C++
std::cout << v.index() << std::endl; // 0 출력
std::get<A>(v).a();  // 혹은 std::get<0>(v).a()
```
다만 variant는 타입을 기준으로 객체들을 구분하므로 variant<string, string>처럼 같은 타입이 여러번 들어갈 수 없다.  

#### std::monostate
variant는 반드시 값을 가지고 있어야 한다. 만약 사용자가 직접 지정해주지 않았으면 첫번째 인자를 값으로 가진다고 했다.  
근데 만약 아래처럼 모든 인자가 기본 생성자가 없는 상황이라 들어갈 수 있는 값이 없다면 오류가 생긴다.  
```C++
class A {
 public:
  A(int i) {}
};

class B {
 public:
  B(int i) {}
};

std::variant<A, B> v;
```
그래서 아무것도 들어있지 않은 상태를 표현하는게 필요하다.  

monostate키워드가 아무것도 들어있지 않은 상태를 의미하며  
```C++
std::variant<std::monostate, A, B> v;
```
처럼 사용하면 아무것도 들어있지 않거나, A가 들어있거나, B가 들어있다는 뜻이 된다.  
이러면 마치 optional과 같은 기능을 하는 셈이 된다.  


## 3. Structured binding

C++ 17이상부터 지원  
기존에는 튜플의 값을 참조할때 아래와 같은 형태로 get<>을 사용했었음  
```C++
  int age = std::get<0>(student);
  std::string name = std::get<1>(student);
  bool is_male = std::get<2>(student);
```

근데 structured binding을 사용하면 아래와 같게 편하게 사용 가능  
```C++
  auto [age, name, is_male] = student;
```
위의 형태로 사용하면 값 복사형태이고 레퍼런스로 쓰고 싶다면 auto&로 사용하면 된다.  

다만 단점은 name만 필요할땐 `get<1>`하면 됐는데 structured binding 쓸거면 모든 값 다 받아야 한다.  
튜플뿐만 아니라 구조체에서도 사용 가능하다.  
```C++
struct Data {
  int i;
  std::string s;
  bool b;
};

Data d;
auto [i, s, b] = d;
```

pair에서도 `auto [key, value] = p;`처럼 사용 가능해서 광범위하게 사용 가능하다.






※ 참고 문헌  
[https://modoocode.com/309](https://modoocode.com/309)