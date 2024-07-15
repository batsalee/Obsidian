# filesystem과 fstream 라이브러리


## 1. filesystem과 fstream 라이브러리

#### 1) filesystem 라이브러리
파일에 관한 정보에 대한 접근을 도와주는 기능 제공  
##### 예시)  
파일 a.txt가 있는지 없는지 확인  
디렉터리를 만들고 삭제하는 기능  
파일의 생성 시간이나 권한같은것을 확인하는 기능  

단, 파일 자체를 읽고 쓰는 역할을 하지는 않음  

#### 2) fstream 라이브러리
파일 입출력 라이브러리  
파일 하나의 내용을 읽고, 파일에 내용을 쓰는 기능 제공  
파일의 내용을 건드는것이지 파일에 관한 정보(파일이름, 위치 등)는 건들 수 없음  
ofstream으로 파일을 만들순 있지만 디렉터리를 만들수는 없음  


## 2. filesystem 라이브러리

```C++  
#include <filesystem>
#include <iostream>

int main() {
  std::filesystem::path p("./some_file");

  std::cout << "내 현재 경로 : " << std::filesystem::current_path() << std::endl;
  std::cout << "상대 경로 : " << p.relative_path() << std::endl;
  std::cout << "절대 경로 : " << std::filesystem::absolute(p) << std::endl;
  std::cout << "공식적인 절대 경로 : " << std::filesystem::canonical(p) << std::endl;
  
  std::cout << "Does " << p << " exist? [" << std::boolalpha
            << std::filesystem::exists(p) << "]" << std::endl;
  std::cout << "Is " << p << " file? [" << std::filesystem::is_regular_file(p)
            << "]" << std::endl;
  std::cout << "Is " << p << " directory? [" << std::filesystem::is_directory(p)
            << "]" << std::endl;            
}
```
#### 1) 경로 관련
`std::filesystem::path`로 파일경로 객체를 만들고  
`std::filesystem::current_path()`로 내 현재 경로를 확인할 수 있고  
`p.relative_path()`로 현재 경로로부터 해당 객체의 상대경로를 확인할 수 있고  
`std::filesystem::absolute(p)`로 해당 파일의 절대 경로를 확인할 수 있고  
`std::filesystem::canonical(p)`로 공식적인 절대 경로를 확인할 수 있음  

※ absolute와 canonical의 차이는 .이나 ..같은 정보가 포함 되냐 안되냐의 차이(canonical은 . .. 없이 깔끔하게 나옴)

#### 2) 파일정보 확인 관련
`std::filesystem::exists()`로 해당 객체가 존재하는지 확인할 수 있으며  
`std::filesystem::is_regular_file()`로 해당 객체가 파일인지 확인할 수 있으며  
`std::filesystem::is_directory()`로 해당 객체가 폴더인지 확인할 수 있음  

#### 3) 파일 순회(directory_iterator)
디렉터리 안의 모든 파일을 순회하기 위해서는 directory_iterator를 사용  
```C++
#include <filesystem>
#include <iostream>

namespace fs = std::filesystem;

int main() {
  fs::directory_iterator itr(fs::current_path() / "a"); // 현재경로 밑의 a폴더
  while (itr != fs::end(itr)) {
    const fs::directory_entry& entry = *itr;
    std::cout << entry.path() << std::endl;
    itr++;
  }
}

/* 또는 아래처럼 range based for로 사용 가능
int main() {
  for (const fs::directory_entry& entry :
       fs::directory_iterator(fs::current_path() / "a")) {
    std::cout << entry.path() << std::endl;
  }
}
*/
```
STL컨테이너들은 begin()함수를 사용하면 시작 itr을 편하게 정의할 수 있었는데  
directory_iterator는 begin()함수는 없다고 함  
그래서 직접 반복자를 정의해줘야 하고 그 문구가  fs::directory_iterator itr(fs::current_path() / "a");  

/ "a"의 경우 path에 operator / 가 정의되어있어서 위처럼 path() / "a"라고 하면 현재 경로에 있는 a 디렉터리 안에 있는 파일들을 탐색하게 됨  

begin()뿐 아니라 그냥 깔끔한 end()도 없기때문에 `itr != std::filesystem::end(itr)`처럼 사용해서 끝이 아닐때까지 반복  

itr이 가리키는것은 디렉터리에 정의된 개개의 파일을 나타내는 directory_entry를 가리키며  
directory_entry에는 파일의 이름이나 크기 등의 여러 정보들이 저장되어있음  

단, directory_iterator의 경우 해당 디렉터리 안에 다른 디렉터리가 또 있다면 그 안까지는 확인하지 않음  
> [!note] 디렉터리 안의 디렉터리 순회
>
> 디렉터리 안의 디렉터리까지 모두 보고 싶다면 recursive_directory_iterator를 사용

> [!warning] directory_iterator 사용시 주의점
>
> 디렉터리에서 파일이 삭제된다거나 하면 iterator가 무효화됨  
> 그러므로 .txt파일을 모두 삭제하기 위해 순회하면서 지운다면 remove 하나 할때마다 iterator를 다시 얻어야 함

#### 4) 디렉터리 생성
```C++
std::filesystem::path p("./a/c");
std::filesystem::create_directory(p);
```
위처럼 사용 가능한데 create_directory는 반드시 생성하고자 하는 부모디렉터리가 이미 있는 상태에서 사용해야함  
위의 코드에서는 c디렉터리를 만들려고 하므로 a디렉터리는 이미 있어야 함  

> [!note] 만약 부모디렉터리까지 한번에 다 만들고 싶다면
> 
> create_directories를 사용  
> ```C++
> std::filesystem::path p("./c/d/e/f");
> std::filesystem::create_directories(p);
> ```
  

#### 5)  파일 및 폴더 복사
```C++
#include <filesystem>

int main() {
  std::filesystem::path from("./a");
  std::filesystem::path to("./c");

  std::filesystem::copy(from, to, std::filesystem::copy_options::recursive);
}
```

copy함수를 사용하면 쉽게 가능  
recursive 옵션을 주면 해당 디렉터리 하위 디렉터리의 모든 파일까지 다 복사해줌  
recursive를 주지 않으면 딱 안에 있는 파일만 주고 하위 디렉터리들은 복사해주지 않음  

만약 복사해주는 목적지에 이미 같은 이름의 파일이 존재한다면 3가지 선택지가 있음
- skip_existing : 이미 존재하는 파일은 무시
- overwite_existing : 덮어씌우기
- update_existing : 이미 존재하는 파일이 더 오래된것이라면 덮어씌우기

#### 6) 파일 및 폴더 삭제
```C++
std::filesystem::remove(p);
```
파일경로를 주면 파일을 지워주고 디렉터리 경로를 주면 디렉터리를 지워줌  

단 디렉터리 하위에 파일이 있다면 사용불가  
>[!note] 디렉터리 하위 파일까지 모두 강제로 지우고 싶다면 remove_all 사용
>
> `std::filesystem::remove_all(p);`


## 3. fstream 라이브러리

iostream의 하위 클래스인 istream이나 ostream은 자주 사용하던 표준입출력이고  
fstream은 키보드나 화면이 아닌 파일에 입출력을 하는 것  

ifstream은 istream 상속받았고, ofstream은 ostream을 상속받았음  
둘 다 fstream 라이브러리로 사용하면 되고  
파일로부터 읽는다면 ifstream을, 파일에 쓴다면 ofstream을 사용    

#### 1) 파일에서 읽어오기
```C++
// 파일에서의 입출력
#include <fstream>
#include <iostream>
#include <string>

int main() {
  // 파일 읽기 준비
  std::ifstream in("test.txt");
  std::string s;

  if (in.is_open()) {
    in >> s;
    std::cout << "입력 받은 문자열 :: " << s << std::endl;
  } 
  else {
	std::cout << "파일을 찾을 수 없습니다!" << std::endl;
  }
  
  return 0;
}
```
파일에서 읽어오기 위해 ifstream객체를 만들고, 읽어온 문자를 저장할 string도 만듬  
그 후 if문으로 파일이 잘 열렸는지 is_open()으로 반드시 확인한 후에 위처럼 사용    

파일의 경로는 기본적으로 visual studio에서 실행한다면 소스파일과 같은 경로에 있는것을 찾음  
그 외 경로를 지정하려면 상대경로나 절대경로를 활용하면 됨  

##### 1-1) 파일에서 문자열로 받아와서 string에 넣으려면
```C++
std::string s;
in >> s;
```
  
##### 1-2) 몇글자만 받고싶다면
```C++
char x[10];
in.read(x, 10);
```
  
##### 1-3) 한줄씩 읽고 싶다면
```C++
#include <string> // string의 getline을 활용

std::string s;
getline(in, s);
```
  
##### 1-4) 파일 전체 한번에 읽으려면
```C++
#include <fstream>
#include <iostream>
#include <string>

int main() {
  // 파일 읽기 준비
  std::ifstream in("test.txt");
  std::string s;

  if (in.is_open()) {
    in.seekg(0, std::ios::end); // 위치 지정자를 파일 끝으로 옮긴다.    
    int size = in.tellg(); // 그리고 그 위치를 읽는다. (파일의 크기)
    s.resize(size); // 그 크기의 문자열을 할당한다.   
    in.seekg(0, std::ios::beg); // 위치 지정자를 다시 파일 맨 앞으로 옮긴다.

    // 파일 전체 내용을 읽어서 문자열에 저장한다.
    in.read(&s[0], size);
    std::cout << s << std::endl;
  } 
  else {
    std::cout << "파일을 찾을 수 없습니다!" << std::endl;
  }

  return 0;
}
```
  
★ C계열의 파일 입출력에선 file* 를 만들면 반드시 close()를 했어야 했지만  
ifstream의 경우 소멸자에서 close를 알아서 해주기 때문에 안해도 되므로 매우 편함  
다만 위의 in객체를 다른 파일로 사용하고 싶다면  
```C++
in.close();
in.open("other.txt");
```
위처럼 close 사용도 충분히 가능

#### 2) 파일에 쓰기
```C++
#include <iostream>
#include <fstream>
#include <string>

int main() {
  // 파일 쓰기 준비
  std::ofstream out("test.txt"); // 옵션없이 사용하면 기존 내용 모두 지워지므로 주의
  std::string s;
  
  if (out.is_open()) {
    out << "파일에 해당 내용 작성";
  }

  return 0;
}
```

>[!warning] 주의사항
>
> test.txt가 존재하지 않는다면 test.txt를 생성한 뒤에 출력함  
> 반면 test.txt가 이미 존재한다면 옵션지정 안하면 원래 내용을 다 지워버리고 해당 내용을 작성함
> 그러므로 옵션을 적절히 설정해줘야 함
##### 출력 옵션 지정 방법
```C++
std::ofstream out("test.txt", std::ios::app); // 이런식으로 옵션 사용
  
ios::trunc // 기본값이라고 보면 되고 파일스트림을 여는 순간 내용이 모두 지워짐
ios::binary // 값을 바이너리로 읽고 씀
ios::app // 뒤에 이어붙여서 씀
ios::ate // 파일을 열때 위치지정자가 끝을 가리키고 있게함(자동으로 파일의 끝부터 읽고 씀)
ios::in, ios::out // ifstream이라면 in이 적용되어 있고, ofstream이라면 out이 적용되어있음
                  // 만약 fstream으로 만든다면 둘 중 하나를 적어줘야할듯?
                  
// app과 ate의 차이점
// app은 원문은 절대 건드리지 않음
// ate는 위치지정자를 옮길 수 있으므로 이전으로 옮기면 원문을 건들수있음
```
  
만약 문자열이라면 그냥 `out << "...";` 처럼 쓰면 되지만 직접 만든 객체의 내용을 파일에 저장하려면 `operator<<`를 구현해야 함  
```C++
#include <fstream>
#include <iostream>
#include <string>

class Human {
  std::string name;
  int age;

 public:
  Human(const std::string& name, int age) : name(name), age(age) {}
  std::string get_info() {
    return "Name :: " + name + " / Age :: " + std::to_string(age);
  }

  friend std::ofstream& operator<<(std::ofstream& o, Human& h);
};

std::ofstream& operator<<(std::ofstream& o, Human& h) {
  o << h.get_info();
  return o;
}

int main() {
  // 파일 쓰기 준비
  std::ofstream out("test.txt");

  Human h("이재범", 60);
  out << h << std::endl;

  return 0;
}
```
  

  

  

※ 참고 문헌
[https://modoocode.com/306](https://modoocode.com/306)