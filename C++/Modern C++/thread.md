
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