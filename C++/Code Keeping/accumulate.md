벡터 내의 모든 원소의 합은 
`int sum = accumulate(vec.begin(), vec.end(), 0);`처럼 쓰는데 

벡터 내의 모든 원소의 곱은?
`int mul = accumulate(vec.begin(), vec.end(), 1, multiplies<int>());`