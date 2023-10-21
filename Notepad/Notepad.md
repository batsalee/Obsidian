# Today_Study 정리하기
---

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
ip.first = 2; // 이런식으로 대입도 가능
    
cout << "ip.first : " << ip.first << ", ip.second : " << ip.second << endl;
 
pair<int, double> idp(1, 2.5); // 이런식으로초기화도 가능
cout << "idp.first : " << idp.first << ", idp.second : " << idp.second << endl;
 
pair<string, int> sip;
sip = make_pair("스트링", 10); // make_pair를 사용해 대입도 가능
cout << "sip.first : " << sip.first << ", sip.second : " << sip.second << endl;
```
make_pair의 존재 기억하기

## 3. 메모리 공간
프로그램이 운영체제로부터 할당받는 메모리공간은 대표적으로 4가지  
1. 코드영역 : 실행할 프로그램의 코드  
2. 데이터영역 : 전역변수, 정적변수(extern, static)  
3. 스택영역 : 지역변수, 매개변수(컴파일시에 크기가 결정됨, 높은 주소에서 낮은 주소 순으로 할당) 
4. 힙영역 : 동적할당영역(런타임에 크기가 결정됨, 낮은 주소에서 높은 주소 순으로 할당)

## 4. 생성자
#### 기본생성자
생성자를 하나라도 정의하는 순간 디폴트생성자는 생성되지 않게 되서 다 구현해줘야 하는 불편함이 있었는데  
``` C++
class Test   
{  
public :  
Test() = default;  
};
```
이런식으로 디폴트생성자를 만들어라 라고 명시할 수 있음

#### 복사생성자
- 얕은 복사 : 값들을 복사하되, 만약 참조타입의 값이라면 주소값만 복사가 된다. 즉 원본값이 바뀌면 얕은 복사를 한 대상의 값도 바뀐다.
- 깊은 복사 : 주소값만 복사해서 같은걸 가리키는게 아니라 가리켜지는 객체 자체를 하나 더 만들어서 복사하는 것(즉, 새로 동적할당을 받고 그 공간에 원본의 데이터를 복사)

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
첫 문자가 숫자가 아닌 경우 오류가 발생함.
첫 문자가 숫자라면 숫자부터 문자가 나오기 전까지 숫자로 변환해 줌
```C++
cout << stoi("abc"); // 오류발생
cout << stoi("123"); // 123
cout << stoi("123abc"); // 123
cout << stoi("abc123"); // // 오류발생
```


## 8. 형변환(명시적/암시적)

static_cast : 흔히 생각하는 일반적인 타입 변환  
const_cast : const를 없애는 변환, const int 가 int 로  
dynamic_cast : 기반/파생클래스 사이에서의 다운캐스팅  
reinterpret_cast : 위험을 감수하고 하는 캐스팅, 예를들어 서로 관련이 없는 포인터들 사이의 캐스팅 등  

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
원래대로라면 다운캐스팅이라 문제가 될 여지가 많지만 애초에 p_p가 c를 가리키고 있고 c는 애초에 파생클래스의 객체이므로 Derived* p_c = p_p;는 사실 파생클래스에서 파생클래스로 넣는 것
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
기반클래스* ap = &파생클래스객체; 처럼 선언할때 ap.func()를 하면 기반클래스의 함수가 사용되는데 그 이유는 기반클래스에는 파생클래스의 정보가 없으므로 파생클래스가 애초에 존재하는지, 또 그 파생클래스에 func()함수가 있는지 모르기 때문이라고 함

다만 기반클래스의 func()에 virtual이 붙어있으면 파생클래스에 func()가 있는지 확인하고, 있으면 파생클래스의 func를, 없다면 기반클래스의 func를 호출하는 것. 이런 식으로 런타임중에 정해지는걸 동적바인딩이라고 함

기반클래스의 소멸자는 반드시 virtual로 선언해야 함
그렇지 않으면 ``Parent *p = new Child();`` 같은 경우 delete p;하면 부모의 소멸자만 호출가능하므로 사실은 child 객체인데 소멸이 제대로 작동되지 않게 됨

사실상 모든 함수에 virtual을 붙여도 파생클래스에 해당 함수가 없으면 기반클래스의 함수를 사용해주므로 편할 것 같지만 가상함수에는 그만큼 약간의 오버헤드가 존재하기 때문에 직접 선언하게 되어있음
다만 Java같은 경우는 이미 모든 함수가 default로 virtual로 선언됨

#### 순수가상함수와 추상클래스
- 순수가상함수
``virtual void func() = 0;``처럼 virtual과 = 0을 붙여서 반드시 오버라이딩하게 만드는 함수
가이드라인만 제공하기 위한 설계도의 개념이며 인터페이스라고도 함

- 추상클래스
순수가상함수를 하나라도 포함하고 있는, 반드시 상속되어야 하는 클래스

#### override
C++ 11에 나온 것  
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


## 13. getline
```C++
getline(cin, buf); // \n까지 받음, string라이브러리  
getline(cin, buf, '+'); // + 바로 앞글자까지 받음, 뒷 문자열 다 잘림 string라이브러리
cin.getline(buf, bufsize, '+'); // + 바로 앞글자까지 받음, iostream라이브러리
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

주의할점은 재귀적으로 사용되므로 반드시 재귀 종료를 위한 함수가 필요하고 그게 윗쪽에 선언된 print이며, 함수 작성된 순서도 당연히 윗쪽의 print가 먼저와야 오류가 발생하지 않음

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

```C
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

물론 꼭 정의되어 있는 객체만 던져야하는 것은 아니고 스스로 만든 객체를 던져도 됨
애초에 객체가 대단한 기능이 있는게 아니라 예약어에 가까워서 그냥 정해둔 이름을 쓰는것뿐이지 중요한 의미가 있지는 않음

예를들어 3칸짜리 벡터가 있는데 4번인덱스에 접근한다면
``if(index > vec.size()) throw out_of_range("예외 발생");`` 처럼 쓰는 방식이고 결국 범위를 벗어났는지 판단하는건 if문 조건이 파악하는 것이고 던지는 객체명만 out_of_range인 것
즉 ``if(out_of_range)``처럼 쓰는게 아님!!

throw하면 해당 위치에서 함수가 즉시 종료되고 뒷부분은 실행하지 않고 예외처리하는 부분으로 감
또한 함수에서 할당한 stack에 있는 객체나 값들도 모두 소멸시킴

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

만약 예외처리를 하지 않으면 예외가 발생한 순간 해당 함수는 끝내버리고 예외가 던져지므로 뒷부분의 함수 내용들이 작동하지 않음
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
		cout << spolit[i];
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
예를들어 float은 7개의 자릿수만큼의 정밀도를 가지고, double은 15자리수, long double은 33자리수, cout은 6자리를 정밀도 기본값으로 사용한다.(setprecision으로 조정 가능)

123456789 2 를 저장할 공간이 부족하게 되므로 실수부분을 6칸으로 만들기 위해 7번째자리에서 반올림한 후 1.23457 * 10^2처럼 처리하게 되고 오차가 발생하게 된다.

#### 고정소수점
반면 부동소수점이 아닌 고정 소수점의 경우 123.456789가 있으면 123과 456789를 저장한다.
그래서 부동소수점에 비해 상대적으로 작은 값만 보관할 수 있지만 정확도는 더 높아지게 된다.


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
  
- 전역변수와 정적변수는 저절로 0으로 초기화된다.