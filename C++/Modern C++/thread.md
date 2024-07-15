# Thread  

## 1. Thread란

단일코어 CPU의 경우 여러 프로그램을 아주 잠깐씩 번갈아서 실행한다.  
CPU에서 실행할 프로그램을 바꿀때마다 프로그램을 변경해줘야 하는데 이를 컨텍스트 스위칭이라고 한다.  

다만 실행의 순서나 기간은 운영체제의 스케쥴러가 정하는 것이고 CPU는 단지 스케쥴러가 정해준대로 일할 뿐이다.  

여기서 CPU의 코어에서 돌아가는 프로그램의 단위를 thread라고 부른다.  
즉 CPU 코어 하나에서는 하나의 thread가 실행된다.  

이런 thread를 여러개 사용해서 구현한 프로그램이 멀티 쓰레드 프로그램이다.  
만약 thread를 따로 만들지 않고 구현한 프로그램은 하나의 쓰레드로 실행된다.  

최근의 CPU는 많이 발전해서 CPU 하나에 여러개의 코어가 장착되어있다.  
그러므로 동시에 여러개의 쓰레드가 동작할 수 있다.  
그러므로 멀티 쓰레드 프로그램으로 구현하는게 CPU를 더 효율적으로 쓸 수 있는 방법이다.  

그런데 프로세스 하나에 하나의 쓰레드로 구성된다면 굳이 멀티 쓰레드가 아닌 멀티 프로세스여도 CPU의 코어를 여러개 동시에 사용할 수 있다.  
그럼에도 멀티 프로세스 방식이 아닌 멀티 쓰레드 방식을 쓰는 이유는 컨텍스트 스위칭이 꽤나 코스트가 크기 때문이다.  
쓰레드는 컨텍스트 스위칭할때 스택부분만 바꿔주면 되고 공용부분은 바꾸지 않아도 된다.  
반면 프로세스는 모든 프로세스 내용을 컨텍스트 스위칭 해야하므로 여기서 차이가 발생한다.  

#### Process vs Thread
프로세스는 프로세스끼리 메모리를 공유하지 않는다.  
굳이 공유하려면 방법은 있지만 자원 부담이 크다.  

쓰레드는 같은 프로세스 내의 다른 쓰레드와 메모리를 공유할 수 있다.  
각 쓰레드는 자신만의 전용 스택 공간을 가진다.  
그 외의 공간인 힙이나 데이터영역, 코드영역 등에 있는 데이터는 공유가 가능한것이다.  


## 2. 멀티 쓰레드 방식이 항상 유리한가?

멀티 쓰레드 방식이 항상 유리한것은 아니다.  

#### 멀티 쓰레드의 장점
1) 컨텍스트 스위칭할때 공유하는 부분은 두고 스택만 갈아끼우면 되므로 빠르다.

#### 멀티 쓰레드의 단점
1) 쓰레드 하나가 오류를 일으키면 해당 프로세스 내의 다른 쓰레드까지 모두 종료될 수 있다.
2) 공유하는 자원부분에서의 동기화 문제를 잘 해결해야 한다.

#### 멀티 쓰레드 방식이 유리한 예시
1) 병렬 가능한 작업들    
1~10000까지 더하는 작업을 단일 쓰레드 프로그램으로 짠다면 그냥 for문으로 1~10000까지 더하면 된다.  
하지만 이걸 10개의 쓰레드로 만든다면  1번쓰레드는 1~1000까지, 2번 쓰레드는 1001~2000까지... 하고나서 다 합치면  실행시간이 거의 10분의 1 수준이 될 수 있다.  
  
2) 대기시간이 긴 작업들    
인터넷에 잇는 자료를 스크랩 한다면 인터넷 속도가 cpu속도보다 느리므로 응답을 기다리기만 하는 시간이 생긴다.  
이런 경우 여러개의 쓰레드로 요청하고 응답이 오기까지 또 다른 쓰레드가 요청하고 또 다른 쓰레드가 요청하고  
이러다가 응답이 오면 다운받고, 또 다운받고 하면 대기시간에도 다른 일을 할 수 있다.  

#### 멀티 쓰레드 방식에 부적합한 예시
1) 피보나치 수열  
피보나치 수열의 경우 n번째를 구하기 위해선 n-1과 n-2를 알아야한다.  
즉 다음 연산을 하려면 이전 연산의 결과가 필요하므로 이런 경우 병렬화가 매우 어렵다.    
  
2) 임계영역 접근이 너무 많아서 병목현상이 발생하는 경우


## 3. Modern C++에서의 Thread 생성하기

C++ 11 이전에는 thread에 표준이 없었다.  
나만해도 리눅스에서는 pthread_create 함수를 사용했고, 윈도우에선 CreateThread 함수를 사용했었다.  

하지만 C++ 11부터는 표준에 thread가 추가되어서 그냥 thread 객체 만들듯이 사용하면 된다.  
```C++
#include <iostream>
#include <thread>
using namespace std;

void func1() {
  for (int i = 0; i < 10; i++) {
    cout << "쓰레드 1 작동중! \n";
  }
}

void func2() {
  for (int i = 0; i < 10; i++) {
    cout << "쓰레드 2 작동중! \n";
  }
}

void func3() {
  for (int i = 0; i < 10; i++) {
    cout << "쓰레드 3 작동중! \n";
  }
}
int main() {
  thread t1(func1);
  thread t2(func2);
  thread t3(func3);

  t1.join();
  t2.join();
  t3.join();
}
```
위처럼 thread 객체를 만들고 매개변수로 실행할 함수를 넣어주면 새로 생긴 쓰레드에서 해당 함수를 실행하기 시작한다.  

그리고 join()함수는 쓰레드가 실행을 모두 마치면 해당 쓰레드의 할당을 해제하는 역할이다.  
즉 t1이 끝나고 리턴할때까지 대기하다가 t1.join()이 할당을 해제하고 다음 줄로 넘어가는 것이다.

다만 다시 한번 유념해야 할 것은 thread는 운영체제의 스케쥴러에 의해 먼저 실행될지, 얼마나 오래 실행될지 결정된다.  
그러므로 위의 경우 "쓰레드 작동중!" 문구가 꼭 1 10번, 2 10번, 3 10번 순서대로 나오지 않고 뒤죽박죽 나온다.  
마찬가지로 그럼 끝나는 시점도 꼭 `t1 -> t2 -> t3` 순서가 아니다.  
그래서 t1이 끝나서 t1.join()을 수행하고 t2.join()으로 갔는데 이미 t2가 먼저 끝났었다면 t2.join()도 즉시 수행된다.  

join() 말고 detach()라는 함수도 있다.  
```C++
int main() {
  thread t1(func1);
  thread t2(func2);
  thread t3(func3);

  t1.detach();
  t2.detach();
  t3.detach();

  std::cout << "메인 함수 종료 \n";
}
```
join의 경우 쓰레드가 끝나고 리턴 받을때까지 대기하는 역할이다.  
반면 detach의 경우 쓰레드가 끝날때까지 기다리지 않는다.  

그러므로 join의 경우 모든 출력이 끝나야 main함수가 종료되어서 프로그램이 끝난다.  
반면 detach의 경우 모든 출력이 끝나지 않았어도 main함수가 종료되므로 프로그램이 끝날 수 있다.  
하지만 detach를 걸어놨어도 메인 프로세스가 종료되면 안에 있는 쓰레드들도 다 종료되기때문에 쓰레드 진행 중간에 끝날 수도 있다.  

만약 join이나 detach 둘 다 사용하지 않은 쓰레드의 소멸자가 호출된다면 예외가 발생한다.  

#### 쓰레드가 실행시키는 함수에 매개변수 부여하기
그냥 쓰레드 만들때 첫 인자로 함수명, 두번째 인자부터 매개변수명 쓰면 된다.
```C++
#include <iostream>
#include <thread>
using namespace std;

void func(int a, int b, int* c)
{
	*c = a + b;
}

int main()
{
	int result = 0;
	thread t1(func, 1, 2, &result);
	t1.join();
	cout << result;

	return 0;
}
```

#### 알아둘것
1) 어느 쓰레드인지 확인하고 싶다면 아래의 get_id함수를 사용하면 쓰레드 id를 알 수 있다.
```C++
thread::id this_id = std::this_thread::get_id();
```

2) 쓰레드가 끝난 후 리턴값을 받고 싶다면
쓰레드는 리턴값이 없기때문에 결과를 받고싶다면 쓰레드가 실행시킬 함수의 매개변수에 포인터 변수를 놓고 거기에 전달 받으면 된다. 아래에서 배울 future를 사용하는 방법도 있다.

3) 쓰레드에서 cout을 사용하면 출력 결과가 이상하게 나올 수 있다.
```C++
cout << "i의 값은 : " << i << '\n';
```
cout의 경우 `<<` 하나당 한번씩 인터럽트가 들어 올 수 있다.  
그러니 "i의 값은 : 3" 까지 출력되기 전에 "i의 값은:"까지만 출력되고 다른 쓰레드로 컨텍스트 스위칭 될 수 있다.  

위의 코드를 여러개의 쓰레드가 실행한다면 컨텍스트 스위칭이 될때마다 값이 마구 뒤섞여서 출력될 것이다.  
그래서 쓰레드가 실행할 함수의 출력은 printf를 더 선호하는 듯 하다.  


## 4. 임계 영역

만약 여러개의 쓰레드가 하나의 변수의 값을 1씩 증가시키는 작업을 동시에 진행한다면 문제가 생길 수 있다.  

예를들어 t1이 count 값을 확인했을때 10이어서 11로 만든 후 `count = 11`을 대입한다.  
근데 만약 t1이 값을 확인한 후, 값을 다시 대입하기 전 그 사이구간에 t2가 값을 확인한다면?  

t2도 값이 10이라고 확인하고 11을 대입한다.  
둘이 1씩 올렸으니 12가 되어야 하는데 오류가 발생한다.  

이런식으로 count 변수처럼 동시에 접근하면 문제가 되는 영역을 임계영역이라고 한다.  
이런 임계 영역에 여러 쓰레드가 동시에 진입하려고 하면 어느 쓰레드가 먼저 들어갈지 중재해줘야 한다.  
이런식으로 여러 쓰레드가 들어가려고 기다리고 있는 상황을 경쟁 상태(race condition)이라고 하며  
이 상태에서 중재가 잘 이루어지지 않아서 아무도 들어가지 못하거나, 들어가서 나가지 않으면   
프로그램이 제대로 진행될 수 없고 이런 상황을 데드락(dead lock)이라고 한다.  


## 5. mutex

상호 배재(MUTual EXclusion)의 약자  

어느 쓰레드가 임계 영역에 들어갈지 중재해주는 역할을 하는 객체가 mutex다.  
뮤텍스 외에도 세마포어같은것도 있다.  

- mutex : 한번에 한명만 들어갈 수 있다. 화장실 문 잠금장치 같은 것이다.
- semaphore : 한번에 여러명이 들어갈 수 있다. 다만 최대 들어갈 수 있는 인원이 정해져있다.

즉 임계영역에 한명만 들어가야 한다면 mutex를 사용해도 되지만 최대 인원이 1인 semaphore를 사용하는것도 가능하다.  
세마포어는 C++ 20부터 사용가능하다.  

여기선 mutex 기준으로 설명한다.  
```C++
#include <iostream>
#include <mutex>  // mutex 를 사용하기 위해 필요
#include <thread>
#include <vector>

void worker(int& result, std::mutex& m) {
  for (int i = 0; i < 10000; i++) {
    m.lock();
    result += 1;
    m.unlock();
  }
}

int main() {
  int counter = 0;
  std::mutex m;  // 우리의 mutex 객체

  std::vector<std::thread> workers;
  for (int i = 0; i < 4; i++) {
    workers.push_back(std::thread(worker, std::ref(counter), std::ref(m)));
  }

  for (int i = 0; i < 4; i++) {
    workers[i].join();
  }

  std::cout << "Counter 최종 값 : " << counter << std::endl;
}
```
main함수 내에서 mutex 객체를 만든다.  
worker함수에서 만들지 않는 이유는 각 쓰레드마다 mutex를 갖길 원하는게 아니라 모든 쓰레드가 하나의 mutex로 통제되길 원하기 때문이다.  

그리고 쓰레드가 실행하는 함수에선 임계영역 값인 result에 진입하기 전에 lock()하고, 나올때 unlock()한다.  
엄밀히 말하면 lock()은 뮤텍스 객체를 내가 쓰게 해달라고 요청하는것과 같다.  
그래서 자신의 차례가 오면 뮤텍스 객체를 사용해서 임계영역에 들어간다고 보면 된다.  
그 후 모두 사용했으면 unlock()으로 뮤텍스 객체를 반환한다.  

만약 unlock()을 제대로 해주지 않으면 다른 쓰레드들이 들어가지 못하니 프로그램이 비정상 종료된다.  
또는 unlock()을 하지 않고 자기 자신이 또 lock()으로 뮤텍스 객체를 요청하면 데드락에 빠지게 된다.  
그러므로 unlock()을 반드시 잘 해줘야 한다.  

그러나 코드가 길어지고 복잡해지다보면 unlock()을 빠트릴 수 있다.  
그러므로 동적할당 해제를 스마트포인터에 맡기듯 뮤텍스의 관리를 다른 객체에 맡기는 것이 좋다.  
```C++
void worker(int& result, std::mutex& m) {
  for (int i = 0; i < 10000; i++) {
    // lock 생성 시에 m.lock() 을 실행한다고 보면 된다.
    std::lock_guard<std::mutex> lock(m); // 또는 unique_lock<mutex> lock(m);
    result += 1;

    // scope 를 빠져 나가면 lock 이 소멸되면서
    // m 을 알아서 unlock 한다.
  }
}
```
위처럼 `lock_guard<mutex> lock(m)`나 `unique_lock<mutex> lock(m)`으로 lock 객체를 만들고, 스코프를 벗어날때 unlock하게 하거나 lock.unlock()함수로 unlock 할 수 있다.  

unique_lock은 기존의 lock_guard와 거의 동일하지만 차이점은  
lock_guard는 생성자가 lock하는 경우 외엔 lock할수없다.  
반면 unique_lock은 생성자 외에도 lock할 수 있어서 unlock 후에 다시 lock할 수 있다.  

뮤텍스 설정/lock_guard/unique_lock은 최소한의 부분에만 해야한다.  
그러므로 lock_guard와 unique_lock은 필요한 부분까지만 lock하고 unlock시킬 필요가 있는데  
그냥 스코프 하나 더 만들어서 그 안에서 해결하는 방법도 있다.  
근데 애초에 이게 됐으면 lock_guard도 필요없고 mutex만 써도 됐을것같다.  
```C++
void worker() {
  {
    std::lock_guard<std::mutex> lk(m);
	// 임계영역
  }
  // 임계영역 밖
}
```
또는 그냥 lk.unlock() 해도 된다.  

혹시나 다른곳에서 해당 뮤텍스를 사용하고 있는지 확인하고 싶다면  
```C++
  if (!m1.try_lock()) {
    m2.unlock();
    continue;
  }
```
위처럼 try_lock()함수를 사용하면 되고, 만약 아무도 사용하고 있지 않다면 lock()을 수행 후 true를 리턴한다.  
누군가 사용하고 있다면 대기하지 않고 false를 리턴한다.  

#### 데드락을 피하기 위한 가이드라인
1) 중첩된 Lock을 사용하는 것을 피해라
- 대부분의 lock은 하나만으로 해결 가능하다.
- 여러개의 lock을 가지도록 구현하는 것을 피해야 한다.

2) Lock을 소유하고 있을 때 유저 코드를 호출하는 것을 피해라
- Lock을 소유한 상황에서 호출한 유저코드가 또 Lock을 요청할 수도 있기때문에

3) Lock을 언제나 정해진 순서로 획득해라
- 만약 정말 어쩔 수 없이 여러개의 Lock을 사용한다면 순서를 정해야한다.
- t1은 m1, m2 순서로 lock을 하는데 t2가 m2, m1 순서로 lock을 한다면 서로가 서로의 락을 기다릴 수 있다.


## 6. 생산자 소비자 패턴

멀티 쓰레드 프로그램에서 자주 등장하는 패턴이다.  

생산자는 무언가 처리할 일을 받아오는 쓰레드이다.  
소비자는 받은 일을 처리하는 쓰레드이다.  

예를들어 인터넷 페이지를 긁어와서 분석하는 프로그램을 만든다면  
생산자가 인터넷 페이지를 긁어오고 소비자가 긁어온 페이지를 분석한다.  
```C++
// 좋지 못한 구현방법

#include <chrono>  // std::chrono::miliseconds
#include <iostream>
#include <mutex>
#include <queue>
#include <string>
#include <thread>
#include <vector>

void producer(std::queue<std::string>* downloaded_pages, std::mutex* m,
              int index) {
  for (int i = 0; i < 5; i++) {
    // 웹사이트를 다운로드 하는데 걸리는 시간이라 생각하면 된다.
    // 각 쓰레드 별로 다운로드 하는데 걸리는 시간이 다르다.
    std::this_thread::sleep_for(std::chrono::milliseconds(100 * index));
    std::string content = "웹사이트 : " + std::to_string(i) + " from thread(" +
                          std::to_string(index) + ")\n";

    // data 는 쓰레드 사이에서 공유되므로 critical section 에 넣어야 한다.
    m->lock();
    downloaded_pages->push(content);
    m->unlock();
  }
}

void consumer(std::queue<std::string>* downloaded_pages, std::mutex* m,
              int* num_processed) {
  // 전체 처리하는 페이지 개수가 5 * 5 = 25 개.
  while (*num_processed < 25) {
    m->lock();
    // 만일 현재 다운로드한 페이지가 없다면 다시 대기.
    if (downloaded_pages->empty()) {
      m->unlock();  // (Quiz) 여기서 unlock 을 안한다면 어떻게 될까요?

      // 10 밀리초 뒤에 다시 확인한다.
      std::this_thread::sleep_for(std::chrono::milliseconds(10));
      continue;
    }

    // 맨 앞의 페이지를 읽고 대기 목록에서 제거한다.
    std::string content = downloaded_pages->front();
    downloaded_pages->pop();

    (*num_processed)++;
    m->unlock();

    // content 를 처리한다.
    std::cout << content;
    std::this_thread::sleep_for(std::chrono::milliseconds(80));
  }
}

int main() {
  // 현재 다운로드한 페이지들 리스트로, 아직 처리되지 않은 것들이다.
  std::queue<std::string> downloaded_pages;
  std::mutex m;

  std::vector<std::thread> producers;
  for (int i = 0; i < 5; i++) {
    producers.push_back(std::thread(producer, &downloaded_pages, &m, i + 1));
  }

  int num_processed = 0;
  std::vector<std::thread> consumers;
  for (int i = 0; i < 3; i++) {
    consumers.push_back(
        std::thread(consumer, &downloaded_pages, &m, &num_processed));
  }

  for (int i = 0; i < 5; i++) {
    producers[i].join();
  }
  for (int i = 0; i < 3; i++) {
    consumers[i].join();
  }
}
```
위의 구현방식은 생산자는 자체적으로 일을 한다.  
소비자는 때때로 lock()을 걸고 할 일이 있는지 확인한다. 만약 할일이 없다면 unlock()하고 10ms 후에 다시 확인한다.  
즉 소비자는 일이 없을때도 쓸데없이 계속 lock()과 sleep을 반복한다.  

이렇게 비효율적인 방식이 아닌 생산자가 일을 넣고나서 소비자에게 알리는 방식이 더 좋다.  
이 방식을 구현하는 도구로 condition_variable이 있다.  
조건 변수 라는 뜻이며 해당 조건을 만족하면 일을 시작해라! 라는 뜻으로 사용할 수 있다.  
```C++
std::unique_lock<std::mutex> lk(*m);

cv->wait(lk, [&] { return !downloaded_pages->empty() || *num_processed == 25; });
```
위처럼 사용하면 `!downloaded_pages->empth() || *num_processed == 25` 조건이 true가 될때가지 wait하라는 뜻이 된다.  
조건변수는 위 조건이 거짓이라면 lk를 unlock하고 누군가 깨워줄때까지 영원히 sleep한다.  
즉 wait의 기능은 조건이 안맞으면 unlock하고 sleep한다, 조건이 맞으면 일을 한다.  

자 그럼 모든 소비자가 sleep하고 있다고 하자  
생산자가 일을 가져온 후 소비자들 중 하나를 깨워야 한다  
```C++
// consumer 에게 content 가 준비되었음을 알린다.
cv->notify_one();
```
만약 모든 쓰레드가 이미 일을 하고 있다면 아무일도 일어나지 않는다.  
반면 자고 있는 쓰레드가 있다면 wait의 조건을 다시 검사하게 한다.  

그러고나서 모든 일이 끝났을땐 자고 있는 쓰레드들을 깨워서  
조건을 확인해야 쓰레드들이 종료하므로 모든 쓰레드를 깨워야 한다.  
```C++
// 나머지 자고 있는 쓰레드들을 모두 깨운다.
cv.notify_all();
```
notify_one과 notify_all이 다름을 인지하자.  

이렇게 생산자 소비자 패턴을 개선한 코드는 아래와 같다.  
```C++
// 개선된 구현 방법

#include <chrono>              // std::chrono::miliseconds
#include <condition_variable>  // std::condition_variable
#include <iostream>
#include <mutex>
#include <queue>
#include <string>
#include <thread>
#include <vector>

void producer(std::queue<std::string>* downloaded_pages, std::mutex* m,
              int index, std::condition_variable* cv) {
  for (int i = 0; i < 5; i++) {
    // 웹사이트를 다운로드 하는데 걸리는 시간이라 생각하면 된다.
    // 각 쓰레드 별로 다운로드 하는데 걸리는 시간이 다르다.
    std::this_thread::sleep_for(std::chrono::milliseconds(100 * index));
    std::string content = "웹사이트 : " + std::to_string(i) + " from thread(" +
                          std::to_string(index) + ")\n";

    // data 는 쓰레드 사이에서 공유되므로 critical section 에 넣어야 한다.
    m->lock();
    downloaded_pages->push(content);
    m->unlock();

    // consumer 에게 content 가 준비되었음을 알린다.
    cv->notify_one();
  }
}

void consumer(std::queue<std::string>* downloaded_pages, std::mutex* m,
              int* num_processed, std::condition_variable* cv) {
  while (*num_processed < 25) {
    std::unique_lock<std::mutex> lk(*m);

    cv->wait(
        lk, [&] { return !downloaded_pages->empty() || *num_processed == 25; });

    if (*num_processed == 25) {
      lk.unlock();
      return;
    }

    // 맨 앞의 페이지를 읽고 대기 목록에서 제거한다.
    std::string content = downloaded_pages->front();
    downloaded_pages->pop();

    (*num_processed)++;
    lk.unlock();

    // content 를 처리한다.
    std::cout << content;
    std::this_thread::sleep_for(std::chrono::milliseconds(80));
  }
}

int main() {
  // 현재 다운로드한 페이지들 리스트로, 아직 처리되지 않은 것들이다.
  std::queue<std::string> downloaded_pages;
  std::mutex m;
  std::condition_variable cv;

  std::vector<std::thread> producers;
  for (int i = 0; i < 5; i++) {
    producers.push_back(
        std::thread(producer, &downloaded_pages, &m, i + 1, &cv));
  }

  int num_processed = 0;
  std::vector<std::thread> consumers;
  for (int i = 0; i < 3; i++) {
    consumers.push_back(
        std::thread(consumer, &downloaded_pages, &m, &num_processed, &cv));
  }

  for (int i = 0; i < 5; i++) {
    producers[i].join();
  }

  // 나머지 자고 있는 쓰레드들을 모두 깨운다.
  cv.notify_all();

  for (int i = 0; i < 3; i++) {
    consumers[i].join();
  }
}
```


## 7. 명령어 재배치와 수정순서

#### 1) 명령어 재배치
명령어 재배치는 컴파일러에 의해 일어날 수도 있고 물리적인 상황때문에 일어날 수도 있다.  

1-1) 파이프라이닝(pipelining)  
여러개의 작업을 동시에 실행하는 것  
![](https://blog.kakaocdn.net/dn/UysZO/btsIzehSHRJ/9G7ThVtBcN4OZsJSX7JJtk/img.png)

파이프라이닝을 하지 않으면 위처럼 비효율적인 작업을 한다.  
반면 파이프라이닝을 하면 아래와 같이 효율적으로 작업을 할 수 있다.  

![](https://blog.kakaocdn.net/dn/bKkMW8/btsIBhYhvvk/JZhkzz7qDh2QbRc1o3LZx0/img.png)

세탁기는 세탁기대로, 건조기는 건조기대로, 빨래 개기는 빨래 개기대로 동시에 진행하지만 더 효율적이다.  

CPU의 경우 명령어를 실행할 때 아래의 단계를 거친다.  
fetch(명령어 읽기) -> decode(명령어 해석) -> execute(명령어 실행) -> write(결과 기록)  
즉 CPU의 각 작업들도 파이프라이닝을 하면 더 효율적이다.  
![](https://blog.kakaocdn.net/dn/blMVdz/btsIzAdVcBW/RB64kQf8dyyv67rGJyzcI1/img.png)

다만 세탁기를 돌리는데는 30분이 걸리는데 건조기를 돌리는데는 1시간이 걸린다면  
건조기를 기다리면서 세탁기를 계속 돌릴필요는 없다.  

즉 파이프라인을 효율적으로 사용하려면 명령의 재배치가 필요하다.  
컴파일러는 명령어들을 재배치해서 파이프라인을 효율적으로 사용할 수 있게 한다.  
물론 명령어를 재배치해도 최종 결과는 똑같게 나오게 재배치한다.  

근데 문제는 컴파일러의 명령어 재배치는 다른 쓰레드들을 고려하지 않는다.  
그래서 멀티 쓰레드 환경에서는 예상치 못한 결과가 나올 수 있다.  

1-2) 물리적인 상황에 따른 재배치  
```C++
// int a = 0, b = 0에서 시작

a = 1; // 캐시에 없음
b = 1; // 캐시에 있음
```
이런 경우 a는 RAM에서 가져오느라 오래걸리는데 b는 캐시에 있으니 빠르게 진행된다.  
그래서 다른 쓰레드에서 봤을때에는 a = 0, b = 1인 순간이 포착될 수 있다.  

#### 2) 수정순서(modification order)
그러면 명령어 재배치나 물리적 상황에 따라 명령어 순서도 뒤죽박죽이고, 값도 제대로 보관되지 않는다.  
하지만 최종적으로 결과는 올바르게 나온다.  
그 방법이 수정순서라는 개념때문이다.  
![](https://blog.kakaocdn.net/dn/cdYdxc/btsIAcwWXGi/cLK2nbxEqLTMEMBidGs971/img.png)

예를들어 어떤 쓰레드가 처음 값을 확인했을때 8로 읽었다면 그 다음에 읽었을땐 반드시 8, 6, 3 중 하나여야 한다.  

개념이 되게 어려운데 그냥 간단하게 이러는 이유가 있다.  
CPU의 코어들은 각각이 각자의 캐시를 가진다.  
멀티 쓰레드 프로그램이라면 각 코어가 자신의 계산을 하고 자신의 캐시에 기록한다.  
그러니까 같은 값에서 시작했어도 각자 보관하고 있는 캐시의 값이 다 다를 수 있다.  
이걸 항상 올바르게 맞추려면 동기화해야 하는데 동기화는 시간이 오래걸리는 작업이다.  
그렇기때문에 모든 쓰레드가 수정순서에 동의하고 결과는 항상 올바르게 만든다.  

C++에서는 원자적 연산을 하는 경우 모든 쓰레드들이 같은 객체에 대해 동일한 수정 순서를 관찰한다.  
즉 모든 연산들이 원자적이면 수정순서에 동의한다.  
원자적인 연산이 아니라면 수정순서가 쓰레드마다 다를 수 있기때문에 적절한 동기화 방법을 통해 처리해야 한다.  


## 8. atomic

하지만 원자적(atomic)이라면 얘기가 쉽다.  
원자적인 연산은 CPU가 한번에 처리 가능한 연산이므로 뮤텍스도 필요없다.  
그렇기에 속도도 더 빠르다.  
```C++
#include <atomic>
#include <iostream>
#include <thread>
#include <vector>

void worker(std::atomic<int>& counter) {
  for (int i = 0; i < 10000; i++) {
    counter++;
  }
}

int main() {
  std::atomic<int> counter(0);

  std::vector<std::thread> workers;
  for (int i = 0; i < 4; i++) {
    workers.push_back(std::thread(worker, ref(counter)));
  }

  for (int i = 0; i < 4; i++) {
    workers[i].join();
  }

  std::cout << "Counter 최종 값 : " << counter << std::endl;
}
```
글의 처음에 예시들었던 counter라는 동일한 변수에 여러 쓰레드가 1씩 더하는 코드이다.  
차이점은 counter라는 변수가 atomic객체이며 0을 초기값으로 갖고 시작한다는 점이다.  
또한 mutex도 사용하지 않는다.  

원자적 연산은 컴파일러가 어느 CPU에서 실행될지 알고 있기때문에 원자적 연산 명령어를 제공할 수 있어서 가능한 것이다.  
원자적인 명령어를 제공하지 않는 CPU일수도 있다. 그러므로 확인이 필요하다.  
원자적 연산이 가능한지 확인하는 함수는 is_lock_free()이다. 뮤텍스의 lock()/unlock() 없이도 올바르게 연산을 수행할 수 있다는 뜻이다.  
```C++
std::atomic<int> x;
std::cout << "is lock free ? : " << boolalpha << x.is_lock_free() << std::endl;
```


## 9. memory_order

atomic 객체들은 원자적 연산시에 메모리 접근 방식을 지정할 수 있다.  

#### memory_order_relaxed
memory_order_relaxed는 가장 느슨한 조건이다.  
이 방식으로 메모리에서 읽기/쓰기를 할 경우 주위의 다른 메모리 접근들과 순서가 바뀌어도 된다.  
```C++
#include <atomic>
#include <cstdio>
#include <thread>
#include <vector>
using std::memory_order_relaxed;

void t1(std::atomic<int>* a, std::atomic<int>* b) {
  b->store(1, memory_order_relaxed);      // b = 1 (쓰기)
  int x = a->load(memory_order_relaxed);  // x = a (읽기)

  printf("x : %d \n", x);
}

void t2(std::atomic<int>* a, std::atomic<int>* b) {
  a->store(1, memory_order_relaxed);      // a = 1 (쓰기)
  int y = b->load(memory_order_relaxed);  // y = b (읽기)

  printf("y : %d \n", y);
}

int main() {
  std::vector<std::thread> threads;

  std::atomic<int> a(0);
  std::atomic<int> b(0);

  threads.push_back(std::thread(t1, &a, &b));
  threads.push_back(std::thread(t2, &a, &b));

  for (int i = 0; i < 2; i++) {
    threads[i].join();
  }
}
```
store와 load는 atomic 객체들이 원자적으로 쓰고 읽게 하는 함수이다.  

memory_order_relaxed는 가장 느슨한 조건이라고 했으므로  
위 코드를 실행하면 x, y값이 { 1, 0 }이 나올수도 있고 { 0, 1 }이 나올수도 있고 { 1, 1 }이 나올수도 있다.  
심지어는 가장 느슨한 조건이라 명령어를 재배치해서 { 0, 0 }조차 나올 수 있다.  

즉 memory_order_relaxed는 CPU에게 메모리 연산 순서에 무한한 자유를 주는 것이다.  
이때문에 속도가 아주 빠르지만 결과를 예상할 수 없게 된다.  

하지만 memory_order_relaxed가 유용한 경우가 있다.  
처음의 counter++ 상황이다.  
```C++
#include <atomic>
#include <iostream>
#include <thread>
#include <vector>
using std::memory_order_relaxed;

void worker(std::atomic<int>* counter) {
  for (int i = 0; i < 10000; i++) {
    // 다른 연산들 수행

    counter->fetch_add(1, memory_order_relaxed);
  }
}
int main() {
  std::vector<std::thread> threads;

  std::atomic<int> counter(0);

  for (int i = 0; i < 4; i++) {
    threads.push_back(std::thread(worker, &counter));
  }

  for (int i = 0; i < 4; i++) {
    threads[i].join();
  }

  std::cout << "Counter : " << counter << std::endl;
}
```
`counter->fetch_add(1, memory_order_relaxed);`는 counter++와 개념은 동일하지만 메모리 접근 방식이 다르다.  
counter++였다면 먼저 counter의 값을 읽고, 1을 더하고, 다시 그 결과를 기록하겠지만  
memory_order_relaxed라면 읽고, 더하고, 기록하는 순서가 뒤바뀌어도 된다.  
즉 어떻게 뒤바뀌던 말던 결론적으로는 1을 더한다라는 연산이 10000번 등장하기때문에 최종 결과는 같다.  

#### memory_order_release와 memory_order_acquire
위의 memory_order_relaxed는 특수한 경우에 사용이 가능하지만 용도가 굉장히 제한적이다.  
좀 더 엄격한 방식인 memory_order_release와 memory_order_acquire가 있다.  

memory_order_release는 해당 명령 이전의 모든 메모리 명령이 해당명령 이후로 재배치되는 것을 금지한다.  
또한 같은 변수를 memory_order_acquire로 읽는 쓰레드가 있다면 memory_order_release이전에 오는 메모리 명령들이 해당 쓰레드에 의해 관찰될 수 있어야 한다.  

memory_order_acquire의 경우 release와 반대로 해당 명령 뒤에 오는 모든 메모리 명령들이 해당 명령 위로 재배치되는 것을 금지한다.  
그래서 release와 acquire를 이용해서 서로 다른 두개의 쓰레드가 동기화를 수행할 수 있다.  

예를들어 아래와 같은 코드가 있다면 relaxed끼리는 자기들끼리 CPU에서 재배치 될 수 있지만 release 뒤로 재배치될 수는 없다.  
```C++
data[0].store(1, memory_order_relaxed);
data[1].store(2, memory_order_relaxed);
data[2].store(3, memory_order_relaxed);
is_ready.store(true, std::memory_order_release);
```

반면 아래와 같은 코드가 있다면 아래의 relaxed들은 acquire 위로 재배치 될 수 없다.  
```C++
while (!is_ready.load(std::memory_order_acquire)) {
  }

  std::cout << "data[0] : " << data[0].load(memory_order_relaxed) << std::endl;
  std::cout << "data[1] : " << data[1].load(memory_order_relaxed) << std::endl;
  std::cout << "data[2] : " << data[2].load(memory_order_relaxed) << std::endl;
```

그래서 아래 사진 한장으로 모든게 이해되는데  
![](https://blog.kakaocdn.net/dn/E1uDM/btsIy0xrU7c/FR2NE06Laz1LOtLnPpy5Yk/img.png)
release와 acquire 둘이 동기화 되는 시점인거고 그 전과 후는 값이 동기화 될 수 밖에 없다.  

#### 전체 코드  
```C++
#include <atomic>
#include <iostream>
#include <thread>
#include <vector>
using std::memory_order_relaxed;

std::atomic<bool> is_ready;
std::atomic<int> data[3];

void producer() {
  data[0].store(1, memory_order_relaxed);
  data[1].store(2, memory_order_relaxed);
  data[2].store(3, memory_order_relaxed);
  is_ready.store(true, std::memory_order_release);
}

void consumer() {
  // data 가 준비될 때 까지 기다린다.
  while (!is_ready.load(std::memory_order_acquire)) {
  }

  std::cout << "data[0] : " << data[0].load(memory_order_relaxed) << std::endl;
  std::cout << "data[1] : " << data[1].load(memory_order_relaxed) << std::endl;
  std::cout << "data[2] : " << data[2].load(memory_order_relaxed) << std::endl;
}

int main() {
  std::vector<std::thread> threads;

  threads.push_back(std::thread(producer));
  threads.push_back(std::thread(consumer));

  for (int i = 0; i < 2; i++) {
    threads[i].join();
  }
}
```

#### memory_order_acq_rel
이름에서 acq와 rel이 보이듯이 acquire와 release를 모두 수행하는 용도이다.  
읽기와 쓰기를 모두 수행하는 fetch_add 같은 함수에서 사용가능하다.  

#### memory_order_seq_cst
메모리 명령의 순차적 일관성(sequential consistency)를 보장해준다.  
즉 메모리 명령 재배치도 하지말고, 모든 쓰레드에서 모든 시점에 동일한 값을 관찰할 수 있는  
사람이 기대하는 방식대로 CPU가 작동하게 하는 방식이다.  

이 기준이 atomic 객체를 사용할때 memory_order를 따로 지정해주지 않으면 사용되는 default 값이다.  
atomic한 counter++은 counter.fetch_add(1, memory_order_seq_cst)와 동일한 연산이다.

다만 당연히 가장 코스트가 큰 연산 방식이라서 꼭 필요할때만 사용해야 한다.  
그러므로 atomic객체를 사용할때 memory_order를 더 약하게 지정해주지 않으면 성능향상을 기대하기 어려울 수 있다.  


## 10. 동기와 비동기 실행

동기(synchronous), 비동기(asynchronous)  
멀티쓰레딩의 강력함을 더 쉽게 활용할 수 있게 도와주는 도구들이다.  

인터넷에서 데이터를 긁어와서 분석한다고 할때  
**동기적**이란 말은 데이터를 긁어올때까지 기다린다, 다 긁어왔으면 분석한다, 다 분석했으면 또 긁어온다.  
이런식으로 순차적으로 진행하는 개념이다.  
문제는 데이터가 다 긁어와질때까진 CPU가 아무것도 하지 않고 대기하므로 낭비가 발생한다.  

**비동기적**이란 말은 데이터를 긁어올 때까지 기다리지 않고 다른 작업을 하고 있는것이라고 생각하면 된다.  

#### 동기적인 상황의 예시코드
```C++
string txt = read("a.txt");                  // 5ms
string result = do_something_with_txt(txt);  // 5ms

do_other_computation();  // 5ms 걸림 (CPU 로 연산을 수행함)
```
위의 경우 read, do_somthing_with_txt, do_other_computation 순서로 진행해서 총 15ms가 걸린다.  

#### 비동기적 상황의 예시코드
```C++
void file_read(string* result) 
{
  string txt = read("a.txt");  // (1)
  *result = do_something_with_txt(txt);
}

int main() 
{
  string result;
  thread t(file_read, &result);
  do_other_computation();  // (2)

  t.join();
}
```
이 경우 thread t가 생성되면서 file_read함수를 실행하므로 read함수가 실행된다.  
그 후 read함수가 실행될동안 대기하는게 아니라 main함수 쓰레드의 do_other_computation()을 수행한다.  
do_other_computation()이 끝나고 나면 t.join()을 수행하면서 다시 file_read함수 쓰레드를 연산하게 되는데 이땐 이미 read가 다 되어 도착해있을 것이므로 바로 do_something_with_txt를 수행한다.  

이렇게 되면 read에서 5ms를 기다리지 않고 다른 일을 처리하므로 총합 10ms에 처리가 가능하다.  

C++ 11에서 추가된 표준 라이브러리를 이용해서 비동기적 실행을 간단하게 할 수 있다.  


## 11. std::promise와 std::future

비동기적 실행을 구현하기 위한 객체들이다.  

비동기적 실행으로 처리하고 싶은 일이 무엇인가?  
내가 다른 일을 할 동안 다른 쓰레드가 어떠한 데이터를 처리하게 한 후 받아내는 것이다.  
즉 미래(future)에 다른 쓰레드가 내게 원하는 데이터를 돌려주겠다는 약속(promise)이다.  
```C++
#include <future>
#include <iostream>
#include <string>
#include <thread>
using std::string;

void worker(std::promise<string>* p) {
  // 약속을 이행하는 모습. 해당 결과는 future 에 들어간다.
  p->set_value("some data");
}
int main() {
  std::promise<string> p; // 연산 후 돌려줄 객체의 타입을 템플릿 인자로 작성
  
  std::future<string> data = p.get_future(); // promise에 대응되는 future 객체를 get
  											 // get한 future객체를 data 변수에 보관함
  std::thread t(worker, &p);				 // 그 후 thread가 worker함수 실행

  // 미래에 약속된 데이터를 받을 때 까지 기다린다.
  data.wait(); // 있어도 되고 없어도 됨

  // wait 이 리턴했다는 뜻이 future 에 데이터가 준비되었다는 의미.
  // 참고로 wait 없이 그냥 get 해도 wait 한 것과 같다.
  // 값이 아직 전달 안됐으면 wait했다가 얻기때문
  // get()하는 시점에 전달된 객체를 얻음
  std::cout << "받은 데이터 : " << data.get() << std::endl;
  // 주의사항 : get()은 절대 두번쓰면 안된다.

  t.join();
}
```
메인함수쪽을 잘 보면 된다.  
다른 설명은 주석을 통해 잘 달아놨지만 get()을 절대 두번 써선 안되는 점에 주의해야 한다.  
furture에서 get을 호출하면 설정된 객체가 이동된다. 그래서 get을 또 호출하면 안된다.  
만약 여러 쓰레드에서 future를 get해야 해서 여러번 써야 한다면 shared_future를 사용해야 한다.  

정리해보면 promise는 마치 생산자이고, future는 소비자인것처럼 보인다.  

future는 예외도 던질 수 있기때문에 예외를 잡기에도 더 좋다.  
```C++
#include <exception>
#include <future>
#include <iostream>
#include <string>
#include <thread>
using std::string;

void worker(std::promise<string>* p) {
  try {
    throw std::runtime_error("Some Error!");
  } catch (...) {
    // set_exception 에는 exception_ptr 를 전달해야 한다.
    p->set_exception(std::current_exception());
  }
}
int main() {
  std::promise<string> p;

  // 미래에 string 데이터를 돌려 주겠다는 약속.
  std::future<string> data = p.get_future();

  std::thread t(worker, &p);

  // 미래에 약속된 데이터를 받을 때 까지 기다린다.
  data.wait();

  try {
    data.get();
  } catch (const std::exception& e) {
    std::cout << "예외 : " << e.what() << std::endl;
  }
  t.join();
}
```

#### packaged_task
packaged_task를 사용한다면 위의 코드에서 굳이 promise를 전달하지 않아도 된다.  
packaged_task가 함수의 리턴값을 처리해준다.  
```C++
#include <future>
#include <iostream>
#include <thread>

int some_task(int x) { return 10 + x; }

int main() {  
  std::packaged_task<int(int)> task(some_task); 
  // packaged_task 객체를 비동기적으로 수행할 함수를 인자로 생성
  // packaged_task는 전달된 함수를 실행해서 그 함수의 리턴값을 promise에 set_value함
  // 만약 promise가 예외를 던졌다면 set_exception함
  // 이후는 동일하다

  std::future<int> start = task.get_future();

  std::thread t(std::move(task), 5);

  std::cout << "결과값 : " << start.get() << std::endl;
  t.join();
}
```

#### wait_for()
위에서 wait()는 있어도 없어도 된다고 했었다.  
wait()대신에 wait_for을 사용하면 값이 준비될때까지 기다리는게 아니라 정한 시간만큼만 기다렸다가 리턴하게 만들 수 있다.  
```C++
 while (true) {
    std::future_status status = data.wait_for(std::chrono::seconds(1));

    // 아직 준비가 안됨
    if (status == std::future_status::timeout) {
      std::cerr << ">";
    }
    // promise 가 future 를 설정함.
    else if (status == std::future_status::ready) {
      break;
    }
  }
```

wait_for을 사용하고 난 결과를 future_status에 보관한다

여기엔 총 3가지 상태가 있다.

- future_status::ready : future에 값이 설정 됐을 때

- future_status::timeout : 설정한 시간이 지났지만 값이 설정되지 않아서 리턴됐을 때

- future_status::deferred : 결과값을 계산하는 함수가 채 실행되지 않았을 때

#### shared_future

future이 get()은 이동되기때문에 한번만 써야한다.

하지만 여러 쓰레드에서 future의 값을 get해야 한다면 share_future를 사용한다.

shared_future를 사용하면 모든 복사본들이 하나의 객체를 가리키는 방식이므로 포인터나 레퍼런스를 쓸 필요가 없다.

```
#include <chrono>
#include <future>
#include <iostream>
#include <thread>
using std::thread;

void runner(std::shared_future<void> start) {
  start.get();
  std::cout << "출발!" << std::endl;
}

int main() {
  std::promise<void> p;
  std::shared_future<void> start = p.get_future();

  thread t1(runner, start);
  thread t2(runner, start);
  thread t3(runner, start);
  thread t4(runner, start);

  // 참고로 cerr 는 std::cout 과는 다르게 버퍼를 사용하지 않기 때문에 터미널에
  // 바로 출력된다.
  std::cerr << "준비...";
  std::this_thread::sleep_for(std::chrono::seconds(1));
  std::cerr << "땅!" << std::endl;

  p.set_value();

  t1.join();
  t2.join();
  t3.join();
  t4.join();
}
```

|   |
|---|
|## async|

promise나 packaged_task는 비동기적으로 실행하기 위해 쓰레드를 명시적으로 생성해서 실행해야 했다.

하지만 async를 어떤 함수에 전달하면 알아서 쓰레드를 만들어서 해당 함수를 비동기적으로 실행하고 그 결과를 future에 전달한다.

```
 std::future<int> lower_half_future = std::async(std::launch::async, sum, cref(v), 0, v.size() / 2);
```

위처럼 async함수에 sum함수를 전달하면 알아서 쓰레드를 만들어서 sum함수를 수행하고 그 결과를 future에 보관한다.

첫번째 인자인 std::launch::async는 즉시 쓰레드를 생성해서 인자로 전달된 함수를 실행하라는 의미이다.

반면 std::launch::deferred로 사용하면 future의 get함수가 호출 되었을때 실행하는 방식으로 사실상 동기적으로 실행하게 된다. 즉 비동기적으로 사용하고 싶다면 async를, 동기적으로 사용해도 상관없다면 deferred를 쓴다.

전체 코드

```
#include <future>
#include <iostream>
#include <thread>
#include <vector>

// std::accumulate 와 동일
int sum(const std::vector<int>& v, int start, int end) {
  int total = 0;
  for (int i = start; i < end; ++i) {
    total += v[i];
  }
  return total;
}

int parallel_sum(const std::vector<int>& v) {
  // lower_half_future 는 1 ~ 500 까지 비동기적으로 더함
  // 참고로 람다 함수를 사용하면 좀 더 깔끔하게 표현할 수 도 있다.
  // --> std::async([&v]() { return sum(v, 0, v.size() / 2); });
  std::future<int> lower_half_future =
    std::async(std::launch::async, sum, cref(v), 0, v.size() / 2);

  // upper_half 는 501 부터 1000 까지 더함
  int upper_half = sum(v, v.size() / 2, v.size());

  return lower_half_future.get() + upper_half;
}

int main() {
  std::vector<int> v;
  v.reserve(1000);

  for (int i = 0; i < 1000; ++i) {
    v.push_back(i + 1);
  }

  std::cout << "1 부터 1000 까지의 합 : " << parallel_sum(v) << std::endl;
}
```

parallel_sum 부분이 핵심인데 async로 인해 생긴 쓰레드에서 1 ~ 500까지 비동기적으로 sum한다.

비동기적 쓰레드가 1 ~ 500을 sum할동안 현재 쓰레드는 upper_half에 501 ~ 1000까지 sum하는 작업을 한다.

그 후 두 결과를 합친다.

이를 통해 시간을 절반으로 줄인다.

|   |
|---|
|## 쓰레드풀|

쓰레드풀이란 미리 쓰레드들이 여렇 만들어져서 대기하고 있다가, 할일이 들어오면 대기하던 쓰레드 중 하나가 이를 받아서 실행하는 개념이다.

정말 넘모 어렵다.

잘 작성된 코드만 일단 첨부해두고 나중에 실력이 쌓이고 더 이해가 깊어지면 아래 글 읽어보기로 하자

[https://modoocode.com/285](https://modoocode.com/285)

위 글에서 완성된 ThreadPool 구현 예시

```
#include <chrono>
#include <condition_variable>
#include <cstdio>
#include <functional>
#include <future>
#include <mutex>
#include <queue>
#include <thread>
#include <vector>

namespace ThreadPool {
	class ThreadPool {
	public:
		ThreadPool(size_t num_threads);
		~ThreadPool();

		// job 을 추가한다.
		template <class F, class... Args>
		std::future<typename std::result_of<F(Args...)>::type> EnqueueJob(
			F&& f, Args&&... args);

	private:
		// 총 Worker 쓰레드의 개수.
		size_t num_threads_;
		// Worker 쓰레드를 보관하는 벡터.
		std::vector<std::thread> worker_threads_;
		// 할일들을 보관하는 job 큐.
		std::queue<std::function<void()>> jobs_;
		// 위의 job 큐를 위한 cv 와 m.
		std::condition_variable cv_job_q_;
		std::mutex m_job_q_;

		// 모든 쓰레드 종료
		bool stop_all;

		// Worker 쓰레드
		void WorkerThread();
	};

	ThreadPool::ThreadPool(size_t num_threads)
		: num_threads_(num_threads), stop_all(false) {
		worker_threads_.reserve(num_threads_);
		for (size_t i = 0; i < num_threads_; ++i) {
			worker_threads_.emplace_back([this]() { this->WorkerThread(); });
		}
	}

	void ThreadPool::WorkerThread() {
		while (true) {
			std::unique_lock<std::mutex> lock(m_job_q_);
			cv_job_q_.wait(lock, [this]() { return !this->jobs_.empty() || stop_all; });
			if (stop_all && this->jobs_.empty()) {
				return;
			}

			// 맨 앞의 job 을 뺀다.
			std::function<void()> job = std::move(jobs_.front());
			jobs_.pop();
			lock.unlock();

			// 해당 job 을 수행한다 :)
			job();
		}
	}

	ThreadPool::~ThreadPool() {
		stop_all = true;
		cv_job_q_.notify_all();

		for (auto& t : worker_threads_) {
			t.join();
		}
	}

	template <class F, class... Args>
	std::future<typename std::result_of<F(Args...)>::type> ThreadPool::EnqueueJob(
		F&& f, Args&&... args) {
		if (stop_all) {
			throw std::runtime_error("ThreadPool 사용 중지됨");
		}

		using return_type = typename std::result_of<F(Args...)>::type;
		auto job = std::make_shared<std::packaged_task<return_type()>>(
			std::bind(std::forward<F>(f), std::forward<Args>(args)...));
		std::future<return_type> job_result_future = job->get_future();
		{
			std::lock_guard<std::mutex> lock(m_job_q_);
			jobs_.push([job]() { (*job)(); });
		}
		cv_job_q_.notify_one();

		return job_result_future;
	}

}  // namespace ThreadPool

// 사용 예시
int work(int t, int id) {
	printf("%d start \n", id);
	std::this_thread::sleep_for(std::chrono::seconds(t));
	printf("%d end after %ds\n", id, t);
	return t + id;
}

int main() {
	ThreadPool::ThreadPool pool(3);

	std::vector<std::future<int>> futures;
	for (int i = 0; i < 10; i++) {
		futures.emplace_back(pool.EnqueueJob(work, i % 3 + 1, i));
	}
	for (auto& f : futures) {
		printf("result : %d \n", f.get());
	}
}
```

※ 참고 문헌

[https://modoocode.com/269](https://modoocode.com/269)

[https://modoocode.com/270](https://modoocode.com/270)

[https://modoocode.com/271](https://modoocode.com/271)

[https://modoocode.com/284](https://modoocode.com/284)

[https://modoocode.com/285](https://modoocode.com/285)