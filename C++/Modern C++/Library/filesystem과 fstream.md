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

  

2. 파일정보 확인 관련

std::filesystem::exists()로 해당 객체가 존재하는지 확인할 수 있으며

std::filesystem::is_regular_file()로 해당 객체가 파일인지 확인할 수 있으며

std::filesystem::is_directory()로 해당 객체가 폴더인지 확인할 수 있음

  

3. 파일 순회(directory_iterator)

디렉터리 안의 모든 파일을 순회하기 위해서는 directory_iterator를 사용

#include <filesystem>
#include <iostream>

namespace fs = std::filesystem;

int main() {
  fs::directory_iterator itr(fs::current_path() / "a");
  while (itr != fs::end(itr)) {
    const fs::directory_entry& entry = *itr;
    std::cout << entry.path() << std::endl;
    itr++;
  }
}

STL컨테이너들은 begin()함수를 사용하면 시작 itr을 편하게 정의할 수 있었는데

directory_iterator는 begin()함수는 없다고 함

그래서 직접 반복자를 정의해줘야 하고 그 문구가  fs::directory_iterator itr(fs::current_path() / "a");

  

/ "a"의 경우 path에 operator / 가 정의되어있어서 위처럼 path() / "a"라고 하면 현재 경로에 있는 a 디렉터리 안에 있는 파일들을 탐색하게 됨

  

begin()뿐 아니라 그냥 깔끔한 end()도 없기때문에 

itr != std::filesystem::end(itr)

처럼 사용해서 끝이 아닐때까지 반복

  

itr이 가리키는것은 디렉터리에 정의된 개개의 파일을 나타내는 directory_entry를 가리킴

directory_entry에는 파일의 이름이나 크기 등의 여러 정보들이 저장되어있음

그러므로 굳이 itr을 순회하지 않고 range_based_for문을 사용한다면 위 코드는

#include <iostream>

namespace fs = std::filesystem;

int main() {
  for (const fs::directory_entry& entry :
       fs::directory_iterator(fs::current_path() / "a")) {
    std::cout << entry.path() << std::endl;
  }
}

처럼도 사용 가능

  

단, directory_iterator의 경우 해당 디렉터리 안에 다른 디렉터리가 또 있다면 그 안까지는 확인하지 않음

디렉터리 안의 디렉터리까지 모두 보고 싶다면 recursive_directory_iterator를 사용

  

※ directory_iterator 사용시 주의점

디렉터리에서 파일이 삭제된다거나 하면 iterator가 무효화됨

그러므로 .txt파일을 모두 삭제하기 위해 순회하면서 지운다면 remove 하나 할때마다 iterator를 다시 얻어야 함

  

4. 디렉터리 생성

std::filesystem::path p("./a/c");

std::filesystem::create_directory(p);

위처럼 사용 가능한데 create_directory는 반드시 생성하고자 하는 부모디렉터리가 이미 있는 상태에서 사용해야함

위의 코드에서는 c디렉터리를 만들려고 하므로 a디렉터리는 이미 있어야 함

만약 부모디렉터리까지 한번에 다 만들고 싶다면 create_directories를 사용

std::filesystem::path p("./c/d/e/f");

std::filesystem::create_directories(p);

  

5. 파일 및 폴더 복사

#include <filesystem>

int main() {
  std::filesystem::path from("./a");
  std::filesystem::path to("./c");

  std::filesystem::copy(from, to, std::filesystem::copy_options::recursive);
}

copy함수를 사용하면 쉽게 가능

recursive 옵션을 주면 해당 디렉터리 하위 디렉터리의 모든 파일까지 다 복사해줌

recursive를 주지 않으면 딱 안에 있는 파일만 주고 하위 디렉터리들은 복사해주지 않음

  

만약 복사해주는 목적지에 이미 같은 이름의 파일이 존재한다면 3가지 선택지가 있음

- skip_existing : 이미 존재하는 파일은 무시

- overwite_existing : 덮어씌우기

- update_existing : 이미 존재하는 파일이 더 오래된것이라면 덮어씌우기

  

6. 파일 및 폴더 삭제

std::filesystem::remove(p);

파일경로를 주면 파일을 지워주고 디렉터리 경로를 주면 디렉터리를 지워줌

단 디렉터리 하위에 파일이 있다면 사용불가

디렉터리 하위 파일까지 모두 강제로 지우고 싶다면 remove_all 사용

std::filesystem::remove_all(p);