# enum, enum class

## 1. enum

enum은 내부적으로 int와 동일하게 사용되며, 값을 지정하지 않으면 가장 첫 값부터 0으로 시작해서 1씩 증가한다.  
그러므로 아래처럼 Red라고 사용하면 그냥 0과 동일하게 사용되며, WaterMelon::Black처럼 어느 enum의 값인지 명시할 수도 있다.  

#### 문제점
- 서로 다른 enum에 같은 이름을 중복해서 사용할 수 없다.
- 또한 int와 같게 처리되므로 암시적으로 실수가 발생할 여지가 있다.
	- 예를들어 서로 다른 enum의 값간에 대소비교
- 공간복잡도 최적화 상황에서 1 ~ 2바이트로도 충분한 경우의 효율문제
```C++
#include <iostream>
using namespace std;

void main() 
{
	enum WaterMelon
	{
		Red,   // 0
		Green, // 1
		Black  // 2
	};

	enum PineApple
	{
		Yellow, // 0
		// Green   // 1, 다른 enum 멤버의 이름과 중복될 수 없음
	};

	int a = Red;
	int b = WaterMelon::Black;
}
```


## 2. enum class

Modern C++ 11에 추가됐다.  
기존의 enum에 더 강한 제약을 두면서도 더 명확한 의미를 갖게 하여 enum의 문제점들을 해결했다.  
```C++
#include <iostream>
using namespace std;

void main() 
{
	enum class WaterMelon
	{
		Red,   // 0
		Green, // 1
		Black  // 2
	};

	enum class PineApple
	{
		Yellow, // 0
		Green   // 1
	};

	WaterMelon a = WaterMelon::Red;
	WaterMelon b = WaterMelon::Green;
	PineApple c = PineApple::Green;

	// int d = Red; // 오류
	// int e = WaterMelon::Red; // 오류
	int f = static_cast<int>(WaterMelon::Red);
}
```

#### enum과의 차이점
1) 클래스명을 반드시 같이 적어줘야 한다.
- Red처럼 사용 불가능하고 WaterMelon::Red처럼 명확히 사용해야 한다.

2) 서로 다른 enum class에 같은 이름의 값을 사용할 수 있다.
- WaterMelon과 PineApple 양쪽 모두 Green이 있지만 문제없다.
3) 내부적으로 int로 처리하지 않고 다른 자료형으로 처리하게 만들 수 있다.
```C++
enum class Color : char {
    RED,
    GREEN,
    BLUE
}
```

4) 먼저 선언만 해두고 나중에 정의할 수 있다.
- 선언만 해두고 나중에 정의하거나, 헤더에 선언만 해두고 cpp에 정의하는 식으로 구현 가능하다.
```C++
enum class PineApple; // 선언

enum class WaterMelon
{
	Red,   // 0
	Green, // 1
	Black  // 2
};

enum class PineApple // 정의
{
	Yellow, // 0
	Green   // 1
};
```

#### 제약이 추가된 이유
개발자가 실수할 수 있는 부분에 제약을 추가해서 버그를 방지하기 위한 목적도 있지만 사용이 더 불편해진것 또한 사실이다.  
다만 다른 언어들은 enum을 C++의 enum class와 유사하게 사용하기 때문에 언어간의 통일성 및 표준화를 위한 목적도 있다고 한다.  

#### 결론
결국 본인이 enum을 여러개 정의해야해서 변수명이 겹칠것 같거나, 실수할 여지가 있다면 enum class를 선택하면 된다.  
그냥 갑작스럽게 enum class로 정의된 코드를 만났을 때 이해만 할 수 있으면 되겠다.  