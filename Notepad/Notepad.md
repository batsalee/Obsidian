# Today_Study 정리하기
---

## 1. stoi 사용 시 core dump 오류

stoi(buffer) 사용 시 core dump가 뜨는 이유는 ==buffer가 비어있기 때문==  
그러므로 if로 buffer가 비어있는지 확인하고 사용해야 함
```C++
if(buffer != "") stoi(buffer);
```
★ 포인터나 문자열에 접근하기 전에는 항상 != null 사용하는 습관 들이기

## 2. pair STL

``` C++
pair<int, int> ip;
ip.first = 1;
ip.first = 2; // 이런식으로 대입도 가능
    
cout << "ip.first : " << ip.first << ", ip.second : " << ip.second << endl;
 
pair<int, double> idp(1, 2.5); // 이런식으로초기화도 가능
cout << "idp.first : " << idp.first << ", idp.second : " << idp.second << endl;
 
pair<string, int> sip;
sip = make_pair("스트링", 10); // make_pair를 사용해 대입도 가능
cout << "sip.first : " << sip.first << ", sip.second : " << sip.second << endl;
```
make_pair의 존재 기억하기