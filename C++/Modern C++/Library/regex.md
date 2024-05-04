# regex


## 1. 정규표현식이란  

아래의 글에 정규표현식의 개념과 문법에 대해 정리해뒀음  
[[정규표현식]]


## 2. C++에서의 정규표현식

C++ 11부터 정규표현식 라이브러리인 regex를 지원한다.  
자세한 클래스 구성과 함수등은 [https://cplusplus.com/reference/regex/](https://cplusplus.com/reference/regex/)에서 확인 가능하고  
여기엔 대표적인 몇가지 기능만 기록해둠  

#### 대표적인 기능
- regex_match : **문자열 전체**가 정규표현식 패턴에 부합하는지 확인할때 사용한다.
			캡쳐 그룹을 사용해 특정 문자열만 분리할 때
- regex_search : 주어진 문자열에서 정규표현식 패턴과 일치하는 **부분**이 있는지 검색할 때
- regex_replace : 주어진 문자열에서 원하는 패턴의 문자열을 다른 특정 패턴의 문자열로 치환할 때

#### 1) C++에서 정규표현식 사용시 주의점
- C++에서는 \(백슬래쉬) 사용시 2번 사용해야 하는 점, 그러므로 \d같은건 c++에선 \\d로 사용해야 한다.
- .의 경우 임의의 한 문자를 의미하지만 C++에서 그냥 .으로 사용시 문자 '.'으로 인식하므로 \.으로 사용해야 한다.

물론 그냥 Raw string literal를 사용해버리는 방법도 있다.  
Raw string literal은 [https://modoocode.com/292#page-heading-5](https://modoocode.com/292#page-heading-5)에 자세히 설명되어 있다.  

#### 2) regex 객체
```C++
std::regex re("db-\\d*-log\\.txt");
```
위처럼 regex객체로 정규표현식을 정의할 수 있다.

다만 정규표현식은 문법의 종류나 처리하는 엔진이 여러가지가 있어서 다른 정규표현식 문법을 사용하고 싶다면 추가 인자로 전달 가능하다.  
만약 grep 정규표현식 문법을 사용하고 싶다면 아래처럼 사용하면 된다.
```C++
std::regex re("db-\\d*-log\\.txt", std::regex::grep);
```
참고로 인자를 전달하지 않았을 경우 default 값은 `std::regex::ECMAScript`이다.  

또한 두가지 이상의 인자를 전달하고 싶은 경우 | 로 묶어서 사용 가능하다.  
```C++
std::regex re("db-\\d*-log\\.txt", std::regex::grep | std::regex::icase);
```
참고로 `std::regex::icase`는 대소문자를 구분하지 않게하는 옵션이다.  

정규표현식의 성능이 중요한 경우에는 `std::regex::optimize`옵션을 사용시  
regex 객체 생성에는 시간이 좀 더 걸리지만, 객체 사용작업은 더 빠르게 수행 가능하다.

#### 3) regex_match
문자열 전체가 정규표현식 패턴에 부합하는지 확인할때 사용한다.  
regex_match(문자열, regex객체) 형태로 사용한다.  

또한 문자열 내에서 특정 부분만 분리할 때도 사용 가능하다.  
캡쳐 그룹과 smatch등의 객체를 활용한다.  
```C++
#include <iostream>
#include <regex>
#include <vector>

int main() {
    // 주어진 파일 이름들.
    std::vector<std::string> file_names = { 
        "db-123-log.txt", "db-124-log.txt",
        "not-db-log.txt", "db-12-log.txt",
        "db-12-log.jpg" 
    };
    
    std::regex re("db-\\d*-log\\.txt");
    for (const auto& file_name : file_names) {
        std::cout << file_name << ": " << std::boolalpha
            << std::regex_match(file_name, re) << '\n';
    }
}
```

결과) 
![](https://blog.kakaocdn.net/dn/bsY6y8/btsHcblp1rI/uPm3Sy5TVEJouHnPpQW1Bk/img.png)

#### 4) 문자열에서 특정부분 분리하기
이번엔 특정 문자열만 분리하는 경우를 살펴보자.  
분리할 부분을 ()로 감싸주면 된다.
```C++
#include <iostream>
#include <regex>
#include <vector>

int main() {
    std::vector<std::string> phone_numbers = { 
        "010-1234-5678", "010-123-4567",
        "011-1234-5567", "010-12345-6789",
        "123-4567-8901", "010-1234-567"
    };

    std::regex re("[01]{3}-(\\d{4})-\\d{4}"); // 캡처할 부분을 ()로 감싸줌
    std::smatch match;  // 매칭된 결과를 string 으로 보관
    for (const auto& number : phone_numbers) {
        if (std::regex_match(number, match, re)) {
            for (size_t i = 0; i < match.size(); i++) {
                std::cout << "Match : " << match[i].str() << std::endl;
            }
            std::cout << "-----------------------\n";
        }
    }
}
```
위처럼 분리하고자 하는 부분을 ()로 감싸주고, smatch나 cmatch등의 객체(자세한건 document 참고)를 사용한다.  
regex(문자열, match객체, regex객체) 형태로 사용시 문자열이 regex조건에 부합한다면 match객체에 ()로 감싼부분이 저장된다.  
위의 경우 smatch를 사용하므로 string형태로 사용되고 `match[0]`에는 원본문자열이, `match[1]`에는 ()로 캡쳐된 부분이 저장된다.  
만약 정규표현식 안에 ()로 캡쳐한게 여러개라면 `match[2]`, `match[3]`에 쭉쭉 저장된다.

결과) 
![](https://blog.kakaocdn.net/dn/dnxvoG/btsHb8912Ds/259WKiqkmUBhtjfQblH7cK/img.png)

#### 5) regex_search
위의 regex_match는 문자열 전체가 패턴에 부합하는지 확인하는 함수였다.  
regex_search는 전체가 아닌 패턴을 만족하는 문자열 일부가 있는지 검색하는 함수이다.  

regex_search(문자열, match객체, regex객체)로 사용하면 문자열에서 정규표현식에 맞은 부분들이 match에 저장된다.  
매칭되는 패턴이 존재한다면 true가 리턴되며 매칭된 패턴은 match객체에 저장된다.  
```C++
#include <iostream>
#include <regex>

int main() {
  std::string html = R"(
        <div class="social-login">
          <div class="small-comment">다음으로 로그인 </div>
          <div>
            <i class="xi-facebook-official fb-login"></i>
            <i class="xi-google-plus goog-login"></i>
          </div>
        </div>
        <div class="manual">
          <div class="small-comment">
            또는 직접 입력하세요 (댓글 수정시 비밀번호가 필요합니다)
          </div>
          <input name="name" id="name" placeholder="이름">
          <input name="password" id="password" type="password" placeholder="비밀번호">
        </div>
        <div id="adding-comment" class="sk-fading-circle">
          <div class="sk-circle1 sk-circle">a</div>
          <div class="sk-circle2 sk-circle">b</div>
          <div class="sk-circle3 sk-circle">asd</div>
          <div class="sk-circle4 sk-circle">asdfasf</div>
          <div class="sk-circle5 sk-circle">123</div>
          <div class="sk-circle6 sk-circle">aax</div>
          <div class="sk-circle7 sk-circle">sxz</div>
        </div>
  )";

  std::regex re(R"(<div class="sk[\w -]*">\w*</div>)");
  std::smatch match;
  while (std::regex_search(html, match, re)) {
    std::cout << match.str() << '\n';
    html = match.suffix(); // 검색된 위치의 다음부분으로 반복자를 조정해주는 역할
  }
}
```
html = match.suffix()의 역할은 regex_search를 여러번 사용해봤자 처음 패턴에 일치한 문자열을 계속 return하므로 첫 문장만 계속해서 return된다.  
그러므로 search에서 true가 return된다면 해당 위치의 다음 위치부터 search를 시작하게 만들어야 하며
match.suffix()를 하면 std::sub_match객체를 리턴해서 위치를 조정할 수 있다.  
sub_match객체는 이전 검색된 결과의 바로 뒤 위치의 itr과 문자열의 끝에 해당하는 itr을 가지고 있으므로 검색된 위치의 바로 뒤부터 다시 검색이 가능하게 만들어준다.  

suffix가 아닌 iterator를 사용하는 방법도 있다.  
```C++
#include <iostream>
#include <regex>

int main() {
  std::string html = R"(
        <div class="social-login">
          <div class="small-comment">다음으로 로그인 </div>
          <div>
            <i class="xi-facebook-official fb-login"></i>
            <i class="xi-google-plus goog-login"></i>
          </div>
        </div>
        <div class="manual">
          <div class="small-comment">
            또는 직접 입력하세요 (댓글 수정시 비밀번호가 필요합니다)
          </div>
          <input name="name" id="name" placeholder="이름">
          <input name="password" id="password" type="password" placeholder="비밀번호">
        </div>
        <div id="adding-comment" class="sk-fading-circle">
          <div class="sk-circle1 sk-circle">a</div>
          <div class="sk-circle2 sk-circle">b</div>
          <div class="sk-circle3 sk-circle">asd</div>
          <div class="sk-circle4 sk-circle">asdfasf</div>
          <div class="sk-circle5 sk-circle">123</div>
          <div class="sk-circle6 sk-circle">aax</div>
          <div class="sk-circle7 sk-circle">sxz</div>
        </div>
  )";

  std::regex re(R"(<div class="sk[\w -]*">\w*</div>)");
  std::smatch match;

  auto start = std::sregex_iterator(html.begin(), html.end(), re);
  auto end = std::sregex_iterator();

  while (start != end) {
    std::cout << start->str() << std::endl;
    ++start;
  }
}
```
위처럼 사용시 regex_iterator가 주어진 문자열에서 정규 표현식으로 매칭된 문자열들을 가리키게 되고 ++연산자로 범위를 조정해가면서 사용할 수 있다.  
또한 `*itr`로 값을 뽑아낼 수 있으므로 `(*itr).str()`와 같은 `itr->str()`를 사용해서 값을 확인할 수 있다.

#### 6) regex_replace
원하는 패턴의 문자열을 다른 문자열로 치환하는 함수이다.  

예를들어 sk-circle1을 1-sk-circle로 변환하고 싶다면  
즉 "sk-circle\d" 를 "숫자-sk-circle"로 변환하고 싶다면  
`sk-circle(\d)`로 숫자부분을 캡처하고, 치환할때 첫번째 캡처된 값임을 표시하는 $1을 사용하면 된다.  

즉 `sk-circle(\d)`를 `$1-sk-circle`로 변환하면 된다.  
이런 $를 back reference라고 부른다고 한다.

regex_replace(문자열, 치환전문자열형식이 담긴 regex객체, 치환후문자열형식)으로 사용하면 된다.  
```C++
#include <iostream>
#include <regex>

int main() {
  std::string html = R"(
        <div class="social-login">
          <div class="small-comment">다음으로 로그인 </div>
          <div>
            <i class="xi-facebook-official fb-login"></i>
            <i class="xi-google-plus goog-login"></i>
          </div>
        </div>
        <div class="manual">
          <div class="small-comment">
            또는 직접 입력하세요 (댓글 수정시 비밀번호가 필요합니다)
          </div>
          <input name="name" id="name" placeholder="이름">
          <input name="password" id="password" type="password" placeholder="비밀번호">
        </div>
        <div id="adding-comment" class="sk-fading-circle">
          <div class="sk-circle1 sk-circle">a</div>
          <div class="sk-circle2 sk-circle">b</div>
          <div class="sk-circle3 sk-circle">asd</div>
          <div class="sk-circle4 sk-circle">asdfasf</div>
          <div class="sk-circle5 sk-circle">123</div>
          <div class="sk-circle6 sk-circle">aax</div>
          <div class="sk-circle7 sk-circle">sxz</div>
        </div>
  )";

  std::regex re(R"r(sk-circle(\d))r");
  std::smatch match;

  std::string modified_html = std::regex_replace(html, re, "$1-sk-circle");
  std::cout << modified_html;
}
```

결과)
![](https://blog.kakaocdn.net/dn/eeXluQ/btsHcLGys06/qOGir4y3Wvf6PWPgyPVXIk/img.png)

#### 7) 중첩된 캡쳐 그룹

만약 "sk-circle1 sk-circle"을 "1-sk-circle"로 대체하고 싶다면 

단지 1을 (\d)로 캡처하는  것 뿐 아니라 뒤에 붙은 sk-circle도 날려버려야 하므로 캡처해줘야 한다.

그러므로 (sk-circle(\d) sk-circle)로 문자열 전체를 캡처하면서 숫자부분도 캡처해줘야 한다.

이때 back reference는 괄호가 열리는 순서대로 번호가 붙기때문에 가장 먼저 열린 괄호에 해당하는

$1은 문자열 전체를 캡처하고, $2는 \d부분을 캡처하게 된다.

즉 결과로는 $2-sk-circle을 만들면 되는 것

```
#include <iostream>
#include <regex>

int main() {
	std::string html = R"( 
          <div class="sk-circle1 sk-circle">a</div>
          <div class="sk-circle2 sk-circle">b</div>
          <div class="sk-circle3 sk-circle">asd</div>
          <div class="sk-circle4 sk-circle">asdfasf</div>
          <div class="sk-circle5 sk-circle">123</div>
          <div class="sk-circle6 sk-circle">aax</div>
          <div class="sk-circle7 sk-circle">sxz</div>
  )";

	std::regex re(R"r((sk-circle(\d) sk-circle))r");
	std::smatch match;

	std::string modified_html = std::regex_replace(html, re, "$2-sk-circle");
	std::cout << modified_html;
}
```

결과)

![](https://blog.kakaocdn.net/dn/ctcDmL/btsHa234wsr/Grb0l6QF7voGVCJCuIL000/img.png)

※ 참고 문헌

[https://maloveforme.tistory.com/196](https://maloveforme.tistory.com/196)

[https://modoocode.com/303](https://modoocode.com/303)

[https://cplusplus.com/reference/regex/](https://cplusplus.com/reference/regex/)