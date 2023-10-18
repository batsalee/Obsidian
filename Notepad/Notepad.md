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

### atoi
첫 문자부터 확인해서 숫자가 아닌게 나올때까지 읽어서 반환해줌
첫 문자가 숫자가 아닌 경우 0으로 변환 됨
``` C++
cout << atoi("abc"); // 0
cout << atoi("123"); // 123
cout << atoi("123abc"); // 123
cout << atoi("abc123"); // 0
```

### stoi
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
dynamic_cast : 파생 클래스 사이에서의 다운 캐스팅  
reinterpret_cast : 위험을 감수하고 하는 캐스팅, 예를들어 서로 관련이 없는 포인터들 사이의 캐스팅 등  

``` C++
(원하는 캐스팅 종류)<바꾸려는 타입>(무엇을 바꿀 것인가?)  
static_cast<int>(float변수); //float을 int로 형변환
```


## 짤짤이들
기반클래스* ap = &파생클래스객체; 처럼 선언할때 ap.func()를 하면 기반클래스의 함수가 사용되는데 그 이유는 기반클래스에는 파생클래스의 정보가 없으므로 파생클래스가 애초에 존재하는지, 또 그 파생클래스에 func()함수가 있는지 모르기 때문이라고 함



