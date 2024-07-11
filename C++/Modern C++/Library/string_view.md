# string_view

C++ 17에 도입된 새로운 문자열 타입  
문자열 데이터를 복사하지 않고 내부적으로 포인터와 길이정보만 사용해서 읽기 전용으로 제공한다.  

## 특징
- 읽기 전용 : 수정불가능
- 복사하지 않음 : 처리성능이 좋음
- 메모리 안정성 : 데이터를 복사하지 않으니 메모리 안정성을 보장함
- 호환성 : 다른 문자열 타입과 호환되는 타입으로 변환 가능

## 사용 예시
문자열 검색, 분석, 비교 등의 작업에서 사용 가능

#### 1) 문자열 검색
객체.find(내용);으로 index를 return받는다.  
```C++
#include <iostream>
#include <string_view>
using namespace std;

int main() {
    string_view view("Hello, world!");
    size_t index = view.find("world");
    
    if (index != string_view::npos) cout << "Found 'world' at index " << index;
    else cout << "Not found";
    
    return 0;
}
```
위의 경우 Found 'world' at index 7 이라고 출력된다.  

#### 2) 문자열 분석
그냥 string과 마찬가지로 문자열 내에서 탐색하듯 사용할 수 있다.  
```C++
#include <iostream>
#include <string_view>
#include <cstring>
using namespace std;

int main() {
    string_view view("Hello, world!");
    const char* prefix = "Hello";
    const char* suffix = "!";
    
    bool has_prefix = strncmp(view.data(), prefix, strlen(prefix)) == 0;
    bool has_suffix = strncmp(view.data() + view.size() - strlen(suffix), suffix, strlen(suffix)) == 0;
    
    if (has_prefix && has_suffix) cout << "Found prefix and suffix";
    else cout << "Not found";

    return 0;
}
```
위의 경우 접두사와 접미사를 문자열과 비교해서 맞는지 확인하고 Found prefix and suffix가 출력된다.  

#### 3) 문자열 비교
객체.compare(비교대상객체);  
```C++
#include <iostream>
#include <string_view>
using namespace std;

int main() {
    string_view view1("Hello, world!");
    string_view view2("Hello");
    
    int result = view1.compare(view2);
    if (result == 0) cout << "Strings are equal";
    else if (result < 0) cout << "First string is less than second string";
    else cout << "First string is greater than second string";

    return 0;
}
```
위처럼 사용시 "First string is greater than second string"이 출력된다.

## 멤버 함수
- at : 인덱스 위치에 있는 문자를 반환, out_of_range인 경우 예외 발생
- `operator[]` : 인덱스 위치에 있는 문자를 반환, 범위를 벗어나도 예외 발생시키지 않음
- front, back : 제일 앞글자, 뒷글자 반환
- data : 문자열 데이터를 가리키는 포인터 반환
- size : 문자열 길이 반환
- length : 문자열 길이 반환
- empty : 문자열 비어있는지 유무 반환
- remove_prefix : 접두사 제거
- remove_suffix : 접미사 제거
- substr : 문자열 일부 추출
- copy : 문자열 일부 복사
- swap : 두개의 string_view 객체 교환
- find : 문자열 검색
- rfind : 문자열을 뒤에서부터 검색
- find_first_of : 처음으로 해당 문자가 나오는 위치 검색
- find_last_of : 마지막으로 해당 문자가 나오는 위치 검색
- find_first_not_of : 문자열 중 해당 문자가 아닌 문자가 처음으로 나오는 위치 검색
- find_last_not_of : 문자열 중 해당 문자가 아닌 문자가 마지막으로 나오는 위치 검색
- compare : 문자열 비교






※ 참고 문헌
[https://todamfather.tistory.com/95](https://todamfather.tistory.com/95)