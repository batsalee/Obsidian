# chrono 라이브러리


## 1. chrono 라이브러리

C++ 11부터 추가된 시간 관련 데이터를 다룰 수 있게 도와주는 라이브러리  
chrono`[크로노]`는 '때', '시기' 같은 뜻으로 일반적인 시간을 의미하는 단어  

chrono 라이브러리를 편하게 사용하려면 `using namespace chrono;`를 사용하면 모든 문구마다 `std::chrono::`를 붙이지 않아도 됨  

```C++
#include <chrono>
using namespace chrono;
```

크게 3가지 요소로 구성됨  
1) 시간의 간격을 나타내는 duration
2) 특정 시점을 나타내는 time_point
3) 현재 시간을 알려주는 시계 clock


## 2. duration

chrono 라이브러리에서 duration은 시간 간격을 나타내는 용도  
A시점의 시간과 B시점의 시간 사이의 간격이 몇초인지 알아야 할 때 사용한다.  

틱이 발생하는 횟수로 사용할 수도 있음(예를들어 10초는 1초단위의 틱 10틱동안의 기간을 의미)  
또는 보기 쉬운 초 단위로 캐스팅해서 사용도 가능하다.  
```C++
/* start부터 end까지 실제 걸리는 시간을 보기 좋게 표현하고 싶다거나 할때 duration을 활용 */

/* Alias */
using nanoseconds  = duration<long long, nano>;
using microseconds = duration<long long, micor>;
using milliseconds = duration<long long, milli>;
using seconds      = duration<long long>;
using minutes      = duration<long long, ratio<60>>;
using hours        = duration<long long, ratio<3600>>;

/* 
즉 복잡하게 std::chrono::duration<long long, ration<3600>>처럼 쓸 필요없이
std::chrono::hours로 쓸 수 있음
만약 using namespace chrono;를 선언한다면 그냥 hours로 써도 됨
*/
std::chrono::nanoseconds	// 10^9분의 1초이며 단위는 ns
std::chrono::microseconds 	// 10^6분의 1초이며 단위는 us
std::chrono::milliseconds	// 10^3분의 1초이며 단위는 ms
std::chrono::seconds		// 1초
std::chrono::minutes		// 1분
std::chrono::hours			// 1시간
```
  
duration을 활용해서 시간간격 표현하는 방법
```C++
/*
duration을 .count()로 출력하면 틱이 몇회 발생했는지 확인 할 수 있고
duration_cast을 사용하면 durtion을 원하는 형식으로 캐스팅해서 사용 가능
*/

std::chrono::duration diff = end - start;

std::cout << "몇 번의 틱이 발생했는지 : " << diff.count() << '\n'
        << "실제 시간이 얼마나 걸린건지 : " 
        << std::chrono::duration_cast<std::chrono::microseconds>(diff).count() 
        << "us\n";
```


## 3. time_point

chrono 라이브러리에서 time_point는 특정 시점을 표현  
예를들어 2023년 07월 04일 같은 특정한 시간을 표현  

chrono 라이브러리에서는 유닉스타임(혹은 에포크타임)이라고 불리는 1970년 1월 1일을 기준으로  
특정 기간(duration)이 지난 시점을 time_point로 정의한다.  
```C++
#include <chrono>
#include <iostream>

int main()
{
    std::chrono::time_point tp = std::chrono::system_clock::now(); // 현재 시간 리턴

    // epoch 시간 이후 현재 까지 시간 단위 경과 시간
    std::chrono::hours h = std::chrono::duration_cast<std::chrono::hours>(tp.time_since_epoch());
    std::cout << h.count() << std::endl;

    // epoch 시간 이후 현재 까지 분 단위 경과 시간
    std::chrono::minutes m = std::chrono::duration_cast<std::chrono::minutes>(tp.time_since_epoch());
    std::cout << m.count() << std::endl;

    return 0;
}
```


## 4. clock

1) `std::system_clock` : 일반적인 상황에서 현재 컴퓨터의 시간을 얻어오고자 할 때(1초에 1tick)  
2) `std::high_resolution_clock` : 좀 더 정밀한 시간계산을 원할 때(0.00000001초마다 1tick)  
3) `std::steady_clock` : 수정되지 않는 단조 증가 시계가 필요할 때(시스템 시간을 되돌려도 계속 증가하는 시계)

`std::system_clock`은 정확히 몇시 몇분 이런식으로 알려주는게 아니라 유닉스타임(1970년 1월 1일)부터 현재까지 발생한 틱의 횟수를 time_point객체로 리턴해줌  
즉 time_point 객체에 clock의 시작점인 유닉스타임과 현재시간의 duration을 보관하는 것

`std::high_resolution_clock`은 더 정밀한 시간 계산을 원할 때 사용  
예를들어 프로그램 성능을 측정하고 싶을 때 시작전에 time_point를 기록하고 끝난 후 time_point를 기록해서 duration을 확인하는 식으로 사용 가능  

사용 예시)
```C++
#include <iostream>
#include <chrono>

int main() 
{
	std::chrono::time_point<std::chrono::high_resolution_clock> start =
		std::chrono::high_resolution_clock::now(); // 시작 전의 timestamp 찍어두기

	for (int i = 0; i < 100000; i++) {		
	}

	std::chrono::time_point<std::chrono::high_resolution_clock> end =
		std::chrono::high_resolution_clock::now(); // 시작 후의 timestamp 찍어두기
		
	//auto diff = end - start; // C++ 17 이전	
	std::chrono::duration diff = end - start; // C++ 17 이후

	std::cout << "몇 번의 틱이 발생했는지 : " << diff.count() << '\n'
		<< "실제 시간이 얼마나 걸린건지 : " << std::chrono::duration_cast<std::chrono::microseconds>(diff).count() << "us\n";

	return 0;
}
```


## 5. 다양한 활용 방법들

#### 1) 문자열로부터 time_point 구하기
문자열로부터 time_point를 만들기 위해서는 from_stream 함수를 이용
```C++
#include <iostream>
#include <chrono>
using namespace std::chrono;

int main()
{
	std::istringstream ss{ "2023-07-04 23:44:03" };

	std::chrono::time_point<system_clock, seconds> stp; // seconds를 다른 duration으로 변경 가능
	std::chrono::from_stream(ss, "%F %T", stp);
	std::cout << stp << '\n';
}
```

#### 2) 현재 시간 구하기
C++20 이전에는 chrono 라이브러리로만 깔끔하게 현재 몇년도 몇월 몇일 식으로 표기하는 방법은 없고 C언어의 함수들과 함께 사용해야 한다고 함  
```C++
#pragma warning(disable : 4996)

#include <iostream>
#include <chrono>
#include <ctime>

int main() {
    auto now = std::chrono::system_clock::now();
    std::time_t t = std::chrono::system_clock::to_time_t(now);

    std::cout << "현재 시간 : " << std::put_time(localtime(&t), "%F %T %z") << '\n';
}
```

C++20 이후에는 아래처럼 사용 가능
```C++
#include <iostream>
#include <chrono>

int main()
{
    // system_clock으로 현재 UTC 얻기(Universal Time Coordinated)
    const std::chrono::system_clock::time_point utc = std::chrono::system_clock::now();
    std::cout << "현재 UTC : " << utc << '\n'; // 2023-07-04 13:58:44.7184726
    
    // system_clock으로 Local Time 얻기    
    const std::chrono::local_time<std::chrono::system_clock::duration> local_time =
        std::chrono::zoned_time{ std::chrono::current_zone(), std::chrono::system_clock::now() }.get_local_time();    
    std::cout << "현재 Local time : " << local_time << '\n'; // 2023-07-04 23:02:02.6299804
    
    return 0;
}
```

#### 3) 유닉스 타임(에포크 타임)으로부터 경과 틱 구하기

==UTC인 경우== time_point를 time_t로 변경하는 to_time_t 함수  
반대로 time_t로부터 time_point로 변경하는 from_time_t함수를 사용
```C++
using namespace std::chrono;

std::cout << "original time_t : " << time(nullptr) << std::endl;

const system_clock::time_point utc_now = system_clock::now();

std::time_t t = std::chrono::system_clock::to_time_t(utc_now);
std::cout << "chrono time_t : " << t << std::endl;

system_clock::time_point tp = std::chrono::system_clock::from_time_t(t);
std::cout << "reverse : " << tp << std::endl;
```

==local_time의 경우== 로컬타임은 to_time_t를 사용할 수 없고 대신 time_since_epoch 함수를 사용  
```C++
using namespace std::chrono;

const system_clock::time_point utc_now = system_clock::now();
std::cout << "UTC : " << floor<seconds>(utc_now.time_since_epoch()).count() << std::endl;

const local_time<system_clock::duration> local_now = zoned_time{ current_zone(), system_clock::now() }.get_local_time();
std::cout << "Local : " << floor<seconds>(utc_now.time_since_epoch()).count() << std::endl;
// floor<seconds>로 초단위까지만 남기고 내림
```

