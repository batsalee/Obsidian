# constexpr  

## 1. constexpr

C++ 11에 도입  
객체나 함수 앞에 붙일 수 있는 키워드이다.  
해당 객체나 함수의 리턴값을 컴파일 타임에 알수있다라는 의미를 갖는다.  

컴파일타임에 어떤 식의 값을 결정할 수 있다면 해당 식을 상수식이라고 부른다.  
상수식 중에서도 식의 결과가 정수인 정수상수식이 자주 사용된다.  

예를들어  
```C++
int arr[size]; // 이처럼 사용이 가능하려면 size는 상수식이어야 한다.

enum A { a = number, b, c }; // 이처럼 사용 가능하려면 number가 상수식이어야 한다.

template <int N>
struct A {
    int operator() { return N; }
};
A<N> a;  // 이 경우도 N이 상수식이어야 한다.
```
즉 constexpr은 어떤 식이 상수식이라고 명시해주는 키워드이다.  

#### 사용 예시
위의 코드에 constexpr을 사용해보면  
```C++
constexpr int size = 10;
int arr[size]; // 이처럼 사용이 가능하려면 size는 상수식이어야 한다.

constexpr int number = 3;
enum A { a = number, b, c }; // 이처럼 사용 가능하려면 number가 상수식이어야 한다.

constexpr int N = 10;
template <int N>
struct A {
    int operator() { return N; }
};
A<N> a;  // 이 경우도 N이 상수식이어야 한다.
cout << a();
```


## 2. constexpr vs const

const는 굳이 컴파일 타임에 그 값을 알 필요가 없다.  
```C++
int a;

// ...

const int b = a;
```
위의 경우 컴파일시점에 b의 값을 알수 없다고 해도 b가 결국 a의 값을 따르며, 한번 지정되면 다시는 바꿀 수 없다는 점이 명백하다.  

반면 constexpr은 반드시 컴파일타임에 그 값을 알수있다는 뜻이므로 constexpr 변수는 오른쪽에 상수식이 와야 한다.  
```C++
int a;

// ...

constexpr int b = a; // 불가능
```

또한 컴파일러가 const로 지정된 변수라면 컴파일타임에 초기화를 할 수 있음에도 굳이 하지 않고 런타임에 하는 식으로 조절할수 있다.  
반면 constexpr은 반드시 컴파일타임에 초기화하도록 강제하게된다.  


## 3. constexpr 함수

constexpr이 등장하기 전에는 컴파일 타임 상수인 객체를 return하는 함수를 작성할 수 없었다.  
예를들어 아래와 같은 코드는  
```C++
#include <iostream>

int factorial(int N) {
  int total = 1;
  for (int i = 1; i <= N; i++) {
    total *= i;
  }
  return total;
}

template <int N>
struct A {
  int operator()() { return N; }
};

int main() { A<factorial(5)> a; }
```
A<factorial(5)> a;에서 factorial(5)가 컴파일타임 상수가 아니므로 사용이 불가능했다.  
이걸 해결하려면 템플릿 메타 프로그래밍을 해야해서 코드가 굉장히 복잡해졌었다.  

반면 constexpr을 사용한다면  
```C++
#include <iostream>

constexpr int Factorial(int n) {
  int total = 1;
  for (int i = 1; i <= n; i++) {
    total *= i;
  }
  return total;
}

template <int N>
struct A {
  int operator()() { return N; }
};

int main() {
  A<Factorial(10)> a;

  std::cout << a() << std::endl;
}
```
위처럼 단지 Factorial(10)의 결과값을 컴파일타임에 상수화해서 사용하면 쉽게 사용이 가능하다.  

#### constexpr 함수의 인자
constexpr 함수에 인자로 컴파일타임 상수를 전달한다면  
```C++
Factorial(10)
// 또는
constexpr int ten = 10;
Factorial(ten);
```
인자가 컴파일타임 상수이므로 return값도 constexpr로 잘 리턴된다.  

반면 constexpr 함수의 인자에 일반적인 변수가 들어간다면?  
여전히 사용 가능하다. 단 리턴값이 constexpr이 아닌 일반 값이된다.  
즉 일반적인 함수처럼 사용이 된다.  

그러므로 함수에 constexpr을 붙일 수 있다면 붙여주는것이 좋다.  
다만 그럼에도 constexpr을 사용하지 못하는 경우도 있다.  

#### constexpr 함수 내부에서 사용하지 못하는 것들
- goto문 사용시
- C++ 20 이전의 경우 예외처리시
- **리터럴 타입**이 아닌 변수의 정의
- 초기화 되지 않는 변수의 정의
- constexpr 함수 내부애서 constexpr이 아닌 함수를 호출하는 경우


## 4. 리터럴 타입

리터럴 타입이란 컴파일러가 컴파일 타임에 정의할 수 있는 타입을 말한다.  
리터럴 타입의 객체들만이 constexpr로 선언되거나, constexpr 함수 내부에서 사용 가능하다.  
- void
- 스칼라 타입(char, bool, int, long, float, double 등등)
- 레퍼런스 타입
- 리터럴 타입의 배열
- 혹은 아래의 조건을 만족하는 타입
  - 디폴트 소멸자를 가지고
  - 다음중 하나를 만족하는 타입
    - 람다함수
    - Arggregate 타입(사용자 정의 생성자, 소멸자가 없으며 모든 데이터 멤버들이 public), ex) pair
    - constexpr 생성자를 가지며 & 복사 및 이동 생성자가 없음

C++ 14부터 constexpr 생성자를 지원해서 사용자가 리터럴 타입을 직접 만들수 있게 되었다.  


## 5. constexpr 생성자

생성자를 constexpr로 만드는 경우 constexpr의 제약조건이 모두 적용됨과 동시에  
constexpr 생성자의 인자들 또한 반드시 리터럴 타입이어야 하고  
constexpr 생성자가 있는 클래스는 다른 클래스를 가상상속받을 수 없음  

#### 예시코드
```C++
#include <iostream>

class Vector {
 public:
  constexpr Vector(int x, int y) : x_(x), y_(y) {}

  constexpr int x() const { return x_; } // 이들도 constexpr로 선언해서 constexpr 함수내에서 사용 가능
  constexpr int y() const { return y_; }

 private:
  int x_;
  int y_;
};

constexpr Vector AddVec(const Vector& v1, const Vector& v2) {
  return {v1.x() + v2.x(), v1.y() + v2.y()};
}

template <int N>
struct A {
  int operator()() { return N; }
};

int main() {
  constexpr Vector v1{1, 2}; // constexpr 생성자로 만들었기에 객체도 constexpr로 선언 가능
  constexpr Vector v2{2, 3};

  // constexpr 객체의 constexpr 멤버 함수는 역시 constexpr!
  A<v1.x()> a;
  std::cout << a() << std::endl;

  // AddVec 역시 constexpr 을 리턴한다.
  A<AddVec(v1, v2).x()> b;
  std::cout << b() << std::endl;
}
```


## 6. if constexpr

if constexpr은 조건이 반드시 bool로 타입변환될 수 있어야 하는 컴파일타임상수식이어야만 사용가능  
그래서 조건이 참이라면 else 부분은 컴파일되지 않고 완전 무시됨  
조건이 거짓이라면 else부분만 컴파일됨  
```C++
template <typename T>
void show_value(T t) {
  if constexpr (std::is_pointer_v<T>) {
    std::cout << "포인터 이다 : " << *t << std::endl;
  } else {
    std::cout << "포인터가 아니다 : " << t << std::endl;
  }
}
```
위의 경우 T t로 int a가 들어온다면 a는 포인터가 아니니 거짓이 되고, 그럼 else부분만 컴파일됨  






※ 참고 문헌  
[https://modoocode.com/293](https://modoocode.com/293)