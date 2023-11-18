# 최소공배수, 최대공약수

## 1. GCD, LCM 개념

[[수학적 지식/GCD, LCM|GCD, LCM]]

## 2. C++ style Code

1) STL 사용  
```C++
#include <numeric>
gcd(a, b);
lcm(a, b);
```

혹은 유클리드 호제법으로 아래처럼 직접 구현  

2) 재귀적으로 구현  
``` C++
int GCD(int a, int b)
{
	if(b==0) return a;
	else return GCD(b, a%b);
}
```

3) while문으로  
``` C++
int GCD(int a, int b)
{
	while(1) {
		int c = a%b;
		if(c==0) return b;
		a = b;
		b = c;
	}
}
```

혹은  

``` C++
int GCD(int a, int b)
{
	int c;
	while (b != 0) {
		c = a % b;
		a = b;
		b = c;
	}
	return a;
}
```