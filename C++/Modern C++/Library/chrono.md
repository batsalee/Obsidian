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

