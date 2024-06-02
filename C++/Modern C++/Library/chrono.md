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

