# LIS

## 1. 개념

최장 증가 부분 수열(LIS, Longest Increasing Subsequence)  
원소가 N개인 배열에서 일부 원소를 골라내서 만든 부분 수열들 중, 각 원소가 이전원소보다 크면서 부분수열의 길이가 최대인 수열을 최장 증가 부분 수열이라고 함

`arr[] = { 6, 2, 5, 1, 7, 4, 8, 3 }`이라면 LIS는 `{ 2, 5, 7, 8 }`이 되는 것  
{ 2, 5 }나 { 2, 7, 8 }이나 { 2, 5, 7 }등 증가하는 부분수열은 많겠지만 그 중 가장 긴 것이 { 2, 5, 7, 8 }이므로  

보통 LIS의 길이를 물어보는 방식으로 문제가 출제됨


## 2. LIS 풀이법

3가지 방식이 있는데  
1) O(N<sup>2</sup>) 시간복잡도를 가지는 DP방식
2) O(NlogN) 시간복잡도를 가지는 세그먼트 트리를 활용하는 방식
3) O(NlogN) 시간복잡도를 가지는 이분탐색 방식

#### 1) DP방식
sub mission은 `dp[x] = x`번째 수를 마지막 원소로 가지는 lis의 길이  
j < i 일때 `dp[i]`가 `dp[j] + 1` 보다 작다면 `dp[i]`는 `dp[j] + 1`이 될 수 있으니 갱신하는 방식  
```C++
for (int i = 0; i < n; i++) {
    if (dp[i] == 0) dp[i] = 1;
    
    for (int j = 0; j < i; j++) {
        if (arr[i] > arr[j]) {
            if (dp[i] < dp[j] + 1) {
                dp[i] = dp[j] + 1;
            }
        }
    }
}
```
하지만 위 방식은 O(N^2)이므로 비효율적일 수 있음

#### 2) 세그먼트 트리 방식
x번째 수인 `arr[x]`를 마지막 원소로 가지는 lis의 길이를 업데이트하면서  
매 순간 자기보다 앞구간에서 최댓값을 찾아서 최댓값 + 1로 업데이트하는 방식

#### 3) 이분탐색 방식
빠르면서 쉬운 방식  
수열을 처음부터 확인하면서 이분탐색을 이용해 LIS를 유지하기위한 최적의 위치에 수를 삽입하는 방식  
수열을 처음부터 확인하므로 O(N)의 시간복잡도가 들고, 이분탐색을 사용하므로 O(logN)이 들어서 시간복잡도 O(NlogN)  
이분탐색으로는 lower_bound를 사용한다.  

진행방법
1) 벡터를 생성한 뒤 `-INF`(나올 수 없는 가장 작은 값)을 삽입
2) 수열을 볼때마다 벡터의 맨 뒤 원소와 현재 수열의 원소를 비교해서  
	수열의 원소가 더 크면 push_back하고 LIS++  
	수열의 원소가 더 작으면 lower_bound를 이용해서 최적의 자리를 찾아서 그 자리의 값을 해당 수열의 원소로 교체  

진행과정을 자세히 풀어보면  
`vec[0]`에 `-INF`를 넣고 `int arr[9] = {10, 20, 40, 25, 20, 50, 30, 70, 85};` 라고 할때  
10을 볼때 vec.back()보다 10이 더 크니 vec = {-INF, 10}  
20을 볼때 vec.back()보다 20이 더 크니 vec = {-INF, 10, 20}  
40을 볼때 vec.back()보다 40이 더 크니 vec = {-INF, 10, 20, 40}  
25을 볼때 vec.back()이 더 크므로 lower_bound로 위치 찾고 대체해버리면 vec = {-INF, 10, 20, 25}  
20을 볼때 vec.back()이 더 크므로 lower_bound로 위치 찾고 대체해버리면 vec = {-INF, 10, 20, 25}  
50을 볼때 vec.back()보다 50이 더 크니 vec = {-INF, 10, 20, 25, 50}  
이런식으로 쭉쭉 진행하면 최종적으로 vec = {10, 20, 25, 30, 70, 85}이고 push_back()할때마다 result++했으므로 LIS는 6
```C++
#include <iostream>
#include <vector>

#define INF 2147483647

int main()
{
    int arr[9] = {10, 20, 40, 25, 20, 50, 30, 70, 85};
    std::vector<int> vec;
    vec.push_back(-INF);

    int length_of_lis = 0;
    for (int i = 0; i < 9; i++) {
        if (arr[i] > vec.back()) {
            vec.push_back(arr[i]);
            length_of_lis++;
        }
        else {
            auto itr = lower_bound(vec.begin(), vec.end(), arr[i]);
            *itr = arr[i];
        }
    }

    std::cout << "lis 길이 : " << length_of_lis; // 6 출력

    return 0;
}
```


## 3. LIS 수열 출력

그냥 lis의 길이가 아니라 LIS 배열 결과를 출력해야 하는 경우  
index 배열을 만들고 해당 배열에 i는 LIS의 몇번 index에 들어갔는가를 저장함  
마지막에 뒤에서부터 --하면서 같은 숫자면 넣고 reverse  
```C++
#include <bits/stdc++.h>
using namespace std;

#define INF 2147483647

int main()
{
    int N;
    cin >> N;

    vector<int> input(N), index(N);;
    for (int i = 0; i < N ; i++) { cin >> input[i]; }

    vector<int> lis_vec;
    lis_vec.push_back(-INF);
    for (int i = 0; i < N; i++) {
        if (lis_vec.back() < input[i]) {
            lis_vec.push_back(input[i]);
            index[i] = lis_vec.size() - 1;
        }
        else {
            auto itr = lower_bound(lis_vec.begin(), lis_vec.end(), input[i]);
            *itr = input[i];
            index[i] = itr - lis_vec.begin();
        }
    }
    
    int lis = lis_vec.size() - 1;
    cout << lis << '\n';

    vector<int> answer;
    for (int i = N - 1; i >= 0; i--) {
        if (index[i] == lis) {
            answer.push_back(input[i]);
            lis--;
        }
    }

    for (auto ritr = answer.rbegin(); ritr != answer.rend(); ritr++) {
        cout << *ritr << ' ';
    }
    
    return 0;
}
```






※ 참고 문헌
[https://jason9319.tistory.com/113](https://jason9319.tistory.com/113)