글 작성 다 하면 CS 폴더 만들어서 그쪽에 개념적인 내용들 넣고
여긴 라이브러리 내용만 넣어서 서로 링크따두기
정리 하나도 안된 글이라 중구난방임

쓰레드와 프로세스의 차이  
프로세스들은 메모리를 공유하지 않음  
프로세스1은 프로세스2의 메모리에 접근 불가능하고 프로세스2는 프로세스1의 메모리에 접근 불가능   
하지만 한 프로세스 안에 잇는 여러 쓰레드는 같은 메모리를 공유하고 스택만 별도로 가짐
쓰레드1과 쓰레드2가 같은 변수에 접근할 수 있음  
  
현대 기술의 발전으로 cpu는 코어가 한개가 아니고 여러개임  
그러므로 멀티코어 cpu에서는 각 코어에서 각기 다른 쓰레드들이 동시에 실행되고 잇음  
코어가 늘어나면서 멀티쓰레드로 프로그램을 만드는게 유리해짐  
  
멀티쓰레드로 만들기 유리한 프로그램은?  
1) 병렬 가능한 작업들  
1~10000까지 더하는 작업을 단일 쓰레드 프로그램으로 짠다면 그냥 for문으로 1~10000까지 더하면 됨  
하지만 이걸 10개의 쓰레드로 만든다면  1번쓰레드는 1~1000까지, 2번 쓰레드는 1001~2000까지... 하고나서 다 합치면  실행시간이 거의 10분의 1 수준이 될 수 잇음  

2) 대기시간이 긴 작업들  
인터넷에 잇는 자료를 스크랩 한다면 인터넷 속도가 cpu속도보다 느리므로 응답을 기다리기만 하는 시간이 생김  
이런 경우 여러개의 쓰레드로 요청하고 응답이 오기까지 또 다른 쓰레드가 요청하고 또 다른 쓰레드가 요청하고  
이러다가 응답이 오면 다운받고, 또 다운받고 하면 대기시간에도 다른 일을 할 수 있음  
    
하지만 모든 프로그램을 멀티쓰레드로 짠다고 유리한게 아님  
1) 애초에 멀티쓰레드에 부적합한 프로그램
피보나치 수열의 경우 n번째를 구하기 위해선 n-1과 n-2를 알아야함. 그리고 n+1을 구하기 위해선 n과 n-1을 알아야하고  
그러니 다음 연산을 하려면 이전 연산의 결과가 필요한 이런 경우 병렬화가 매우 어려움  

2) 임계영역 접근이 너무 많아서 병목현상이 발생하는 경우

쓰레드 생성법  
C++ 11 이전에는 표준이 없어서 리눅스는 pthread_create로, 윈도우는 CreateThread로 만드는 등 복잡햇지만  C++ 11 이후로는 표준이 나와서 쉽게 사용 가능  
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
이유는 cout << a << b;를 하게 되면 cout << a까지 잘 하고나서 << b를 수행하기 전 그 틈에 interupt가 들어올 수 있어서  
반면 printf는 하나의 함수단위로 작동해서 컨텍스트스위칭이 있던 말던 한번에 출력 다 되게 만들기 때문에 쓰레드가 값을 출력할땐 printf를 써주면 좋을 듯

___
# Thread

## 배경설명
과거의 CPU는 1개의 코어만 가졌었음  
즉 CPU가 한번에 하나의 일만 처리할 수 있었음  

하지만 User는 여러 프로세스를 동시에 실행하므로  
여러가지 일을 번갈아가면서 조금씩 실행해야 했음  

이를 위해 메모리에 올라와있는 현재 실행중인 프로그램들 중  
하나를 CPU로 가져와서 잠깐 처리해주고 또 다른 프로그램을 가져와서 처리함  
이떄 현재 실행중인 프로그램을 ==프로세스==라고 부름  

위처럼 메모리에서 값을 스위칭해주는 것을 ==컨텍스트 스위칭==(Context Switching)이라고 부름  
컨텍스트 스위칭은 CPU가 아니라 운영체제의 스케쥴러가 결정하는 것이고 CPU는 연산만 수행함

CPU 코어에서 돌아가는 프로그램 단위를 ==쓰레드==(Thread)라고 부름  
즉 CPU 코어 하나에서 한번에 하나의 쓰레드를 실행함

프로세스는 최소 한개의 쓰레드로 이루어져 있고  
여러개의 쓰레드로 구성된 프로그램을 멀티쓰레드 프로그램이라고 부름  

프로세스와 쓰레드의 가장 큰 차이점은  
프로세스는 서로 독립적이라 서로 메모리를 공유하지 않음  
프로세스 A와 B가 있다면 A는 B의 메모리에 접근할 수 없고, B도 A의 메모리에 접근할 수 없음  
반면 한 프로세스 내에 있는 쓰레드들은 서로 같은 메모리를 공유함  
프로세스 A 안에 쓰레드 1과 쓰레드 2가 있다면 같은 변수에 접근이 가능함

## Thread가 필요한 이유
크게 하드웨어의 발전과 병렬 프로그램의 장점 2가지가 있음  

1) 하드웨어의 발전
시대가 발전하면서 CPU의 코어가 더 이상 한개가 아닌 여러개가 됐음  
인텔의 i5는 4개의 코어를, AMD 라이젠7은 8개의 코어를 사용함  

그런데 계속해서 단일쓰레드 프로그램을 사용한다면  
코어 1개만 일을 하고 나머지 코어들은 놀게 됨  

2) 병렬 프로그램의 장점
병렬 가능한 프로그램이라면 여러개의 쓰레드가 병렬진행하는것이 유리함  

예를들어 1부터 10000까지 더하는 프로그램을 만든다면  
```C++
for(int i = 1; i <= 10000; i++) {
	sum += i;
}
```
위 처럼 진행하게 될 텐데 만약 병렬 프로그래밍을 한다면  
쓰레드 1이 1부터 1000까지, 쓰레드 2가 1001부터 2000까지, ..., 쓰레드 10이 9001부터 10000까지  
더해준 후 마지막에 10개의 값만 합쳐주면 되므로 시간이 10분의 1 수준으로 줄어들게 됨  
즉 병렬화가 가능한 프로그램이라면 멀티쓰레드로 구성해서 여러개의 코어가 동시에 일하는것이 유리함  

또 다른 예시로 대기시간이 긴 작업들을 처리할 때 유리함  
만약 네트워크 작업을 하는 경우 Download 함수가 있다면  
쓰레드 1이 Download함수를 호출하면 Core 1에서 Download 작업을 처리한 후  
CPU는 네트워크 작업에 관여하지 않으므로 다운로드가 완료될때까지 그냥 대기하는 낭비가 발생함.  
이 떄 쓰레드 2가 또 Download함수를 호출한다면 Core 1이 그냥 대기하는 것이 아니라 쓰레드 2의 작업을 처리할 수 있음  

하지만 모든 프로그램이 병렬화가 가능한것은 아님  
예를들어 피보나치 수열을 구한다면 ``F(n) = F(n - 1) + F(n - 2)``를 수행해야 하는데  
F(n)을 알기 위해선 F(n - 1)과 F(n - 2)가 필요하므로  
병렬화를 해봤자 앞의 값을 모르니 진행이 불가능함
즉, 각 연산이 독립적이라면 병렬화가 유리하지만 각 연산이 의존적이라면 병렬화가 힘듬


## C++에서 Thread 사용
modern C++인 C++ 11이 나오기 전까진 C++에 쓰레드 관련 표준이 없었음  
그래서 각 플랫폼마다 구현 방법이 달랐음  
예를들어 Windows에서는 쓰레드를 만들기 위해 CreateThread 함수를 사용했었고  
Linux에서는 pthread_create함수를 사용했었음  

하지만 C++ 11버전에 쓰레드가 표준으로 추가되면서 사용하기 굉장히 쉬워짐  
```C++
#include <thread>

thread t1(func1); // thread 객체를 만들고 수행할 함수를 매개변수로 넣어줌

t1.join();  // join은 Thread가 실행을 종료하면 return하는 함수
			// 즉 t1이 종료하기 전까지 return하지 않으므로 계속 대기함
			// 출력해줄것이 있거나 결과를 확인해야 할 때 사용
or

t1.detach(); // Thread가 백그라운드에서 작동함
			 // 대기하는 것이 아닌 Thread는 Thread대로, 프로그램은 프로그램 대로 진행
			 // 출력이나 결과 확인이 필요 없을때 사용
```

사용예시)  
```C++
#include <thread>

void func1() {
	for (int i = 0; i < 10; i++) {
		std::cout << "쓰레드 1 작동중! \n";
	}
}

void func2() {
	for (int i = 0; i < 10; i++) {
		std::cout << "쓰레드 2 작동중! \n";
	}
}

void func3() {
	for (int i = 0; i < 10; i++) {
		std::cout << "쓰레드 3 작동중! \n";
	}
}

int main()
{
	thread t1(func1);
	thread t2(func2);
	thread t3(func3);

	t1.join();
	t2.join();
	t3.join();
}
```

운영체제의 스케쥴러가 언제 컨텍스트 스위칭을 할지는 마음대로라서  
위 코드를 실행하면 "쓰레드 1 작동중!"과 "쓰레드 2 작동중!"과 "쓰레드 3 작동중!"이  
순서대로 10번씩이 아닌 마음대로 출력됨

#### join이나 detach를 하지 않는다면?
위 코드에서 join()함수를 사용하지 않으면 쓰레드들의 작업이 끝나기도 전에 main함수가 종료됨  
Thread가 join이나 detach되지 않은 상태에서 소멸자가 호출된다면 예외가 발생함  
그러므로 join으로 대기시키거나, detach로 백그라운드에서 알아서 돌아가게 해줘야 함  

#### Thread에 인자 전달하기
Thread는 리턴값이 없으므로 결과값을 받아야 한다면 포인터 형태로 사용
```C++
void worker(int start, int end, int* result)
{...}

int main()
{
	int result = 0;
	thread t1(worker, 0, 10, &result);
	t1.join();
}
```
thread 객체 생성시 첫번째 인자로 함수명을, 두번째 인자부터 해당 함수의 매개변수들을 적어주면 됨  

#### 현재 작업중인 Thread 확인하기
`thread::id this_id = std::this_thread::get_id();`
위의 get_id()함수를 사용해서 현재 작업중인 thread의 id를 알 수 있음

#### Thread를 사용해서 출력할 때 주의사항
멀티쓰레드 프로그램에서 cout 사용시 문제가 발생할 수 있음
```C++
cout << "A의 값은 : " << a << " B의 값은 : " << b << " C의 값은 : " << c << endl;
```
위와 같은 출력문이 있다면 cout의 <<연산자를 수행 할때마다 실행되는 쓰레드가 바뀔 수 있음  
그래서 출력문이 여러 쓰레드들의 출력끼리 섞일 수 있고  
이를 해결하기 위해 printf를 사용하는것이 좋을 수 있음
``` C++
printf("A의 값은 : %d B의 값은 : %d C의 값은 : %d\n", a, b, c);
```


## 경쟁상태
여러 쓰레드들은 같은 메모리에 접근할 수 있음  
만약 현재 접속중인 유저의 수를 저장하는 user_count 변수에 10이 들어있을 때  
Thread 1이 `user_count += 1`을 하기 위해 user_count값을 얻어왔는데 + 1을 하기 직전  
Thread 2로 교체가 됐고 Thread 2도 마찬가지로  `user_count += 1`을 수행한다면  
Thread 1의 결과는 11이고, Thread 2의 결과도 11이므로  
user_count는 12가 되어야 정상이지만 11로 저장될 수 있음  

즉 문제가 일어나지 않으려면 `user_count += 1;`을 동시에 여러 쓰레드가 접근할 수 없게 해야 함  

#### 뮤텍스(mutex)
경쟁상태를 해결하기 위해 뮤텍스 객체를 사용함  
mutex란 상호 배제(mutual exclusion)이라는 단어에서 따옴
```C++
#include <mutex>  // mutex 를 사용하기 위해 필요
#include <thread>

void user(int& result, std::mutex& m) {
	m.lock();
	result += 1;
	m.unlock();	
}

int main() {
	int user_count = 0;
	std::mutex m;  // 우리의 mutex 객체

	std::thread t1(user, std::ref(user_count), std::ref(m));
	t1.join();

	std::thread t2(user, std::ref(user_count), std::ref(m));
	t2.join();

	std::cout << "user_count 최종 값 : " << user_count << '\n';
}
```
위처럼 mutex 객체를 이용해 lock해주고 하나의 thread만 사용 후 unlock해주는 구조로 사용  

```C++
m.lock();
result += 1;
m.unlock();	
```
위에서 m.lock()과 m.unlock()으로 감싸진 부분을 임계 영역(critical section)이라고 부르며  
한개의 쓰레드만이 접근 가능함

만약 unlock()을 빼먹으면 모든 쓰레드가 먼저 들어간 쓰레드를 기다리기만 하고 아무 연산도 못하다가 프로그램이 죽어버림  
이런 상황을 데드락(deadlock)이라고 부름

그런데 코드가 많이 길어진다면 마치 동적할당 후 free를 빼먹는 것 처럼  
프로그래머의 실수로 unlock()을 빼먹는 상황이 생길 수도 있음  
그런 이유로 뮤텍스토 unique_ptr처럼 사용 후 해제 패턴을 따르기 위해  
lock_guard를 사용 가능함

```C++
void user(int& result, std::mutex& m) {
	std::lock_guard<std::mutex> lock(m);
	or
	std::unique_lock<std::mutex> ul(*m);
	
	result += 1;
}
```
위 코드처럼 사용하면 scope를 빠져 나가면서 lock객체가 소멸되므로  
lock_guard클래스의 소멸자가 호출되고 해당 소멸자에 unlock이 있어서 알아서 unlock됨  

lock_guard와 unique_lock의 차이점은
lock_guard는 생성자만 lock을 할 수 있는 반면  
unique_lock은 unlock 이후에 다시 lock을 할 수 있음  
그 외에는 거의 동일한 기능을 하는데 condition_variable이 인자로 unique_lock을 인자로 받으니  
condition_variable을 사용한다면 unique_lock을 사용해야 함  

#### 데드락
데드락 상황을 만들지 않기 위한 법칙들
1) 중첩된 Lock을 사용하는 것을 피해라
  모든 쓰레드들이 최대 1개의 Lock만을 소유하면 데드락이 발생하지 않음
  대부분의 경우 1개의 Lock으로 충분하므로 Lock은 1개만 사용하는 것으로 설계한다.
2) Lock을 소유하고 있을 때 유저 코드를 호출하는 것을 피해라
  이미 Lock을 소유한 상태에서 실행한 유저 코드에 만약 Lock을 얻는 코드가 있다면  
  스스로 본인의 Lock을 기다리는 데드락 상태가 발생할 수 있음
3) Lock들을 언제나 정해진 순서로 획득해라
  Lock은 1개만 소유하는것이 좋지만 꼭 여러개의 Lock을 획득해야만 한다면  
  반드시 정해진 순서대로 획득해야 한다.  
  func1에서는 m1, m2순으로 lock을 하는데 func2에서는 m2, m1 순으로 lock을 한다면  
  서로가 서로의 lock을 가진 교착상태가 되므로 반드시 둘 다 m1, m2 순으로 정해야 한다.  


try_lock() 함수
`if(m1.try_lock())`는 만약 m1이 lock을 할 수 있다면 lock을 하고 true를 리턴하고    
lock을 할 수 없다면 대기하지 않고 그대로 false를 리턴한다.  
이 함수를 이용해서 false가 리턴된다면 다른 뮤텍스를 unlock시켜버리는 방식으로  
특정 뮤텍스에 우선권을 줄 수 있음  


## 생산자 소비자 패턴
멀티 쓰레드 프로그램에서 가장 많이 등장하는 패턴인 생산자(Producer) - 소비자(Consumer) 패턴  

생산자는 무언가 처리해야 할 일을 받아오는 쓰레드  
소비자는 받은 일을 처리하는 쓰레드  

예를들어 웹 페이지를 긁어와서 분석하는 프로그램을 작성한다면  
생산자는 페이지를 긁어오는 쓰레드이고  
소비자는 긁어온 페이지를 분석하는 쓰레드의 역할을 의미한다.  

```C++
#include <chrono>
#include <mutex>
#include <thread>

void producer(...)
{
	// 웹 페이지를 긁어오는 코드
}

void consumer(...)
{
	while(~~~) {
		m->lock();
		if(downloaded_pages->empty()) {
			m->unlock(); // 할일이 없으니 다시 unlock

			std::this_thread::sleep_for(std::chrono::milliseconds(10));
			// 10ms 대기 후 다시 확인하도록
			continue;
		}
		else {
			// 페이지를 분석하는 코드
			m->unlock(); // 다 끝나면 unlock
		}
	}
}

int main()
{
	std::mutex m;

	std::thread t1(producer, ...);
	t1.join();

	std::thread t2(consumer, ...);
	t2.join();
}
```
소비자는 위 코드처럼 할 일이 있는지 확인하고 없다면 대기한다.  
대기 후 다시 할 일이 있는지 확인하고 또 없으면 대기한다.  

하지만 위와 같은 방법은 consumer가 할 일이 없어도 계속 할 일이 있는지 확인하느라  
다른 쓰레드들이 CPU를 사용할 시간에 consumer가 CPU를 사용하게 되므로 비효율적이다.  

그러므로 아예 consumer는 대기하지 않고 그냥 재워둔 후 할 일이 생기면 알려주는 것이 좋다.  
C++에선 이런 형태로 생산자 소비자 패턴을 구현하기 위해 여러 도구들을 제공한다.  

#### condition_variable
위 코드에서 downloaded_pages->empty()가 아닐때까지 자고 있으라는 명령을 내리고 싶다면  
조건변수(condition_variable)을 사용해서 해결할 수 있다.  
```C++
#include <chrono>              // std::chrono::miliseconds
#include <condition_variable>  // std::condition_variable
#include <mutex>
#include <thread>

void producer(std::queue<std::string>* downloaded_pages, std::mutex* m,
              int index, std::condition_variable* cv) 
{
    // data 는 쓰레드 사이에서 공유되므로 critical section 에 넣어야 한다.
    m->lock();
    downloaded_pages->push(content); // 페이지를 긁어와서 저장
    m->unlock();

    // consumer 에게 content 가 준비되었음을 알린다.
    cv->notify_one();
  }
}

void consumer(std::queue<std::string>* downloaded_pages, std::mutex* m,
              int* num_processed, std::condition_variable* cv) {
  while (...) {
    std::unique_lock<std::mutex> ul(*m);

    cv->wait(ul, [&] { return !downloaded_pages->empty(); });
	// 매개변수 값이 참이될때까지 wait한다

    if (작업 완료시) {
      ul.unlock();
      return;
    }

    // wait에서 풀렸으니 작업하는 부분
    std::string content = downloaded_pages->front();
    downloaded_pages->pop();

    (*num_processed)++;
    ul.unlock();

    // content 를 처리한다.
    std::cout << content;
    std::this_thread::sleep_for(std::chrono::milliseconds(80));
  }
}

int main() {
	std::mutex m;
	std::condition_variable cv;

	std::thread t1(producer, ..., &cv);
	t1.join();

	std::thread t2(consumer, ..., &cv);
	cv.notify_all(); // 나머지 자고 있는 쓰레드들을 모두 깨운다.
	t2.join();
}
```

consumer함수의 4번째줄의 
```C++
cv->wait(lk, [&] { return !downloaded_pages->empty(); });
// 매개변수 값이 참이될때까지 wait한다
```
부분은 해당 조건이 false라면 ul를 ==unlock한 후== 영원히 sleep하게 만듬  
해당 함수를 실행중인 thread 또한 누가 깨워주기 전까지 계속 sleep상태  

producer함수의 7번째줄의 
```C++
// consumer 에게 content 가 준비되었음을 알린다.
cv->notify_one();
```
는 페이지를 다 긁어왔다면 잠자고 있는 쓰레드들 중 하나를 깨워서  
wait조건을 다시 검사하게 시키는 역할  
조건을 맞춘 후 깨웠으니 해당 쓰레드는 일을 다시 시작하게 되는 것  

main함수의 8번째줄의
```C++
cv.notify_all(); // 나머지 자고 있는 쓰레드들을 모두 깨운다.
```
은 자고있는 프로그램의 종료 조건을 맞춘 시점이라도 consumer 쓰레드들이 있을것이고  
해당 쓰레드들은 계속 자느라 join하지 않고 프로그램을 끝낼 수 없게 되니  
전부 다 깨워서 join시키고 종료 조건이 맞으니 종료하기 위해 사용  

## 동기와 비동기 실행
동기(synchronous)실행은 한번에 하나씩 순차적으로 실행되는 것
```C++
string txt = read("a.txt");        // 5ms, 하드디스크 사용하는 연산
string result = do_something(txt); // 5ms

do_other(); // 5ms, CPU 사용하는 연산
```
위와 같은 코드가 있다면 하드디스크에서 a.txt를 읽어오는 5ms동안 CPU는 아무것도 하지 않음  
그만큼 CPU가 놀고 있으니 비효율이 발생하며 총 실행 시간은 15ms가 걸림  

비동기적(asynchronous) 실행은 프로그램의 실행이 한 갈래가 아니라  
여러 갈래로 갈라져서 동시에 진행되는 것을 의미하며 C++의 경우 쓰레드를 이용함  
```C++
void file_read(string* result) {
  string txt = read("a.txt"); // 5ms
  *result = do_something(txt); // 5ms
}
int main() {
  string result;
  thread t(file_read, &result);
  do_other(); // 5ms

  t.join();
}
```
위처럼 작성하면 thread t가 하드디스크에서 데이터를 읽어오게 되고  
그동안 CPU는 대기하지 않고 바로 main함수로 돌아와서 do_other()함수를 수행함  
그 후 t.join()을 수행하면 다시 file_read쓰레드를 실행하는데 이때 이미 read가 돼있으므로  
총 10ms의 시간만에 수행이 가능함

modern C++에는 위와 같은 비동기적 실행을 쉽게 하는 도구로  
std::promise와 std::futuer를 제공함

#### std::promise와 std::future
15-4, 15-5 마저 보고 정리해보기

그 외 14, 16~17-2, 17-5 보면 끝