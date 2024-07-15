# Raw String Literal

문자열을 그대로 사용하도록 만든다.  
예를들어  
그가 말하길 "저쪽이 동쪽이다."  
라는 문장이 있다면 이걸 C++로 처리하려면 아래처럼 처리해야 한다.  
```
string str = "그가 말하길 \"저쪽이 동쪽이다.\"";
```

위 처럼 백슬래시를 사용할 필요 없이 `R"(문자열)"` 로 사용하는게 Raw String Literal이다.  
```
string str = R"(그가 말하길 "저쪽이 동쪽이다.")";
```

심지어 줄바꿈같은것도 아래처럼 사용할 수 있다.  
```
string str1 = "Hello\nWorld";
string str2 = R"(Hello
World)";
                 
// 위의 str1과 str2는 같은 결과를 출력한다.
```

만약 Row String Literal 안에 이스케이프 시퀀스가 있다면 그냥 문자로 취급된다.  
```
string str = R"(Hello\n World)";

// 위의 경우 그냥 Hello\n World 라고 출력된다.
```

다만 Row String Literal의 경우 문자열 안에 ()로 감싸지기 때문에 문자열 내용에 )가 포함되어 있으면 제대로 인식되지 않는다. 주로 컴파일 오류가 난다.  
이를 해결하기 위해 구분자 시퀀스를 사용한다.  
```
R"r(문자열)r";
```
위처럼 ()를 구분자 시퀀스로 감싸준다.  

구분자 시퀀스는 최대 16글자까지 가능하고 문자열 안에 나오지 않는 값으로 정해서 앞과 뒤에 똑같은 문자열로 감싸주면 된다.  
예를들어 아래와 같이 사용된다.
```
string str1 = R"!(1) Raw String Literal 사용법(예시))!"; //R"!(문자열)!";
// 위의 경우 "1) Raw String Literal 사용법(예시)" 로 인식되는 것이다.

string str2 = R"abcdefg(내용(()) 예시)abcdefg"; // 구분자가 abcdefg인것
// 이 경우는 "내용(()) 예시" 라고 인식된다.
```