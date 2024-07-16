# stringstream

```C++
string buffer; // 분리된 문자열을 넣는 버퍼

string str1 = "This is Test";
istringstream ss1(str1);
while (getline(ss1, buffer, ' ')) { // 띄어쓰기를 기준으로 분리
	cout << buffer << endl;
}

string str2 = "AB/CDE/DFDG/ASCD";
istringstream ss2(str2);
while (getline(ss2, buffer, '/')) { // '/'을 기준으로 분리
	cout << buffer << endl;
}
```

```C++
string name;
int birth;
bool on;

string temp = "mike 1105 1";
stringstream ss;
ss.str(temp);
ss >> name >> birth >> on;

cout << name << endl << birth << endl << on;

//////////////////////////////////////////

// 갯수가 정해져있다면 이런식으로도 가능
istringstream ss(input);
string split[3]; 
for (int i = 0; i < 3; i++) {  
	getline(ss, split[i], '/'); 
	cout << split[i];
}
```

```C++
// 입력 형태가 무조건 고정되어 있다면
// 예를들어 10 + 20 = 30 처럼 "숫자 연산기호 숫자 등호 결과" 순으로 고정이고
// 띄어쓰기마저 완전히 항상 고정이라면 아래와 같이 사용 가능

stringstream ss;
ss.str(s);
ss >> a >> op >> b >> eq >> c;
// 위처럼 아예 고정해버리고 시작하면 쉽다.
```

#### stringstream 사용시 주의점  
stringstream으로 문자열에서 특정 문자를 기준으로 자르게 처리할때  
```C++
while(getline(ss, part, 'x')) {
	answer.push_back(part);
}
```
위처럼 처리할 수가 있는데   
문제는 ss가 "axxbxx"같은 형태라면 "a", "", "b", "" 형태로 ""도 결과에 들어가게 됨  
그러므로 ""는 무시하게 만들어줘야 할 필요가 있음  
```C++
    while(getline(ss, part, 'x')) {
        if(part != "") answer.push_back(part);
    }
```