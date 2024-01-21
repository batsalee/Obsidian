# 예외처리

## 1. exception 클래스

- c++가 제공하는 클래스로써 여러 예외클래스의 기초클래스로 사용할 수 있음
- `#include <stdexcept>` 헤더파일로 사용
- what()이라는 가상멤버함수 제공
- 즉 예외클래스를 직접 만든다면 파생클래스의 what()이라는 함수에 원하는 문자열을 출력하도록 재정의 하면 됨

## 2. 표준 예외 클래스

1) logic\_error
- 일반적인 논리에 대한 오류처리
2) runtime\_error
- 프로그램 실행 중 발생하는 다양한 오류처리

## 3. 예외의 종류

| 예외클래스  | 언제 던져지는가  | 해당 예외의 헤더파일  |
| --- | --- | --- |
| bad\_alloc | `new/new[]`연산자로 동적메모리 할당에 실패하는 경우</br>예를들어 컴퓨터에 메모리 자원이 부족한 경우 | new |
| bad\_cast | dynamic\_cast연산에서 캐스팅이 실패하는 경우</br>예를들어 부모객체를 자식클래스에 캐스팅 시도하는 경우 | typeinfo |
| bad\_typeid | typeid연산자가 null포인터를 반환하는 경우</br>예를들어 typeid(특정nullptr변수).name()처럼 사용하는 경우 | typeinfo |
| bad\_exception | `void func() throw (int, char) {}` 처럼 작성된 함수에서</br>int나 char가 아닌 string예외가 발생하는 경우 | exeption |
| length\_error | 최대 허용 길이를 초과하는 객체를 만들려 할 때</br>예를 들어 `vector<int> vec;`을 만들고 `my_vector.resize(vec.max_size()+1);` 처럼 사용하는 경우 | stdexcept |
| domain\_error | 수학적인 도메인 에러를 탐지할때(결과가 존재하지 않는 인자)</br>예를들어 루트 안에 음수값이 들어간 경우 | stdexcept |
| out\_of\_range | 배열 범위 검사에서 인자가 index 범위를 초과할때</br>예를들어 string str = "abc";를 만들고 str.at(10);처럼 사용 시 at멤버가 던짐 | stdexcept |
| invalid\_argument | 함수 인자의 값이 유효하지 않을때</br>예를 들어 char 자료형은 0~255를 보관할 수 있는데   여기에 256을 넣으려고 할때 | stdexcept |
| overflow\_error | 산술 계산에서 상한값을 초과할 때</br>예를 들어 아주아주 작은 값으로 나눌 때 | stdexcept |
| underflow\_error | 산술 계산에서 하한값을 초과할 때</br>예를들어 아주아주 큰 값으로 나눌 때 | stdexcept |
| range\_error | 계산에서 부동 소수점 값이 너무 크거나 작아서 표현할 수 없을 때</br>표준 라이브러리의 수학함수들은 이 예외를 발생시키지 않음 | stdexcept |
| bad\_function\_call | 함수 포인터를 이용해 함수를 호출했는데 해당 포인터값이 nullptr인 경우 | stdexcept |
| bad\_weak\_ptr | 만료된 weak\_ptr로 생성된 경우 shared\_ptr의 생성자에 의해 예외 발생</br>예를들어 `shared_ptr<int> sp(new int(10));` 처럼 만든 후 `weak_ptr<int> wp(sp);`처럼 weak\_ptr을 만들고 `wp.reset();`함수를 사용하면 weak\_ptr은 소멸되는데 이 후 wp변수를 사용하는 경우 예외 발생 | stdexcept |

## 4. typeid 연산자
자료형, 클래스, 함수, 객체, enum, 사용자정의 클래스, 멤버함수, 람다함수를 받을수 있는 연산자이며 해당 자료형이 무엇인지 알려주는 기능

`cout << typeid(int).name() << endl;` 는 int를 출력
`cout << typeid(cout).name() << endl;` 는 `class std::basic_ostream,char, struct std::char_traits<char>>`를 출력

## 5. 함수에 붙은 throw()

다른 프로그래머가 해당 함수가 어떤 예외를 던지는지 확인하기 쉽게 throw를 이용해 작성해둘 수 있음

| 함수형태 | 설명 |
|:--- |:--- |
| `void func() {}` | 일반적으로 사용해왔던 함수 형태</br>해당 함수는 어떤 예외던 던질수 있으며 또는 예외를 던지지 않을수도 있음    |
| `void func() throw(char*, int) {}` | 해당 함수는 char\*형식과 int형식의 예외를 던진다는것을 알 수 있음</br>만약 char\*이나 int가 아닌 다른 예외를 던지면 bad_exception예외가 발생  |
| `void func() throw()`</br> `void func() noexcept` | 해당 함수는 예외를 던지지 않는다는 것을 의미 |

## 6.  bad_exception 예외 처리법

bad\_exception이 발생하는 경우 지정하지 않은 예외가 발생했으므로 프로그램이 강제로 종료되는데  
이를 막기 위해 unexpected 또는 terminate를 사용해서 bad\_exeption 예외를 처리해 줄 수 있음
```C++
#include <iostream>  
#include <exception>    
  
void my_unexpected() {  
	std::cerr << "unexpected handler called\n";  
	throw; // bad_exception이 이 핸들러(함수)로 넘어온 상황인데 이걸 또 throw;해주면 또 상위함수로 던져질테니 아래의 메인함수로 던져짐  
}  
    
void func() throw (int) {  
	throw 'x'; // 예외로 던져진다고 작성된 int가 아닌 char객체가 예외로 던져짐
			   // 즉 bad_exception 발생  
}    
  
void main() {  
	std::set_unexpected(my_unexpected); // 또는 std::set_terminate(my_unexpected);
	// bad_exception이 발생할 경우 my_unexpected 핸들러가 처리하도록 등록해두는 것  
    
	try {  
		func();  
	}  
	catch (int) { std::cerr << "int 예외 발생\n; }  
	catch (std::bad_exception& be) { std::cerr << "bad_exepcion 예외 발생\n; }  
}
```

위 코드를 실행하게 되면 실행결과로 아래처럼 2줄이 출력됨
```
unexpected handler called
bad_exepcion 예외 발생
```

근데 그냥 try - catch문에서 처리하면 되는거 아닌가... 따로 만들어서 처리해야 할 상황이 있나봄