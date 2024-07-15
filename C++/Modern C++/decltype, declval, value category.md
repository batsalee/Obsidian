# decltype, declval, value category

## 1. decltype  

C++ 11에 추가된 키워드  
decltype(타입을 알고자 하는 식)이라는 함수처럼 사용된다.  
다만 진짜 함수라기보다는 인자로 주어진 식의 타입으로 치환된다.(매크로 같은 느낌)  

#### 예시코드
```C++
#include <iostream>

struct A {
    double d;
};

int main() {
    int a = 3;
    decltype(a) b = 2;  // int b = 2;

    int& r_a = a;
    decltype(r_a) r_b = b;  // int& r_b = b;

    int&& x = 3;
    decltype(x) y = 2;  // int&& y = 2;

    A* aa;
    decltype(aa->d) dd = 0.1;  // double dd = 0.1;
}
```

#### 의문사항
decltype이 아닌 auto를 써도 되지 않을까?  
```C++
int a = 3;
auto b = 2;  // int b = 2;
```
위처럼 쓰면 똑같다.  
근데 auto는 타입을 완벽하게 추론하지 못한다.  

예를들어  
```C++
const int i = 4;
auto j = i;         // int j = i;
decltype(i) k = i;  // const int k = i;
```
auto는 const를 잃게되지만 decltype은 const까지 유지하게 된다.  

또는
```C++
int arr[10];
auto arr2 = arr;     // int* arr2 = arr;
decltype(arr) arr3;  // int arr3[10];
```
auto는 배열을 암시적으로 포인터로 형변환한다, 반면 decltype은 배열 타입도 그대로 유지한다.  

#### 또 다른 사용 예시
템플릿 함수에서 타입이 템플릿의 인자들에 의해 결정된다면  
즉 아래의 경우 t + u의 결과 타입에 의해 result의 자료형을 결정해야 한다면 사용 가능하다.  
```C++
#include <iostream>
using namespace std;

template <typename T, typename U>
void add(T t, U u) {
	decltype(t + u) result = t + u; // auto result도 되긴한다.
	cout << result;
}

int main() {
	add<int, double>(2, 2.5);
}
```

만약 위의 방식처럼 result에 보관하는게 아니라 즉시 t + u를 리턴하고 싶다면 아래처럼 사용한다.

```
template <typename T, typename U>
decltype(t + u) add(T t, U u) { // 이건 오류가 발생한다. 
  return t + u;					// t와 u의 선언이 리턴문보다 뒤에 있기 때문
}

// 제대로 된 사용법
template <typename T, typename U>
auto add(T t, U u) -> decltype(t + u) {
    return t + u;
}
```

#### 예외 케이스

어떠한 연산을 하지 않고 단순히 객체 하나만을 가리키는 식(식별자 표현식)이라면 decltype의 인자로 넣었을때 해당 타입으로 치환된다.

만약 식별자 표현식이 아니라면

- 값의 종류가 xvalue라면 decltype은 T&&가 된다.

- 값의 종류가 lvalue라면 decltype은 T&가 된다.

- 값의 종류가 prvalue라면 decltype은 T가 된다.

위의 분류에 따라 생기는 특이한 케이스가 있는데

```
int a, b;
decltype(a + b) c;  // 이 경우 a + b는 prvalue이므로 a + b의 실제 타입인 int로 추론됨

// 반면
decltype((a)) d; // 이 경우는 (a)를 봐야하는데 &(a)로 주소값연산을 취할 수 있고 이동불가능이라 lvalue가 됨
// 그래서 int&로 추론됨
```

|   |
|---|
|## declval|

C++ 11에서 추가됨

#include <utility> 필요

decltype과는 다르게 키워드가 아니라 함수

auto로 대부분 대체가능해서 type_traits 라이브러리랑 연계할때 주로 사용하는듯

```
template <typename T>
decltype(T().f()) call_f_and_return(T& t) {
  return t.f();
}

// 위 경우 f()의 리턴형으로 함수의 리턴형도 정해짐
// 근데 문제는 T()로 썼는데 해당 템플릿을 사용한 클래스에 기본 생성자가 없다면?
// 오류가 발생함

// 이걸 막아주는게 declval
template <typename T>
decltype(std::declval<T>().f()) call_f_and_return(T& t) {
  return t.f();
}
// 위처럼 쓰면 T의 생성자를 직접 호출하지 않고도 T가 생성된 객체를 가리킴
```

|   |
|---|
|## Value Category|

여기부터 아래 내용은 전부터 봐왔지만 영 이해가 안된다....

언젠가 다시 한번 공부해보는걸로

모든 C++ 식에는 두가지 정보가 항상 따라다닌다.

- 식의 타입

- 값 카테고리

타입은 일반적으로 생각하는 타입이다.

값 카테고리는 좌측값/우측값등의 개념이다.

C++에서 값 카테고리는 5가지로 나뉜다.

- lvalue : 좌측값

- rvalue : 우측값

- glvalue(generalized lvalue) : 일반화된 좌측값

- prvalue(pure rvalue) : 순수 우측값

- xvalue(eXpiring value) : 소멸하는 값

![](https://blog.kakaocdn.net/dn/dqKz42/btsIx9OvMIr/aULwBveRZKYNdL0UFsFes1/img.png)

기준은 아래와 같다.

- 정체를 알 수 있는가? 없는가?

- 이동시킬 수 있는가? 없는가?

|   |   |   |
|---|---|---|
||이동 시킬 수 있다|이동 시킬 수 없다|
|정체를 알 수 있다|xvalue|lvalue|
|정체를 알 수 없다|prvalue|쓸모없음|

정체를 알 수 있는가?라는 말은 해당 식이 다른 식과 같은것인지 다른것인지 구분할 수 있는가? 라는 말과 같고 변수라면 주소값을 확인하면 되고 함수라면 이름을 확인해보면 된다.

정체를 알 수 있는 모든 식을 glvalue라고 한다.

이동시킬 수 있는가?라는 말은 해당 식을 다른곳으로 안전하게 이동할 수 있는가?라는 말과 같고 해당 식을 받는 이동생성자, 이동대입연산자를 사용할 수 있는지 확인하면 된다.

이동시킬 수 있는 모든 식을 rvalue라고 한다.

|   |
|---|
|## lvalue|

```
int i;
```

i는 주소값을 확인해서 다른 변수와 구분할 수 있으니 정체를 알 수 있다.

i는 이동 불가능하다. int&& x = i;는 컴파일되지 않는다.

즉 i는 lvalue다.

이름을 가진 대부분의 객체들은 모두 lvalue다.

#### lvalue의 종류

- 변수, 함수의 이름, 어떤 타입의 데이터 멤버(std::endl, std::cin 등)

- 좌측값 레퍼런스를 리턴하는 함수의 호출 식(cout << 1이나 ++itr)

- 위와 비슷한 이유로 a = b, a += b, a *= b 같은 복합 대입 연산자들

- ++a, --a같은 전위 증감 연산자들

- 문자열 리터럴 "hello"

- a[n]같은 배열 참조 식

- 객체가 멤버를 참조할때 멤버변수가 enum이 아닌 경우

- 객체가 멤버를 참조할때 멤버함수가 static인 경우

```
class A {
  int f();         // static 이 아닌 멤버 함수
  static int g();  // static 인 멤버 함수
}

A a;
a.g;  // lvalue
a.f;  // lvalue 아님 (prvalue)
```

즉 변수뿐 아니라 함수의 결과가 좌측값인 경우도 해당 함수식을 좌측값이라고 부른다.

lvalue들은 주소연산자(&)을 이용해서 식의 주소값을 알아낼 수 있다.

즉 &++i, &std::endl도 가능하다.

lvalue들은 좌측값 레퍼런스를 초기화하는데 사용 가능하다.

#### 주의점

타입과 값 카테고리를 분리해서 생각해야한다.

int&& a = 3;이라면 a의 타입은 우측값 레퍼런스지만, a의 값 카테고리는 좌측값이다.

|   |
|---|
|## prvalue|

```
int f() { return 10; }
f();
```

위의 f()는 주소연산자를 취할 수 없다, 하지만 우측값 레퍼런스에 붙을 수 있다.

그래서 f()는 prvalue다.

이게 뭔 말이지 도대체?????????

#### prvalue의 종류

- 문자열 리터럴을 제외한 모든 리터럴(10, true, nullptr 등)

- 레퍼런스가 아닌 것을 리턴하는 함수의 호출식(str1 + str2)

- 후위 증감 연산자 식(a++, a--)

- 디폴트로 제공되는 산술/논리 연산자 식(a + b, a && b, a < b), 연산자 오버로딩된건 또 다른가봄

- 주소값 연산자 식 &a

- 객체가 멤버변수를 참조할때 멤버변수가 enum일때

- 객체가 멤버함수를 참조할때 멤버함수가 static이 아닐때

- this

- enum값

- 람다식

등등

prvalue는 주소값을 취할 수 없기때문에 &a++이나 &42등은 불가능함

또한 prvalue는 좌측에 올 수 없음

prvalue는 우측값 레퍼런스와 상수 좌측값 레퍼런스를 초기화하는데 사용할 수 있다.

```
const int& r = 42; // 상수 좌측값 레퍼런스
int&& rr = 42;	   // 우측값 레퍼런스
// int& rrr = 42; <-- 불가능
```

|   |
|---|
|## xvalue|

lvalue와 prvalue만 있다면 좌측값을 이동 시킬 방법이 없음. 그래서 xvalue가 필요함

이게 그래서 도대체 뭔 소리냐고......???????????

좌측값 처럼 정체가 있지만 이동도 시킬 수 있는 것

우측값 레퍼런스를 리턴하는 함수의 호출식

대표적으로 std::move()

move의 return 타입은 우측값이고 그럼 move를 사용한 식의 결과가 우측값이니까

해당 식은 좌측값 레퍼런스를 초기화하는데 사용할 수도 있고, prvalue처럼 우측값 레퍼런스에 붙이거나 이동생성자에 전달해서 이동 시킬 수도 있다.

뭔소린지 이젠 진짜 하나도 모르겠다.

※ 참고 문헌

[https://modoocode.com/294](https://modoocode.com/294)