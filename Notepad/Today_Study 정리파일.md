# Today_Study 정리하기

## 1. stoi 사용 시 core dump 오류

stoi(buffer) 사용 시 core dump가 뜨는 이유는 ==buffer가 비어있기 때문==  
그러므로 if로 buffer가 비어있는지 확인하고 사용해야 함
```C++
if(buffer != "") stoi(buffer);
```
★ 포인터나 문자열에 접근하기 전에는 항상 != null 사용하는 습관 들이기

## 2. pair STL

``` C++
pair<int, int> ip;
ip.first = 1;
ip.second = 2; // 이런식으로 대입도 가능
    
cout << "ip.first : " << ip.first << ", ip.second : " << ip.second << endl;
 
pair<int, double> idp(1, 2.5); // 이런식으로초기화도 가능
cout << "idp.first : " << idp.first << ", idp.second : " << idp.second << endl;
 
pair<string, int> sip;
sip = make_pair("스트링", 10); // make_pair를 사용해 대입도 가능
cout << "sip.first : " << sip.first << ", sip.second : " << sip.second << endl;
```
make_pair는 C++11버전이고 C++17이후로 {}를 쓰는 듯하다  
{}를 써도 문제 없는걸로 보이니 둘 다 알아두기

## 3. 메모리 공간

프로그램이 운영체제로부터 할당받는 메모리공간은 대표적으로 4가지  
코드영역부터 낮은주소값에 위치하고, 스택영역이 가장 높은 주소에 위치  
1. 코드영역 : 실행할 프로그램의 코드
2. 데이터영역 : 전역변수, 정적변수(extern, static)
	초기화 된 데이터가 들어가는 data영역과 초기화되지 않은 데이터가 들어가는 bss영역으로 나누기도 함
3. 힙영역 : 동적할당영역(런타임에 크기가 결정됨, 낮은 주소에서 높은 주소 순으로 할당)
4. 스택영역 : 지역변수, 매개변수(컴파일시에 크기가 결정됨, 높은 주소에서 낮은 주소 순으로 할당)

## 4. 생성자
#### 기본생성자
생성자를 하나라도 정의하는 순간 디폴트생성자는 생성되지 않게 돼서 다 구현해줘야 하는 불편함이 있었는데  
``` C++
class Test   
{  
public :  
	Test() = default;  
};
```
이런식으로 디폴트생성자를 만들어라 라고 명시할 수 있음

#### 복사생성자
기본생성자를 만들지 않아도 컴파일러가 자동으로 생성해주듯이 복사생성자도 개발자가 구현하지 않아도 컴파일러가 자동으로 생성해준다.  
다만 디폴트 복사생성자의 경우 얕은 복사를 수행한다.  
value는 잘 복사를 하지만 reference를 복사할땐 얕은 복사를 하게 되므로 이때 깊은 복사를 원한다면 복사생성자를 직접 구현해서 정의해줘야 한다.  
- 얕은 복사 : 값들을 복사하되, 만약 참조타입의 값이라면 주소값만 복사가 된다. 즉 원본값이 바뀌면 얕은 복사를 한 대상의 값도 바뀐다.
- 깊은 복사 : 주소값만 복사해서 같은걸 가리키는게 아니라 가리켜지는 객체 자체를 하나 더 만들어서 복사하는 것  
	즉, 새로 동적할당을 받고 그 공간에 원본의 데이터를 복사

깊은 복사를 원하는 경우 복사생성자를 이용해서 구현  
복사생성자가 호출되는 상황은 말 그대로 "생성자"가 호출되는 시점에 "복사"가 필요한 경우  
```C++
String s1("abc"); // 이 경우 그냥 생성자가 호출되서 대입됨  
String s2(s1); // 이 경우 복사생성자가 호출 됨, s1을 s2로 복사  
String s3 = s1; // 이 경우 또한 윗줄과 같은 취급이기때문에 복사생성자가 호출됨  

단, String s3; s3 = s1;의 경우 생성하는 상황에서 복사되는게 아니라 생성한 후 대입되는 것이므로 복사생성자가 호출되는게 아님
```

### 상속을 받았다면?
파생클래스의 생성자는 당연히 기반클래스가 있어야 생성가능하다.  
파생클래스의 생성자를 호출시 기반클래스의 기본생성자가 먼저 사용된 후 파생클래스의 생성자가 사용된다.  
만약 기반클래스의 기본생성자가 아닌 다른 생성자를 사용하고 싶다면  초기화리스트에 기반생성자의 해당생성자를 적어줘야 한다.  

## 5. explicit, mutable

#### explicit  
explicit : 명시적인 / implicit : 암시적인  
즉 explicit을 사용하면 명시적으로만 쓸 수 있기에 암시적으로 쓰는걸 막아주는 역할을 함  

만약   
MyString(const char* str); 생성자가 있고  
func(MyString("abc")); 식으로 인자로 MyString객체를 받는 함수가 있다면  
이때 func("abc");라고 사용해도 컴파일러가 가장 비슷한 형태인  
func(MyString("abc")); 처럼 암시적으로 변환을 해줌.  
이런식으로 암시적 변환이 일어났을때 입력값이 어디 들어갈지 애매해져서  
암시적변환을 막고싶으면 explicit 사용  
explicit MyString(const char* str); 처럼 하면 해당 생성자로는 암시적 변환이 막힘  
  
#### mutable  
mutable Cache cache; 처럼 선언하면 const함수 내에서도 cache값을 변경할 수 있음  
당연히 const함수는 값을 바꾸지 않는 함수이기 때문에 const로 선언한건데
값을 바꿔야 하는 경우 const를 지울 수도 있겠지만  
외부에 아무 영향도 주지 않고 내부작업에서 바꾸는 값이 있는 경우라면  
외부에서 보기엔 당연히 const가 붙어있어야 할 함수이기에 const를 붙여 둔채로  
바꿀 변수쪽에 mutable을 붙이면 const내부에서도 값을 바꿀 수 있음  

## 6. 연산자 오버로딩

``` C++
/* (리턴 타입) operator(연산자) (연산자가 받는 인자) */
bool operator==(MyString& str);  

/*
위 식으로 str1 == str2 라고 쓴다면  
이는 내부적으로 str1.operator==(str2)로 처리함
*/
```

#### 연산자 오버로딩의 두가지 방식
1. a.operator@(b); // 여기선 @연산자가 a객체의 인스턴스 함수로 사용됨
2. operator@(a, b); // 여기선 @연산자가 외부에 정의된 일반 함수로 사용됨

컴파일러는 연산자 오버로딩이 있으면 위 두가지 경우에 골라서 사용하게 됨  
통상적으로 자기 자신을 리턴하는 경우 1번케이스인 멤버함수로 선언(+=나 -=같은 것)  
자기 자신을 리턴하지 않는 연산자는 2번케이스인 외부함수로 선언(+ - * /같은 것)  
외부에 일반함수로 선언하면 a나 b가 private인 경우 접근을 못하므로 해당 클래스에 friend로 추가해주면 됨  

## 7. stoi와 atoi로 숫자 뽑아내기

#### atoi
첫 문자부터 확인해서 숫자가 아닌게 나올때까지 읽어서 반환해줌  
첫 문자가 숫자가 아닌 경우 0으로 변환 됨  
``` C++
cout << atoi("abc"); // 0
cout << atoi("123"); // 123
cout << atoi("123abc"); // 123
cout << atoi("abc123"); // 0
```

#### stoi
첫 문자부터 확인해서 숫자가 아닌게 나올때까지 읽어서 반환해줌  
첫 문자가 숫자가 아닌 경우 atoi와는 다르게 오류가 발생함  
```C++
cout << stoi("abc"); // 오류발생
cout << stoi("123"); // 123
cout << stoi("123abc"); // 123
cout << stoi("abc123"); // 오류발생
```

## 8. 형변환(명시적/암시적)

- static_cast : 흔히 생각하는 일반적인 타입 변환  
- const_cast : const를 없애는 변환, const int 가 int 로  
- dynamic_cast : 기반/파생클래스 사이에서의 다운캐스팅  
- reinterpret_cast : 위험을 감수하고 하는 캐스팅, 예를들어 서로 관련이 없는 포인터들 사이의 캐스팅 등  

``` C++
(원하는 캐스팅 종류)<바꾸려는 타입>(무엇을 바꿀 것인가?)  
static_cast<int>(float변수); //float을 int로 형변환
```

## 9. 업캐스팅과 다운캐스팅
#### 업캐스팅
문제가 발생하지 않음  
파생클래스에서 기반클래스로 캐스팅하는 것  
```C++
Derived d;
Base *ap = &d; // 파생클래스 객체를 기반클래스로 넣는 것
```

#### 다운캐스팅
절대 권장되지 않음  
기반클래스의 객체를 파생클래스쪽에 넣는 것  

그럼에도 사용 가능한 경우가 있는데  
```C++
Base p;
Derived c;

Base* p_p = &c; // 이 경우 업캐스팅이니 문제가 없음

// 근데 아래의 다운캐스팅은 가능함
Derived* p_c = p_p; // 이렇게 되면 Derived에 Base를 넣으니 다운캐스팅임

/*
원래대로라면 다운캐스팅이라 문제가 될 여지가 많지만 애초에 p_p가 c를 가리키고 있고  
c는 애초에 파생클래스의 객체이므로 Derived* p_c = p_p;는 사실 파생클래스에서 파생클래스로 넣는 것
*/
```
단 위와 같이 가능한 경우에도 오류가 발생한다면 오류를 알려주도록 dynamic_cast를 사용

## 10. is-a와 has-a
#### is-a  
```
class Manager : public Employee  
```
위처럼 쓰면 의미는  
1. Manager클래스는 Employee의 모든 기능을 포함한다.  
2. Manager클래스는 Employee의 모든 기능을 수행 할 수 있기 때문에 Manager를 Employee라고 칭할 수 있다  
3. 즉 모든 Manager는 Employee다. (Manager is a Employee)
그러므로 모든 상속관계는 is-a관계라고 볼 수 있음  

#### has-a  
자동차 클래스를 만든다면 엔진클래스, 타이어클래스, 브레이크클래스 등이 필요해지는데  
이를 상속받는다고 해서 자동차는 엔진이다. 자동차는 브레이크다. 이렇게 되면 말이 안되고  
자동차는 엔진을 가진다. 자동차는 브레이크를 가진다. 이럴때 has-a 관계  
```C++
class Car  
{  
	Encing e;  
	Brake b;  
}
```

## 11. 상속
#### virtual
``기반클래스* ap = &파생클래스객체;`` 처럼 선언할때 ap.func()를 하면 기반클래스의 함수가 사용되는데  
그 이유는 기반클래스에는 파생클래스의 정보가 없으므로 파생클래스가 애초에 존재하는지  
또 그 파생클래스에 func()함수가 있는지 모르기 때문이라고 함(V-Table 개념)  

다만 기반클래스의 func()에 virtual이 붙어있으면 파생클래스에 func()가 있는지 확인하고  
있으면 파생클래스의 func를, 없다면 기반클래스의 func를 호출하는 것  
이런 식으로 런타임중에 정해지는걸 동적바인딩이라고 함  

==기반클래스의 소멸자는 반드시 virtual로 선언==해야 함  
그렇지 않으면 ``Parent *p = new Child();`` 같은 경우 delete p;하면 부모의 소멸자만 호출가능하므로  
사실은 Child 객체인데 소멸이 제대로 작동되지 않게 됨  

사실상 모든 함수에 virtual을 붙여도 파생클래스에 해당 함수가 없으면 기반클래스의 함수를 사용해주므로 편할 것 같지만  
가상함수에는 그만큼 약간의 오버헤드가 존재하기 때문에 직접 선언하게 되어있음  
다만 Java같은 경우는 이미 모든 함수가 default로 virtual로 선언됨  

#### 순수가상함수와 추상클래스
- 순수가상함수
``virtual void func() = 0;``처럼 virtual과 = 0을 붙여서 반드시 오버라이딩하게 만드는 함수
가이드라인만 제공하기 위한 설계도의 개념이며 인터페이스라고도 함

- 추상클래스
순수가상함수를 하나라도 포함하고 있는, 반드시 상속되어야 하는 클래스

#### override 키워드
override개념은 이전부터 있었지만 이 키워드는 C++ 11에 나온 것  
파생클래스에서 기반클래스의 가상함수를 오버라이드하는 경우 명시적으로 나타낼 수 있음  
void what() override { std::cout << s << std::endl; } 처럼 뒤에 override를 붙이면 명시적으로 표현 가능  
override한다고 썼는데 기반클래스에 what이 없어서 override할게 없으면 컴파일오류로 알려줌  
안적으면 안알려주니까 적으면 실수를 줄여줄듯  

#### 다형성
하나의 메소드를 호출했음에도 그떄그때 다른 작업을 하는 것  
오버로딩이나 오버라이딩에 의한 동적바인딩 같은 상황  

## 12. cin 및 cin의 오류 관련
#### cin
cin의 4가지 상태  
- good : 입출력 작업이 가능할 때  
- bad : 복구 불가능한 오류 발생시  
- fail : 복구 가능한 오류 발생시  
- eof : 입력 작업시에 EOF 도달시

```c++
if(std::cin.fail()) // cin 오류 발생시  
{  
	std::cin.clear(); // 플래그들을 초기화하고  
	std::cin.ignore(100, '\0'); // '\0'이 나올때까지 무시  
}
```

#### 입력형식 바꾸기
setf함수를 사용  
setf함수는 인자를 1개 받는 경우와 2개 받는경우 2개가 있는데  
1개만 받는건 인자로 준 형식플래그 적용하는 것  
2개 받는건 두번째 인자의 내용을 초기화하고, 첫번째 인자를 적용하는 것  

입력값을 16진수 취급하게 하고 싶다면(ff입력시 255로, 123입력시 291로 취급)  
``std::cin.setf(ios_base::hex, ios_base::basefield);``  

또는 ``std::cin >> hex >> t;`` 처럼 써도 똑같게 됨  
여기서 hex같이 스트림을 바꿔주는 함수를 조작자라고 부름(위의 ios_base::hex와는 전혀 다른 것)  

hex외에도 boolalpha, left(왼쪽정렬), right(오른쪽정렬), endl, flush 등도 모두 조작자  

precision은 정밀도  

## 13. getline
```C++
getline(cin, buf); // \n 바로 앞글자까지 받음, string라이브러리  
getline(cin, buf, '+'); // + 바로 앞글자까지 받음('\n'도 다 저장하고 +들어와야만 끝남), 뒷 문자열 다 잘림 string라이브러리
cin.getline(buf, bufsize, '+'); // + 바로 앞글자까지 받음('\n'도 다 저장하고 +들어와야만 끝남), iostream라이브러리
```

## 14. template함수
1) 기본적인 형태의 template 함수  
	add_num(변수); 로 쓰면 알아서 변수 자료형에 맞게 처리해줌  
``` C++
template <typename T>
T add_num(T t)
{
	return t;
}
```

2) template 인자 추가  
	아래 코드를 add_num<int, 5>(x); 처럼 사용하면 x+5를 리턴해줌  
```C++
template <typename T, int num>  
T add_num(T t)  
{  
	return t + num;  
}
```
	
디폴트인자도 추가 가능  
 아래처럼 add_num(x);하면 알아서 5 더해서 리턴해줌  
``` C++
template <typename T, int num = 5>  
T add_num(T t)  
{  
	return t + num;  
}
```

#### 가변길이 템플릿(Variadic template)
C++ 11에 추가됨  
``` C++
#include <iostream>

template <typename T>
void print(T arg) {
  std::cout << arg << std::endl;
}

template <typename T, typename... Types>
void print(T arg, Types... args) {
  std::cout << arg << ", ";
  print(args...);
}

int main() {
  print(1, 3.1, "abc");
  print(1, 2, 3, 4, 5, 6, 7);
}
```
위에서 ``template <typename T, typename... Types>``에서 ...을 템플릿 파라미터 팩이라고 부름  
0개 이상의 템플릿 인자들을 의미  
반면 ``void print(T arg, Types... args)``에서 ...은 함수 파라미터 팩이라고 부름  
0개 이상의 함수 파라미터를 의미  

파라미터팩은 추론된 인자를 제외한 나머지들을 나타내게 됨  
print(1, 3.1, "abc");를 호출하면  
첫번째 인자는 1이니 int로 사용되고 arg는 1로 처리됨  
그리고 나머지들은 args에 3.1과 "abc"로 들어감  
그리고 print(args...);에 3.1과 "abc"가 들어가서 또 동작  
재귀적으로 사용  

주의할점은 재귀적으로 사용되므로 반드시 재귀 종료를 위한 함수가 필요하고 그게 윗쪽에 선언된 print이며  
함수 작성된 순서도 당연히 윗쪽의 print가 먼저와야 오류가 발생하지 않음  

#### Fold
C++ 17에 추가됨  
위의 방식은 재귀호출을 위한 베이스케이스가 필요하고 종료조건이 필요하니 코드가 더 복잡해 질 수 있음  
Fold를 쓰면 더 간단해짐  
``` C++
#include <iostream>  
  
template <typename... Ints>  
int sum_all(Ints... nums) {  
  return (... + nums);  
}  
  
int main() {  
  // 1 + 4 + 2 + 3 + 10  
  std::cout << sum_all(1, 4, 2, 3, 10) << std::endl;  
}  
``` 
return (... + nums); 부분이 return ((((1 + 4) + 2) + 3) + 10); 처럼 해석됨  
fold식은 반드시 (... + nums)처럼 괄호로 감싸야 한다  

※ 참고문서 : https://modoocode.com/290

## 15. using

C++ 11부턴 typedef보다 더 직관적인 using을 사용  
``typedef unsigned int UINT;``를 아래처럼  
``using UINT = unsigned int;`` 처럼 사용 가능  

타입 뿐 아니라 함수포인터도  
``typedef void (*func)(int, int);`` 였다면  
``using func = void (*)(int, int);``로 사용 가능  

## 16. 예외처리
예외란?  
문법상에는 문제가 없지만 실행해보면 오류가 발생하는 정상적이지 않은 상황들  
할당되지 않은 메모리에 접근하거나, 할당 할 수 없는 너무 큰 메모리를 할당 하는 등의 경우  

```C++
// C언어에서의 예외처리 방식
char *c = (char*)malloc(1000000000);
if(c == NULL) {
	printf("메모리 할당 오류");
	return;
}
```
C언어에서는 위처럼 일단 해보고, 잘 됐는지 검사하고 잘 됐으면 동작하되, 잘 안됐으면 특정 값을 리턴하게 했었음  

C++에서는 특정 값을 리턴할 필요 없이 throw로 예외를 던져 명시적으로 나타낼 수 있음  
``throw std::out_of_range("vector의 index가 범위를 초과했습니다.");``  
위처럼 throw로 out_of_range 객체를 던져주면 보다 명시적으로 표현 가능  

C++ 표준 라이브러리에는 out_of_range외에도 많은 예외객체가 정의되어있음  
[C++예외의 종류](https://smallpants.tistory.com/39)  
[[C++ 예외의 종류]]

물론 꼭 정의되어 있는 객체만 던져야하는 것은 아니고 스스로 만든 객체를 던져도 됨  
애초에 객체가 대단한 기능이 있는게 아니라 예약어에 가까워서 그냥 정해둔 이름을 쓰는것뿐이지 중요한 의미가 있지는 않음  

예를들어 3칸짜리 벡터가 있는데 4번인덱스에 접근한다면  
``if(index > vec.size()) throw out_of_range("예외 발생");`` 처럼 쓰는 방식이고  
결국 범위를 벗어났는지 판단하는건 if문 조건이 파악하는 것이고 던지는 객체명만 out_of_range인 것  
즉 ``if(out_of_range)``처럼 쓰는게 아님!!  

함수 호출하는 쪽에서 예외가 발생할 수도 있겠다 싶으면 try로 감싸줌  
호출된 함수쪽에서 예외가 발생하면 throw해주고 호출하는 쪽에서 catch문으로 받아줌  
```C++
try {
	func(c);
}
catch(char x) {
	std::cout << "Char : " << x << std::endl;
}
```

만약 예외처리를 하지 않으면 예외가 발생한 순간 해당 함수가 즉시 종료되고  
뒷부분의 함수 내용들이 작동하지 않으며 함수에서 할당한 stack에 있는 객체나 값들도 모두 소멸시킴  
반면 예외처리로 예외를 잡아주면 뒷부분의 내용은 잘 작동하게 할 수 있음  
```C++
string str = "abc";
try {
	cout << str.at(10); // out_of_range 예외 발생
	cout << "try문 내부\n"; // 바로 윗줄에서 예외가 발생하므로 출력되지 않음
}
catch(...) {
	cout << "catch문 내부\n"; // 예외가 발생했으므로 예외 처리부분인 catch내의 이 문구가 출력됨
}

cout << "예외처리부분 이후\n"; // 만약 위에서 try~catch문을 사용하지 않고 그냥 str.at(10)만 있었다면 실행되지 않을 문구
							  // 하지만 여기선 try~catch문을 사용해서 예외를 잡아줬으므로 이 문구도 출력됨
```
결과)
```
catch문 내부
예외처리부분 이후
```

또한 catch문은 여러개 쓸 수 있음  
아래처럼 작성하면 발생한 예외가 위에서부터 순서대로 들어갈 수 있는곳을 찾아 들어가서 해당 catch문으로 처리함  
``` C++
try {
	func(c);
}
catch (char x) {
    std::cout << "Char : " << x << std::endl;
}
catch (int x) {
    std::cout << "Int : " << x << std::endl;
}
catch (std::string& s) {
    std::cout << "String : " << s << std::endl;
}
catch (const char* s) {
    std::cout << "String Literal : " << s << std::endl;
}
```

#### 예외처리시 주의사항
1) catch문의 순서
catch문을 여러개 작성한다면 발생한 예외가 위에서 아래로 내려가며 들어갈 수 있는 매개변수에 들어가서 처리함  
만약 아래처럼 catch문이 나열되어 있으면 부모객체던 자식객체던 전부 다 위에서 걸려버리게 됨  
``` C++
catch(기반클래스 x) {}
catch(파생클래스 x) {}
```
그러므로 파생클래스는 파생클래스 방식대로 처리하고 싶다면  
``` C++
catch(파생클래스 x) {}
catch(기반클래스 x) {}
```
위처럼 순서를 적어야 파생클래스는 파생클래스쪽의 예외처리방법을 따를 수 있음  

2) 생성자에서 예외를 던지면?
생성자는 완벽하게 생성과정을 마치고 생성자를 끝내지 않으면 객체가 생성되지 않았다고 판단하고 소멸자도 호출하지 않음  
그러니 이런 경우 생성자 안에서 예외를 던지는 경우 catch문으로잘 처리해줘야 함  
예를들면 생성자안에서 동적할당을 사용한다면 catch문에서 할당해제를 해줘야 함  

## 17. stringstream

```C++
string buffer; // 분리된 문자열을 넣는 버퍼

string str1 = "This is Test";
istringstream ss1(str1);
while (getline(ss1, buffer, ' ')) { // 띄어쓰기를 기준으로 분리
	cout << buffer << endl;
}

string str2 = "AB/CDE/DFDG/ASCD";
istringstream ss2(str2);
while (getline(ss2, buffer, '/')) { // '/'을 기준으로 분리
	cout << buffer << endl;
}
```

```C++
string name;
int birth;
bool on;

string temp = "mike 1105 1";
stringstream ss;
ss.str(temp);
ss >> name >> birth >> on;

cout << name << endl << birth << endl << on;

//////////////////////////////////////////

// 갯수가 정해져있다면 이런식으로도 가능
istringstream ss(input);
string split[3]; 
for (int i = 0; i < 3; i++) {  
	getline(ss, split[i], '/'); 
	cout << split[i];
}
```

```C++
// 입력 형태가 무조건 고정되어 있다면
// 예를들어 10 + 20 = 30 처럼 "숫자 연산기호 숫자 등호 결과" 순으로 고정이고
// 띄어쓰기마저 완전히 항상 고정이라면 아래와 같이 사용 가능

stringstream ss;
ss.str(s);
ss >> a >> op >> b >> eq >> c;
// 위처럼 아예 고정해버리고 시작하면 쉽다.
```

stringstream 사용시 주의점  
stringstream으로 문자열에서 특정 문자를 기준으로 자르게 처리할때  
```C++
while(getline(ss, part, 'x')) {
	answer.push_back(part);
}
```
위처럼 처리할 수가 있는데   
문제는 ss가 "axxbxx"같은 형태라면 "a", "", "b", "" 형태로 ""도 결과에 들어가게 됨  
그러므로 ""는 무시하게 만들어줘야 할 필요가 있음  
```C++
    while(getline(ss, part, 'x')) {
        if(part != "") answer.push_back(part);
    }
```


## 18. 재귀

개인적으로 가장 못하고 두려워하는 분야  

★ 재귀는 매개변수와 리턴문이 필요하다  

dfs방식 중에 가기전에 확인하고 가는 방식과, 일단 가고 안되면 돌아오는 방식 두가지가 있는데  
이 중 개인적으로 가기 전에 확인하는 방식만 주로 써왔음  
이 방법으로 하면 코드가 훨씬 지저분해짐, 둘 다 더 쉬운 상황이 있으니 익혀야 함 

완전탐색/백트래킹/재귀 자체가 정말 천재가 아니고서야 경험이 꽤 쌓여야 접근 방식이라도 보일것같음  
일단 대략적으로 느껴지는건 처음것 선택한 후 두번째것 선택하고 세번째것 선택하는데 세번째것 선택에서 조건과 맞지 않으면 두번째것 선택을 바꿔야 하므로 되돌아가야함. 이런 경우가 발생한다면 재귀를 사용한다라고 떠올려야 할듯  

## 19. string에 algorithm 함수들 사용

string에도 push_back(), pop_back(), back()으로 마지막글자 가져오기 등 다 된다  

항상 쓸때마다 ``string = string + 'a'``라던가 - 'a'로 썼었고  
``string[len - 1]`` 이런식으로 썼었는데 활용하면 더 깔끔하고 빠르게 가능할 듯하다  

## 20. 부동소수점과 고정소수점 및 정밀도

>1.1 + 1.1 != 2.2 ?? 

#### 부동소수점
부동소수점 자료형 사용 시 값이 부정확해질 수 있는 경우가 있다.  

만약 실수 123.456789가 있다고 할때 컴퓨터는 이 숫자를 부동소수점으로 저장시  
1.23456789 * 10^2로 취급해서 1.23456789와 2 이렇게 두개의 데이터만 저장한다.  
즉 숫자가 들어갈 칸이 123456789 2 이렇게 총 10칸이 필요하게 되는 셈  

하지만 만약 데이터 저장 공간이 10칸이 아니라 7칸만 사용 가능한 부동소수점 자료형이라면?  
예를들어 float은 7개의 자릿수만큼의 정밀도를 가지고, double은 15자리수, long double은 33자리수를  
cout은 6자리를 정밀도 기본값으로 사용한다.(setprecision으로 조정 가능)  

123456789 2 를 저장할 공간이 부족하게 되므로 실수부분을 6칸으로 만들기 위해  
7번째자리에서 반올림한 후 1.23457 * 10^2처럼 처리하게 되고 오차가 발생하게 된다.  

#### 고정소수점
반면 부동소수점이 아닌 고정 소수점의 경우 123.456789가 있으면 123과 456789를 저장한다.  
그래서 부동소수점에 비해 상대적으로 작은 값만 보관할 수 있지만 정확도는 더 높아지게 된다.  

## 21. ``#include <algorithm>``

#### 정렬
- sort : 일반적인 정렬 함수
- partial_sort : 배열의 일부분만 정렬함
- stable_sort : 정렬을 하되 원소들간의 순서를 보존함

기본적으로 sort는 불안정정렬이고, 안정정렬이 필요하면 stable_sort를 사용하는 것  
불안정 정렬은 { 2, 2, 1, 3, 4}가 들어있다면 정렬 후 {1, 2, 2, 3, 4}가 되긴 하는데 두개의 2의 순서가 바뀔수도 안바뀔 수도 있는 것  
반면 안정 정렬은 두개의 2의 순서가 유지됨  

#### 함수객체와 람다함수
1) 함수객체 형태
```C++
struct is_odd {
	bool operator()(const int& i) { return i % 2 == 1; }
}

또는

struct comp {
	return a > b;
}
```

2) 람다함수
```C++
sort(numlist.begin(), numlist.end(),  
	[n](const auto& a, const auto& b) -> bool  
	{ 
		if(abs(a-n) == abs(b-n)) return a > b;  
		else return abs(a-n) < abs(b-n); 
	}  
);  
/*
위처럼 사용할때 return a > b처럼 앞이 크게 리턴하면 큰게 앞으로 가는 내림차순 정렬
return abs(a-n) < abs(b-n);처럼 뒤가 크게 하면 절대값이 더 큰게 뒤로가는 오름차순 정렬
*/
```

3) 람다함수 비교 조건문
```C++
//람다식 pred 부분에서 길이가 짧은 문자열이 앞에 오고 싶다면  
if (a.length() == b.length()) return a < b;  
return a.length() < b.length();  
//형태로 구성해야 한다.  
//그냥 a<b 하면 안되고 a.length() < b.length()
```

#### erase와 remove
``vec.erase(remove(vec.begin(), vec.end(), 3), vec.end()); => vec에 있는 모든 3 제거``  

#### transform
컨테이너 내의 모든 값에대한 변경은 for문 돌리는것보다 transform을 먼저 떠올리자  

#### any_of / all_of
둘의 존재를 잊지 않기  

#### reverse
``reverse(vec.begin(), vec.end());``  
{1, 2, 3, 4, 5}가 들어있던 vec을 reverse하면 {5, 4, 3, 2, 1}이 된다.  

``reverse(str.begin(), str.end());``  
또한 문자열 뒤집기도 가능  

#### rotate
벡터의 값들을 한칸씩 옮기며 회전시킴  
``rotate(v.begin(), v.begin() + 1, v.end()); // 한칸씩 왼쪽 이동``  
``rotate(v.begin(), v.end() - 1, v.end()); // 한칸씩 오른쪽 이동``  
매개변수는 (시작반복자, 첫 위치로 올 반복자, 종료 반복자)순이며   
두번째 매개변수 위치에 있던 값이 첫 위치로 간다는 뜻으로  
v.begin() + 1이 첫 위치로 간다는것은 왼쪽으로 움직인다는 뜻  

#### swap
``swap(a, b);`` 혹은 ``swap(객체, 객체);  

#### lower_bound와 upper_bound
```C++
lower_bound(v.begin(), v.end(), 0) - v.begin() 
// lower_bound는 x이상인 첫번째 원소의 위치 반환  
upper_bound(v.begin(), v.end(), 0) - v.begin()
// upper_bound는 x초과인 첫번째 원소의 위치 반환
```
또한 upper_bound 결과 - lower_bound 결과로 값의 갯수를 알 수 있다.  
만약 count를 사용한다면 for문 돌리는 효과라 O(N)이지만 위의 이분탐색 함수를 사용하면 O(logN)  

#### 보이어-무어 알고리즘
문자열에서 특정 문자열을 찾을때 find를 쓰는 방법도 있지만 보이어-무어 알고리즘을 사용하면 훨씬 빠르다.  
```C++
std::string s =	"I believe I can fly I believe I can fly I believe I can fly (woo)";
std::string needle = "believe";

auto it = std::search(s.begin(), s.end(),
		std::boyer_moore_searcher(needle.begin(), needle.end()));

if (it != s.end()) {
	std::cout << needle << " at : " << std::distance(s.begin(), it) << std::endl;
} 
else {
	std::cout << needle << " not found " << std::endl;
}
```

#### 문자열 맨 앞 또는 맨 뒤의 연속된 값 지우기
bitset같은걸로 2진수 만들때 유용할 듯  
```C++ 
// 문자열에서 맨 앞의 연속된 0을 지우고 싶다면   
s.remove_prefix(std::min(s.find_first_not_of("0"), s.size())); 
// 맨 뒤는  
s.remove_suffix(std::min(s.size() - s.find_last_not_of("0") - 1, s.size()));
```

#### inner_product
`` int result = inner_product(v1.begin(), v1.end(), v2.begin(), 0);``  
v2의 size는 v1의 size보다 크거나 같아야 함  
함수의 기능은 ``v1[0] * v2[0] + v1[1] * v2[1] * ... + v1[10] * v2[10]``  
처럼 같은 위치의 값들을 곱한 후 그 값들을 다 더해주는 것  

#### accumulate
이건 사실 ``#include <algorithm>``이 아니라 ``#include <numeric>``인데 일단 여기 같이 적어둠  
```C++
#include <numeric>  
int sum = std::accumulate(v.begin(), v.end(), 0);  
// 첫번째, 두번째 인자는 어디부터 어디까지  
// 3번째 인자는 sum값의 초기값  
// 만약 sum이 double이면 0.0으로 적어주면 됨
```

#### string vector의 accumulate
```C++
vector<string> v{"abc", "de", "f", "ghijk"};
string answer = accumulate(v.begin(), v.end(), string{});
```
위처럼 사용하면 answer == "abcdefghijk"가 된다.  
정수나 실수뿐 아니라 string객체도 이어붙일 수 있음

## 22. Thread
  
#### 쓰레드와 프로세스의 차이  
프로세스들은 메모리를 공유하지 않음  
프로세스1은 프로세스2의 메모리에 접근 불가능하고 프로세스2는 프로세스1의 메모리에 접근 불가능   
하지만 한 프로세스 안에 있는 여러 쓰레드는 같은 메모리를 공유하고 스택만 별도로 가짐  
쓰레드1과 쓰레드2가 같은 변수에 접근할 수 있음  
  
현대 기술의 발전으로 cpu는 코어가 한개가 아니고 여러개이며  
멀티코어 cpu에서는 각 코어에서 각기 다른 쓰레드들이 동시에 실행되고 있음  
코어가 늘어나면서 멀티쓰레드로 프로그램을 만드는게 유리해짐  
  
멀티쓰레드로 만들기 유리한 프로그램은?  
1) 병렬 가능한 작업들  
1~10000까지 더하는 작업을 단일 쓰레드 프로그램으로 짠다면 그냥 for문으로 1~10000까지 더하면 됨  
하지만 이걸 10개의 쓰레드로 만든다면  1번쓰레드는 1~1000까지, 2번 쓰레드는 1001~2000까지... 하고나서  
다 합치면  실행시간이 거의 10분의 1 수준이 될 수 잇음  

2) 대기시간이 긴 작업들  
인터넷에 잇는 자료를 스크랩 한다면 인터넷 속도가 cpu속도보다 느리므로 응답을 기다리기만 하는 시간이 생김  
이런 경우 여러개의 쓰레드로 요청하고 응답이 오기까지 또 다른 쓰레드가 요청하고 또 다른 쓰레드가 요청하고  
이러다가 응답이 오면 다운받고, 또 다운받고 하면 대기시간에도 다른 일을 할 수 있음  
    
하지만 모든 프로그램을 멀티쓰레드로 짠다고 유리한게 아님  
1) 애초에 멀티쓰레드에 부적합한 프로그램
피보나치 수열의 경우 n번째를 구하기 위해선 n-1과 n-2를 알아야함. 그리고 n+1을 구하기 위해선 n과 n-1을 알아야하고  
그러니 다음 연산을 하려면 이전 연산의 결과가 필요한 이런 경우 병렬화가 매우 어려움  

2) 임계영역 접근이 너무 많아서 병목현상이 발생하는 경우

#### 쓰레드 생성법  
C++ 11 이전에는 표준이 없어서 리눅스는 pthread_create로, 윈도우는 CreateThread로 만드는 등 복잡햇지만  
C++ 11 이후로는 표준이 나와서 쉽게 사용 가능  
``` C++
#include <thread> // 헤더파일  
thread t1(func1); // 객체 생성  
// t1객체는 인자로 받은 func1을 새로운 쓰레드에서 실행함  
thread t2(func2); // 이런식으로 여러 쓰레드를 만들어서 동시 실행 가능  
// 단 스케쥴러 마음대로 조정되는거라 완전 동시는 아님  
t1.join(); // join은 해당 쓰레드가 종료되면 리턴하는 함수  
// 즉 t1.join()은 t1이 종료될때까지 리턴하지 않음  
// 쓰는 이유는 쓰레드가 종료되기도 전에 메인함수가 종료되서 쓰레드 객체들이 소멸하는것을 막기 위해  
// join은 쓰레드가 종료되어야만 리턴하므로, t1.join()해두면 쓰레드가 종료될때까지 리턴되지 않고, 즉 코드 다음줄들은 실행되지 않고 대기중인 셈이 되는 것  
t1.detach(); // detach는 쓰레드 실행시킨 후 잊어버린다라는 개념. 메인함수가 종료되어도 백그라운드에서 쓰레드는 계속 작동함  
// join은 결과를 꼭 확인해야할 때, detach는 결과를 눈으로 보는게 아니라 실행시키는것이 중요할 때 쓰는 듯

// detach는 메인함수와 상관없이 t1의 일이 끝나면 쓰레드가 소멸함  
```  

쓰레드가 cout을 하게 되면 쓰레드 이것저것이 계속 스위칭되면서 출력해서 문장이 제대로 출력이 안됨  
이유는 cout << a << b;를 하게 되면 cout << a까지 잘 하고나서 << b를 수행하기 전 그 틈에 interrupt가 들어올 수 있어서  
반면 printf는 하나의 함수단위로 작동해서 컨텍스트스위칭이 있던 말던 한번에 출력 다 되게 만들기 때문에 쓰레드가 값을 출력할땐 printf를 써주면 좋을 듯

## 짤짤이들

- 파일입출력에서 출력으로 CSV파일형태로 하면 엑셀에서 열 수 있다. 필요할때 공부해보기

- 우선순위큐는 Heap으로 구현되어있다.
- set은 이진탐색트리로 구현되어있다.
- map은 과거에는 이진탐색트리로, 현재에는 레드블랙트리로 구현되어있다.
- sort는 퀵정렬 기반으로 구현되어 있다.

- precision이 정밀도라는 뜻이다. 그래서 소수점 몇자리까지 나올지 정할때 썼나봄

- 오름차순 정렬되어 있을 경우 find보다 lower_bound를 쓰는게 더 빠르다  
- 정렬된 벡터에서 값이 있는지 없는지 bool값만 확인하면 된다면 binary_search()를 쓰면 좋다  
단 꼭 정렬되어있는 경우에만 사용  
  
- static과 extern은 저절로 0으로 초기화된다.
- const와 레퍼런스는 초기화가 필요하다

- 나눗셈은 특별하다. 나눗셈 누를때마다 항상 이 생각이 저절로 들어야한다  
1) 0으로 나누고 있진 않은가  
2) 나눈 결과가 실수라서 데이터소실이 있지는 않은가  
항상 생각해야한다

- 0은 int형 0으로 취급, 0LL은 long long형 0으로 취급

- 소수 판별할땐 N - 1까지 확인이 아니라 sqrt(N)까지만 확인하면 된다.
```C++
bool is_primenumber(int n)
{
	if (n < 2) return false;

	for (int i = 2; i <= sqrt(n); i++) {
		if (n % i == 0) return false;
	}
	return true;
}
```

- isdigit 사용할때 매번 이상했던 점 해결  
``if(isdigit(*part.c_str()))``
isdigit은 인자로 char변수를 받아야 함 , 즉 문자 한개만 받아서 그게 숫자인지 아닌지 판별함  
근데 안에 string을 넣으니 일단 문제가 계속 생겼었고  해결법으로 c_str()로 string to char을 하려고 했지만 c_str()의 결과물은 char가 아니라 `char*`였음  
그러니 char포인터 안에 있는 값인 char값을 빼오려면 `*변수.c_str()`을 해야 했음

- 곱하기 2나 나누기 2를 여러번 반복해야 하는 경우 쉬프트연산을 떠올리기
10 << 1; 하면 그냥 20이 되고  
10 << 2; 하면 10 * 2 를 2번해서 40이 된다  
10 >> 1; 은 5가 되고  
10 >> 2; 는 2.5가 되는 것  

- ``cout << string(10, '*');``하면 * 10개 출력 가능

* range based for는   
기본적으로  
```C++
for(int v : vec) {  
	cout << v;  
}
```
이런 형태겠지만 vec을 v로 복사할 필요 없이 참조만 하는 경우는  
`for(const int& v : vec)` 처럼 쓰는게 더 좋다  


문장의 문자들을 모두 소문자로 바꾸고 싶다면
transform(my_string.begin(), my_string.end(), my_string.begin(), ::tolower);


min_element / max_element는 만약 vector에 가장 작은/큰 값이 여러개라면 가장 왼쪽의 itr을 출력해줌


cout << fixed; cout.precision();은 자동으로 반올림을 한다.
예를들어 
```C++
double d = 1234.56789
cout << fixed;
cout.precision(3);
cout << d; // 1234.568
```


우선순위큐는 heap으로 구현
set은 이분탐색트리로 구현
map은 레드블랙트리로 구현

heap은 완전이진트리를 이용해서 구현함
근데 완전이진트리는 array로 구현
그러므로 heap도 구현시에는 array를 사용하는 듯
그럼 우선순위큐도 마찬가지로 array를 사용하게 될듯(혹은 vector)



set과 map에  
algorithm header의 lower_bound와 upper_bound를 사용하면 O(N)으로 비효율적이다  
set의 iterator는 양방향itr이기때문  
그래서 set과 map에는 멤버함수로 lower_bound, upper_bound함수가 따로있다.  
  
그래서 특정값이상을 찾아 erase가 잦은 경우 단지 erase가 잦다는 이유로 list를 택할 수 없다.  
list에서 제공하는 lower_bound는 O(N)이고 erase가 O(1)인것을 감안해도   
set/multiset에 넣고 lower_bound하고 erase하는게 더 효율적일 수 있다.  
  
관련문제 : [https://www.acmicpc.net/problem/1202](https://www.acmicpc.net/problem/1202)

정답코드)
```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
	ios::sync_with_stdio(false); cin.tie(NULL);

	int N, K;
	cin >> N >> K;

	vector<pair<int, int>> pq(N);
	int M, V;
	for (int i = 0; i < N; i++) {
		cin >> M >> V;
		pq[i] = { V, M };
	}
	sort(pq.begin(), pq.end(),
		[&](auto a, auto b) {
			if (a.first == b.first) return a.second < b.second;
			else return a.first > b.first;
		}
	);

	multiset<int> C;
	int input;
	for (int i = 0; i < K; i++) {
		cin >> input;
		C.insert(input);
	}

	long long answer = 0;
	int index = 0;
	while (index < N && C.size()) {
		int value = pq[index].first;
		int weight = pq[index].second;
		index++;

		auto itr = C.lower_bound(weight);
		if (itr != C.end()) {
			answer += value;
			C.erase(itr);
		}		
	}

	cout << answer;
	
	return 0;
}
```



#### vector간의 비교연산자
vector간의 비교는 보통 두 벡터가 같은지 확인하는
```C++
if(v1 == v2) ;
```
위와 같은 형태가 종종 보이지만 그 뿐 아니라 <, >, <=, >= 같은 비교연산자도 사용 가능하다  
```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
	ios::sync_with_stdio(false); cin.tie(NULL);

	vector<int> v1{ 1, 2, 3 };
	vector<int> v2{ 1, 2, 3 };
	vector<int> v3{ 1, 2, 3, 4 };
	vector<int> v4{ 1, 2, 4, 5 };

	cout << boolalpha;
	cout << (v1 == v2) << '\n';
	cout << (v2 < v3) << '\n';
	cout << (v3 < v4) << '\n';

	return 0;
}
```
위처럼 비교연산자로 vector를 비교하면  
vector의 size가 더 큰 쪽이 더 크게 판정되며,  
size가 같다면 index 0부터 비교하면서 처음으로 숫자가 달라지는 index의 값 중 더 큰쪽의 vector가 더 큰것으로 판정한다.  
마치 {1, 2, 3, 4}와 {1, 2, 4, 5} 두개의 벡터의 값들을 문자열로 붙인것마냥 1234와 1245의 비교로 1245가 더 크다고 판정하는것처럼 받아들이면 됨  
![](https://blog.kakaocdn.net/dn/JLxTi/btsGNZzBEtW/O8X3gCIkio6usldCJMimD1/img.png)


#### string rfind
string에서 find로 글자를 찾으면 왼쪽부터 비교해서 처음 발견된 인덱스를 반환해줌  
반면 rfind로 찾으면 오른쪽부터 비교해서 처음 발견된 인덱스를 반환해줌  
```C++
#include <iostream>
using namespace std;

int main() {
	string str = "abcdea";
	cout << str.find('a') << ' ' << str.rfind('a');
}
```

결과)  
![](https://blog.kakaocdn.net/dn/9bokk/btsHbCYqzZ4/p2erKGK2F1I3o865yEo0l1/img.png)
그래서 특정 문자로 끝나게 자르고 싶다면 뒤에서부터 위치를 찾아서 인덱스를 얻어서 substr하는 방법이 있음



#### 인터프리터 해석 과정
#### 1) 렉싱(Lexing)
- 표현식을 해석하는 첫 단계
- 입력된 문자열을 토큰이라 불리는 단위로 나누어 나열함
  - 토큰 : 문법상에서 의미를 갖는 최소단위의 분류

예를들어 3 + (5 - 4) 같은 경우는 토큰이 정수, 연산자, 괄호 세가지 경우가 있다.  
토큰은 값이 아닌 분류이므로 3, 4, 5는 모두 하나의 정수라는 토큰 내에 속한다.  
- 렉싱할때 for문으로 글자 하나씩 확인하면서 + - ( ) 라면 특정 기능을 하게 하는것은 쉽지만
숫자의 경우 여러 자릿수가 붙어있을 수도 있고 음수라면 -로 시작할 수도 있는 등 분리하기 쉽지 않다.

#### 2) 파싱(Parsing)
- 토큰의 나열을 의미있는 객체지향 데이터구조로 바꾸는 것


#### 디자인패턴 단어들
component : 구성요소
composite : 복합체
decorate : 꾸미다. 장식하다.
ingredient : 재료
facade : 겉모습
proxy : 대리인  
flyweight : 경량화하다
implement : 구현하다
Interpreter : 해석자
mediator : 중재자


chrono의 3가지 요소
1) 현재 시간을 알려주는 clock
2) 특정 시점을 알려주는 time_point
3) 시간의 간격을 나타내는 duration

프록시 : Proxy를 통해서만 접근 가능, 
ProxyA는 A와 동일한 인터페이스를 제공해야하며
ProxyA가 A의 속성값을 변경시켜선 안됌
게으른 초기화 방식을 사용함

플라이웨이트 : 플라이웨이트 Factory와 map을 사용
싱글톤의 구조패턴버전

데코레이터 : 빌더패턴 기능이 있는 어댑터와 유사한 모양새
어댑터는 기능변환없이 연결만 잘 되게 한다면 데코레이터는 추가기능이 있음

메멘토 : 원본객체, 메멘토, caretaker 3개로 구성되며 caretaker가 메멘토를 관리하는 역할
메멘토는 하나의 commit과 같고 caretaker가 하나의 git이라고 봐야 함
순간의 상태를 저장하고 나중에 복원도 할 수 있어야 함

옵저버는 커플링이 생긴다. 그래도 느슨한 결합이라 옵저버를 새로 추가한다고 해서 subject를 변경할 필요는 없다.
옵저버는 동기적이라 모든 옵저버 호출/return이 다 완료되기 전까지 다음 작업 진행 불가능하므로 멀티쓰레드 작업이 필요할 수 있다.

스테이트나 스트레티지는 상태나 알고리즘 객체를 멤버변수로 갖고 해당 멤버변수의 함수를 호출하는 방식으로 구현

비지터패턴은 컴포지트 패턴같은 계층적 구조에서 사용한다고 하는데
이게 컴파짓에서 쓰는 이유를 이해 못해서 그런지 좀 읽어봐도 차라리 어댑터 쓰는게 간단하지 않나 싶다.
객체의 성격과 맞지 않는 함수를 visitor가 대신 해주는건데
사실상 그냥 성격과 맞지 않는 함수를 작성할 자리에 accept를 작성하고 
accept 안에 내용을 다른 객체가 해당 역할을 하게 하는거라 구현은 쉬운데 코드 양만 늘리는 꼴인것 같기도 하고...
그래도 코드 양 늘려서 단일책임원칙 준수하는게 목적인듯 하다.


unique_ptr은 스마트포인터와 소유권의 개념을 합쳐서 만든 것
vector의 capacity가 크면 shrink_to_fit 혹은 swap으로 해결


좌측값과 우측값의 구분 기준은 주소값을 취할수 있냐 없냐
좌측값은 좌측 우측 다 갈수 있음, 우측값은 우측만갈 수 있음


이동생성자는 복사생성자처럼 클래스 구현할때 직접 만들어 주는 것이지 저절로 생기는게 아니다.  
만약 `MyString str1 = str2 + str3;`로 쓰면 str2와 str3를 붙인 MyString임시객체를 str1으로 복사생성자 이용해서 객체 생성하는 것이고  
`MyString&& str1 = str2 + str3;`로 쓰면 str2와 str3를 붙인 MyString임시객체를 이동생성자를 이용해서 이동시키게 된다.  

move함수는 좌측값을 우측값으로 형변환시켜주는 함수
```C++
template <typename T>
void my_swap(T &a, T &b) {
  T tmp(a);
  a = b;
  b = tmp;
}
```
위같은 상황에서 a, b가 아주 크면 안좋으니까 이럴때 좌측값을 우측값마냥 만들고, operator=도 구현해서  
```C++
template <typename T>
void my_swap(T &a, T &b) {
  T tmp(std::move(a)); // 이동생성자 사용되게 하고
  a = std::move(b);    // operator=로 이동생성자와 같은 기능하게 구현하는 것
  b = std::move(tmp);
}
```

`foward<T>(a)`는 a가 우측값레퍼런스일때만 move와 같은 기능을 해주는 함수

stack unwinding : 예외발생시 맞는 catch문을 찾을때까지 계속 함수스택을 거슬러 올라가는 것

싱글턴도 복사생성자 operator=등등 private로 놓는게 아니라 = delete로 써도 되지 않을까?

C++에서 메모리를 잘못된 방식으로 관리하면 두가지 문제점이 생길 수 있음
1) 메모리 릭(이건 RAII패턴을 사용해서 해결 가능)
2) 이미 해제된 메모리를 다시 참조하는 경우

RAII패턴
자원의 관리를 스택에 할당한 객체를 이용해서 수행하는 패턴이다.
프로그램 진행 중 만약 예외가 발생해서 함수를 빠져나가도 스택에 있는 객체들은 모두 소멸자가 호출된다. 
그냥 자연스레 사라지는게 아니라 소멸자가 호출되는 방식이다.
반면 힙에 있는 객체들은 예외발생시 정상적으로 소멸자가 호출되지 않아서 메모리릭이 일어나게 된다.
그래서 힙에 있는 데이터들을 스택에 있는 객체가 관리하게 만들어서 메모리릭을 방지하는 방법론이다.

스마트포인터
RAII패턴을 적용하기 위해 만든 포인터객체
스마트포인터를 이용해서 힙의 데이터를 스택에 있는 객체가 관리하게 한다고 해도 double free문제가 남아있음
double free문제를 해결하기 위한것이 소유권 개념

shared_ptr은 같은 객체를 소유한다보다는 같은 객체를 가리킨다라고 이해하는편이 좋은 듯
weak_ptr은 참조개수를 증가시키지 않는다는게 큰 차이점

HTTP는 상태정보를 저장하지 않고 연결상태도 처리하지 않고 요청 오면 응답 보내고 연결 끊음
매번 연결을 위한 인증이 필요한게 불편하니 쿠키나 세션을 사용
쿠키 세션은 사용자 인증을 돕는 수단, 캐시는 웹페이지 요소를 저장하기 위한 임시저장수단