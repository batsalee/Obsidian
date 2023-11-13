# nCr

```C++
int numerator = 1, denominator = 1; // 분자, 분모
for(int i = r + 1; i <= n; i++) { numerator *= i; }
for(int i = 1; i <= n - r; i++) { denominator *= i; }

int nCr = numerator / denominator;
```
