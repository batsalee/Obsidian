# msvc와 MinGW

Qt를 처음 써볼떄 컴파일러가 2종류 있길래 둘 다 다운받아서 써보곤 했었다.  
사용 경험도 좀 생기면서 둘의 차이가 궁금해졌고 학습해둔 내용을 기록해둔다.  

## 1. 컴파일러

소스코드 -> (컴파일러) -> 중간코드 -> (링커) -> 실행코드  
즉 컴파일러가 달라지면 중간코드가 달라진다.  

컴파일러의 대표적인 종류로는 GCC, G++, MSVC 등이 있다.  
MinGW는 GCC의 변형이라고 보면 될 듯 하다.  

## 2. GCC

GNU Compiler Collection의 약자이며 오픈소스 컴파일러 컬렉션이다.  
GNU는 UNIX에서 호환되는 모든 사람이 자유롭게 사용할 수 있게 개발된 라이센스이다.  

## 3. MinGW

Minimalist GNU for Windows의 약자이며 GCC for Windows라고 생각하면 된다.  
즉 Windows에서 UNIX계열의 컴파일러인  GCC를 사용할 수 있게 해주는 Tool이다.  

## 4. MSVC

Microsoft Visual C++ 컴파일러이다.  
Window에서 C++ IDE로 대표적인 Visual Studio에 탑재되어 있는 컴파일러인데 이름을 잘 보면 C 컴파일러가 아니라 C++ 컴파일러인것을 볼 수 있다.  
C++이 C의 문법을 포함하기때문에 C도 컴파일 할 수 있긴 하지만 완벽하게 포함하는 것은 아니라 C언어 지원에 문제가 있을 수 있다.  

## 5. 차이점

GCC는 기본적으로 UNIX/Linux 계열의 컴파일러이다.  
MSVC는 Windows의 컴파일러이다.  

또한 특정 API들은 컴파일러에 의존된다.  
그런데 크로스 플랫폼 개발환경에서는 컴파일러에 의존해서 API사용이 OS마다 다르다면 곤란하다.  
이런 면에서 GCC가 MSVC보다 뛰어나다.  
GCC는 MSVC보다 표준을 더 잘 지키고 개발기간이 길어서 표준화가 더 잘되어있고 GCC를 Windows에서 쓸 수 있는 MinGW같은것도 있으니 선택순위가 더 높다.  

다만 MinGW의 경우 Minimalist GNU for Windows라는 이름만 봐도 알겠지만 Cygwin컴파일러에서 미니멀하게 개선된 컴파일러인데 그렇다보니 일부 기능들은 지원하지 않을 수 있다.  
C++ 11의 Thread를 지원하지 않는다는 말이 있는데 지금은 개선되었는지 모르겠다.  

결론. MinGW는 더 가볍고 표준가 잘 되어있다. 하지만 일부 지원하지 않는 기능이 있을 수 있다.  






※ 참고 문헌  
[https://ikcoo.tistory.com/233](https://ikcoo.tistory.com/233)  
[https://kldp.org/node/143951](https://kldp.org/node/143951)