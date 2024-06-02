# random 라이브러리


## 1. random 라이브러리

C++ 11때 modern C++로 넘어오면서 추가된 난수 라이브러리  
기존의 C언어에서 난수를 생성하기 위해 사용되던 time.h나 stdlib.h의 문제점을 개선할 수 있음  


## 2. C언어 방식의 문제점

```C++
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    srand(time(NULL));
    for (int i = 0; i < 10; i++) {
        printf("%d \n", rand() % 100);
    }
    
    return 0;
}
```
#### 1) 진짜 랜덤이 아님
컴퓨터로 완벽한 랜덤 난수를 생성하는 것은 어려움  

위 코드의 방식은 진짜 난수를 생성하는 것이 아니라, 난수처럼 보이는 숫자를 생성하는 코드이며  
첫 번째 수만 랜덤으로 정한 후 나머지 숫자들은 첫 숫자를 기반으로 만들어진 수열  

무작위로 정해진 첫번째 수를 시드(seed)라고 부르며 C언어는 srand로 seed를 설정함  
위 코드에서는 `time(NULL)`을 srand의 값으로 주면서 프로그램을 실행한 시간을 기반으로 시드값을 설정함  
그 후 rand()를 호출할 때마다 seed를 기반으로 무작위처럼 보이는 수열을 생성하는 방식  

#### 2) 시드값이 느리게 변함
`time(NULL)`을 이용하면 프로그램 실행시점의 초를 기준으로 첫 난수를 설정함  
즉 동시에 여러 프로그램이 `srand(time(NULL))`을 사용하면 모두 같은 seed를 가지게 될 수 있음  

#### 3) 난수가 균등하게 생성되지 않음
위 코드로 0 ~ 99의 숫자를 생성하고 싶다면 `rand() % 100`을 해줌으로써 난수를 100으로 나눈 나머지를 얻는 방식을 사용하게 됨  

이때 `rand()`가 만드는 난수는 0부터 RAND_MAX까지인데 만약 RAND_MAX가 100의 배수가 아니라면?  
만약 RAND_MAX가 150이라면 `rand() % 100`을 했을 때 1이 나오는 조건은 `rand()`가 1이거나 101인 경우 두가지가 됨  
반면 51이 나오기 위한 `rand()`결과는 51 하나뿐이므로 모든 숫자가 나올 확률이 균등하지 않음  

#### 4) rand() 자체의 성능이 별로 좋지 않음
`rand()`에서 사용하는 알고리즘이 아주 훌륭하지는 않다고 함


## 3. C++방식의 random 라이브러리
  
```C++
#include <iostream>
#include <random>

int main() {    
    std::random_device rd;  // 시드값을 얻기 위한 random_device 생성    
    std::mt19937 gen(rd()); // random_device 를 통해 난수 생성 엔진을 초기화 한다
    std::uniform_int_distribution<int> dis(0, 99); // 균등 분포 정의(0 ~ 99가 균등한 난수열을 생성하기 위해)

    for (int i = 0; i < 10; i++) {
        std::cout << dis(gen) << ' ';
    }

    return 0;
}
```

#### 1) `std::random_device rd`
seed값을 얻기 위한 객체  

C언어는 `time(NULL)`로 seed를 얻었지만 modern C++의 random 라이브러리는 random_device를 이용해 seed를 얻음  

C언어 방식은 수학적 알고리즘을 이용해 가짜 난수를 생성했지만 random_device는 운영체제 자체에서 제공하는 진짜 랜덤하게 생기는 요소들이며 예를들어 장치드라이버의 noise를 기반으로 만들어진 난수같은것이 있음  

다만 진짜 난수이지만 컴퓨터가 주변장치와 상호작용하여 만들어지는 것이므로 C방식의 가짜난수보다는 속도가 매우 느리므로 seed값만 random_device로 얻고 그 후는 seed값을 기반으로 한 난수 생성 엔진으로 난수를 생성함  

#### 2) `std::mt19937 gen(rd())`
`std::mt19937`은 random 라이브러리가 제공하는 난수 생성 엔진 중 하나  

메르센 트위스터 알고리즘의 약자이며 mt19937알고리즘을 사용해서 C언어 방식의 `rand()`보다 난수들간의 상관관계가 매우 작은 비교적 더 양질의 난수 수열을 생성함

mt19937 객체가 만들어지기만 하면 그 후 난수를 생성하는 작업은 매우 빠르지만 mt19937은 객체의 크기가 커서 메모리 제약이 심한 상황이라면 random 라이브러리의 다른 난수 생성 엔진을 사용하는 방법이 있음  
예를들면 C언어의 rand()와 같은 알고리즘을 사용한 minstd_rand()함수가 있음  

#### 3) `std::uniform_int_distribution<int> dis(0, 99)`
0부터 99까지 균등한 확률로 난수를 만들기 위해 균등 분포를 정의하는 과정  
다양한 분포 설정이 가능하며 균등 분포 외에 자주 사용되는 것은 정규분포가 있음  

※ 균등 분포 사용방법
```C++
#include <iomanip>
#include <iostream>
#include <map>
#include <random>

int main() {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::normal_distribution<double> dist(/* 평균 = */ 0, /* 표준 편차 = */ 1);

    std::map<int, int> hist{};
    for (int n = 0; n < 10000; ++n) {
        ++hist[std::round(dist(gen))];
    }
    for (auto p : hist) {
        std::cout << std::setw(2) << p.first << ' '
            << std::string(p.second / 100, '*') << " " << p.second << '\n';
    }
}

  

![](https://blog.kakaocdn.net/dn/mSTC6/btsmsRAq3SX/vUplmKv30yHupT26es3BL0/img.png)