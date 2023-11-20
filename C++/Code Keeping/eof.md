# eof

알고리즘 문제에서 입력값의 범위가 주어지지 않는 경우가 있다.  

이는 파일이 종료될때까지 입력을 받으라는 의미이므로, cin.eof()를 사용하면 된다.  
bool타입을 가지며 파일의 끝을 의미하는 EOF를 읽게 되면, true를 return한다.  
콘솔 창에서는 EOF를 수동으로 넣어주어야 한다. 이때, 윈도우 기준 ctrl+z가 EOF를 의미한다.  

```C++
while (!cin.eof()) {} // 문자라면  
while (getline(cin, input)) {} // 문장이라면
```

#CodeKeeping