#### 1) 벡터 내의 모든 원소의 합
```C++
int sum = accumulate(vec.begin(), vec.end(), 0);
```

#### 2) 벡터 내의 모든 원소의 곱
```C++
int mul = accumulate(vec.begin(), vec.end(), 1, multiplies<int>());
```

#### 3) string 벡터 이어붙이기
```C++
vector<string> v{"abc", "de", "f", "ghijk"};
string answer = accumulate(v.begin(), v.end(), string{});
```