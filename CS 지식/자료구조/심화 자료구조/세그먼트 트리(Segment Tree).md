# 세그먼트 트리(Segment Tree)


## 1. 세그먼트 트리란?

구간을 저장하기 위한 트리  
구간합, 구간 내 최소값, 구간 내 최대값 등등 구간내의 값을 빠르게 파악하기 좋은 자료구조  
![](https://blog.kakaocdn.net/dn/dtFQLa/btshE8NxaMs/ywXjRgh6bL0X8b8OGRXE3K/img.png)

구간합을 예시로 설명하자면 위 사진처럼 7칸짜리 배열이 있다고 할 때  
0 ~ 7번 자리에 들어있는 값들을 트리의 말단 노드인 리프에 놓고  
0 ~ 1번 인덱스 값의 합을 부모노드에, 2 ~ 3번 인덱스 값의 합을 부모노드에, ...  
최종적으로 root에는 모든 배열값들의 합이 위치하는 형태  


## 2. 세그먼트 트리가 없다면?

예를들어 `arr[] = { 1, 2, 3, 4, 5 };`의 배열이 있을 때  
index 0 ~ 2의 구간의 합을 구해라, 1 ~ 3의 구간의 합을 구해라, 2 ~ 4의 구간의 합을 구해라 같은 구간합을 여러번 구하는 문제가 있다면 누적합 알고리즘을 사용하면 된다.  
  
그런데 만약 구간의 합을 구하는 사이사이에 배열의 값을 바꾸는 요청이 있다면?  
예를들어  
1) index 0~2 구간의 합을 구해라  
2) index 2의 값을 7로 변경해라  
3) index 1~3 구간의 합을 구해라  
4) index 1의 값을 6으로 변경해라  
   ...  
이런 식으로 있다면  
  
만약 누적합 알고리즘을 사용한다면 1)처럼 구간합을 구하는 부분은 O(1)로 아주 빠르지만    
2)처럼 값을 변경하는 경우 해당 index의 이후 값을 전부 다 변경해줘야 하므로 O(N)의 시간이 걸린다.    
이런 사이클을 M회 반복한다면 결국 O(M * N)이 된다.    
  
그냥 매번 반복문으로 구간합을 구한다고 해도    
1)처럼 구간합을 구하는 부분은 O(N)의 시간복잡도이고    
2)처럼 값을 변경하는 것은 O(1)이 되고     
이를 M번 반복한다고 하면 결과적으로 O(M * N)이 된다.  
  
반면 세그먼트트리를 사용하면 O(M * logN)으로 더 효율적으로 해결할 수 있다.  


## 3. 세그먼트 트리 공식

![](https://blog.kakaocdn.net/dn/p7ztz/btshChc3IqR/8uHMeWEk9JrRQWV7UoRkIK/img.png)

`int arr[5] = {1, 2, 3, 4, 5};`가 있을때 세그먼트 트리의 초기화 모습은 위의 트리와 같다.  
리프노드에 배열의 값들이 있고 그 노드들의 부모는 자식들의 합을 값으로 가진다.  

위 트리를 통해 알 수 있는것은 5개에 대한 세그먼트 트리를 구현하는데 필요한 노드의 수는 9개고 트리의 높이는 3이다.  

만약 배열의 원소가 짝수개라면?  
![](https://blog.kakaocdn.net/dn/bjTQO3/btshzz0cSel/IS5ZijpcqOxmB4NGdDIuFK/img.png)

노드의 개수는 7개이고 트리의 높이는 2이다.

#### 트리의 인덱스
먼저 인덱스 0은 비워두고 사용하지 않는다.  
```
부모노드의번호 * 2 = 왼쪽자식노드  
부모노드의번호 * 2 + 1 = 오른쪽자식노드
```
위의 두 공식을 사용하기 위해 0번은 사용하지 않는다.

#### 트리의 높이 구하는 법
트리의 높이는 `CEIL(log2(배열의 크기))`로 구할 수 있다.  
N = 4일땐 `CEIL(log2(4))`이므로 2.0000의 올림인 2가 트리의 높이이고  
N = 5일땐 `CEIL(log2(5))`이므로 2.xxxx의 올림인 3이 트리의 높이이다.  

#### 노드의 갯수 구하는 법
`2^(높이+1)`로 구할 수 있다. 비트연산으로는 `( 1<<(높이+1) )`가 깔끔하다.  
정확하게 딱 몇개다라고 구하는게 아니라 최대 x개 필요하다라고 계산하는 방식(리프노드가 full이 아닐 수 있으므로)  
높이가 2인 트리는 노드의 갯수가 8이므로 사용하지 않는 0번인덱스를 제외하면 7개의 노드가 사용되고  
높이가 3인 트리는 노드의 갯수가 16이고 위 그림에서 16칸 모두 사용하지는 않았지만 9칸을 사용하게 되는 방식이다.  

#### 정리하면
크기가 N인 배열은  
1) 트리의 높이 = CEIL(log(N))  
2) 트리의 크기 = (1 << (트리의 높이 + 1) )  
위 두 공식을 이용해서 트리 구현에 필요한 공간을 설정할 수 있다.  
배열의 크기 * 4로 대충 설정하고 쓰는 경우도 많다고 함  


## 4. 세그먼트 트리 만드는 법

C++기반으로는 벡터에 재귀를 사용해서 만들어준다.  

재귀의 매개변수로 (현재노드번호, 시작범위, 마지막범위)를 사용하며  
**현재노드번호**는 0번인덱스는 사용하지 않으므로 첫호출로  1로 사용해서 호출한다.  
**시작범위**는 배열의 시작범위이므로 0으로 설정하고  
**마지막범위**는 배열의 마지막범위이므로 N-1로 설정한다.  
즉 재귀의 첫 호출은 (1, 0, N-1)로 시작한다.  

트리를 만드는 알고리즘 구성은  
1) 주어진 배열의 범위를 반으로 나눈다.
2) 나눠진 2개의 범위에서 왼쪽범위에 대한 재귀를 호출한다.
3) 나눠진 2개의 범위에서 오른쪽범위에 대한 재귀를 호출한다.
4) 위의 과정을 반복한다.

상세히 설명해보자면
`arr[5] = { 1, 2, 3, 4, 5 };`일때 첫 재귀호출은 `(1, 0, 4)`이고
\1) 5/2로 2가된다. 즉 이제 왼쪽범위는 0~2이고 오른쪽범위는 3~4이다.

\2) 왼쪽노드는 현재노드번호 * 2이므로 왼쪽범위에 대한 재귀호출은 (2, 0, 2)가 된다.
왜냐하면 시작호출인 (1, 0, 4)에서 노드번호는 왼쪽노드니까 1 * 2로 2가 된것이고 왼쪽범위는 0~2이 되므로 (2, 0, 2)가 된다.
여기서 또 왼쪽재귀이므로 다음 진행은 (4, 0, 1)이 될것이고 그 다음은 (8, 0, 0)이 된다.  

이때 `시작하는범위==끝나는범위`일때 재귀를 종료한다.  
재귀를 종료하기 전에 `세그먼트트리[노드번호] = 배열[시작범위]`로 값을 설정한다.  
여기선 `SegmentTree[8] = arr[0];`이 된다.

\3) 오른쪽노드는 현재노드번호 * 2 + 1이고 (8, 0, 0)에서 끝난 후 오른쪽범위를 검사하므로  
(9, 1, 1)이 된다. 이 또한 `시작하는범위==끝나는범위`이므로 `SegmentTree[9] = arr[1];`이 된다.

\4) 트리의 8번과 9번의 값을 구했으니 4번의 값도 구할 수 있다. 8번과 9번 값의 합이된다.

위처럼 재귀가 반복된다.
```C++
#include <vector>

std::vector<int> arr{ 1, 2, 3, 4, 5 };
std::vector<int> SegmentTree;

int Make_SegmentTree(int Node, int Start, int End)
{
    if (Start == End) return SegmentTree[Node] = arr[Start];

    int Mid = (Start + End) / 2;
    int Left_Result = Make_SegmentTree(Node * 2, Start, Mid);
    int Right_Result = Make_SegmentTree(Node * 2 + 1, Mid + 1, End);
    SegmentTree[Node] = Left_Result + Right_Result;

    return SegmentTree[Node];
}

int main(void)
{
    int N = arr.size();

    int Tree_Height = (int)ceil(log2(N));
    int Tree_Size = (1 << (Tree_Height + 1));
    SegmentTree.resize(Tree_Size);
    Make_SegmentTree(1, 0, N - 1);
}
```

다른 방법) 굳이 왼쪽자식값 오른쪽자식값을 구해서 리턴할 필요 없이 그냥 SegmentTree값 참조해서 더하면 된다. return되기 직전엔 mode가 가장 아래쪽 값이므로  
```C++
void Make_SegmentTree(int Node, int Start, int End) {
    if (Start == End) SegmentTree[Node] = arr[Start];
    else {
        int Mid = (Start + End) / 2;
        Make_SegmentTree(Node * 2, Start, Mid);
        Make_SegmentTree(Node * 2 + 1, Mid + 1, End);
        SegmentTree[Node] = SegmentTree[Node * 2] + SegmentTree[Node * 2 + 1];
    }
}
```


## 5. 세그먼트 트리 값 바꾸는 법

배열이라면 그냥 index의 값을 변경하면 끝이지만  
세그먼트 트리는 배열의 구간합들을 저장한 것이므로 리프노드인 배열의 값이 변경하면 연관된 모든 노드의 값을 변경해야 하므로 쉽지않다.  

트리의 값을 변경할 때 2가지 경우의 수가 있다.  
1) 바꾸고자 하는 index값이 현재 탐색하는 범위에 속해있지 않은 경우  
2) 바꾸고자 하는 index값이 현재 탐색하는 범위에 속해있는 경우  

1번의 경우는 탐색을 더 하지 않아도 된다. 변경할 값도 없을것이므로  
2번의 경우가 문제인데

`arr[] = { 1, 2, 3, 4, 5 };`일때  
index 1의 값을 5로 바꾸라고 한다면   

![](https://blog.kakaocdn.net/dn/kCqij/btshy6YiFWM/gkEF3eqdqERqdYo406vC7K/img.png)

1번노드에서 시작해서 왼쪽서브트리와 오른쪽서브트리로 진행해나가게 될것이다.  

근데 3번노드의 경우 범위에 속해있지 않으므로 굳이 6, 7번노드로 내려갈 필요 없이 바로 return하면 된다.  

반면 2번노드의 경우는 우리가 변경할 index가 속해있으므로 값을 바꿔줘야 한다. 배열의 인덱스 1에는 2가 있었는데 5로 변경하므로 +3이 되므로 2번노드도 +3해주면 된다.  
그 후 자식노드에도 변경되는 값이 있을테니 또 진행해준다.  

위의 과정을 거치면서 4번노드와 5번노드로도 갈텐데 5번에서는 또 상관없는 범위이므로 return  
4번 노드의 경우는 +3해주고 8번과 9번노드로 진행한다.  
8번노드는 우리가 찾고자 하는 범위와 상관없으니 return, 9번노드는 +3해준다.  
```C++
void Update_SegmentTree(int Node, int Start, int End, int Index, int Diff)
{
    if (Index < Start || Index > End) return;
    SegmentTree[Node] = SegmentTree[Node] + Diff;
 
    if (Start != End)
    {
        int Mid = (Start + End) / 2;
        Update_SegmentTree(Node * 2, Start, Mid, Index, Diff);
        Update_SegmentTree(Node * 2 + 1, Mid + 1, End, Index, Diff);
    }
}
 
int main(void)
{
    int Index = 1;
    int Value = 5;
    int Diff = Value - Arr[Index];
    Arr[Index] = Value;
    Update_SegmentTree(1, 0, N - 1, Index, Diff);
}
```

다른 방법) 차이만큼 더하면서 내려가는 방식이 아니라 해당 노드를 찾으면 그 값을 변경한 후 올라오면서 자식노드 2개의 합을 계속 새로 계산  
```C++
void Update_SegmentTree(int Node, int Start, int End, int Index, int Value) {
    if (Index < Start || Index > End) return;

    if (Start == End) {
        arr[Index] = Value;
        SegmentTree[Node] = Value;
        return;
    }
    
    int Mid = (Start + End) / 2;
    Update_SegmentTree(Node * 2, Start, Mid, Index, Value);
    Update_SegmentTree(Node * 2 + 1, Mid + 1, End, Index, Value);
    SegmentTree[Node] = SegmentTree[Node * 2] + SegmentTree[Node * 2 + 1];
}
```

개인적으로 아래의 방법이 더 직관적이라 좋은것같음  


## 6. 세그먼트 트리 탐색

트리를 탐색할 때 3가지 경우의 수가 있다.  
1) 현재 탐색하는 범위와 찾고자 하는 범위가 전혀 다를 경우  
2) 현재 탐색하는 범위에 찾고자 하는 범위가 완전히 속하는 경우  
3) 현재 탐색하는 범위에 찾고자 하는 범위가 일부만 속하는 경우(걸쳐있는 경우)  

`arr[] = { 1, 2, 3, 4, 5 };` 일때 
![](https://blog.kakaocdn.net/dn/yGvtz/btshG7nrGJk/JcjwbEeWxhY8j9nz1TJe10/img.png)

루트에서 시작해서 왼쪽먼저 확인한다고 할 때  

배열 index 3~4의 합을 구한다면 왼쪽 노드로 갔을때 왼쪽노드는 index 0 ~ 2의 합이므로 범위가 전혀 다르다. 즉 위의 경우의 수 중 첫번째에 해당한다.  
이 경우 그냥 리턴하고 다음 탐색을 한다.  

배열 index 0~2의 합을 구한다면 2번노드의 값인 6을 그대로 리턴하면 된다.  
즉 이 경우는 경우의 수 중 두번째의 경우에 해당한다.  

배열 index 2~3의 합을 구한다면 왼쪽과 오른쪽 서브트리에 걸쳐있으므로 계속 아래로 탐색해서 원하는 구간이 나오면 그 값을 리턴해서 더한다.
```C++
int Sum(int Node, int Start, int End, int Left, int Right)
{
    if (Left > End || Right < Start) return 0;
    if (Left <= Start && End <= Right) return SegmentTree[Node];
 
    int Mid = (Start + End) / 2;
    int Left_Result = Sum(Node * 2, Start, Mid, Left, Right);
    int Right_Result = Sum(Node * 2 + 1, Mid + 1, End, Left, Right);
    return Left_Result + Right_Result;
}

/*
    Node : 노드번호
    Start, End = 해당 노드가 포함하고 있는 범위
    Left, Right = 구하고자 하는 범위
    
    line 3) 현재 탐색하고 있는 범위가 구하고자 하는 범위와 전혀 상관 없을때 return
    line 4) 현재 탐색하고 있는 범위가 구하고자 하는 범위에 완전 속해있을 때 값을 그대로 가져옴
    line 6~9) 일부만 걸쳐있을 때는 더 아래로 탐색해서 필요한 구간합까지 찾아가야 함
            그 후 필요한 부분의 값들을 양쪽에서 구해서 더한 후 return
*/
```


## 7. 최종적으로 코드 정리

#### 구간합 코드
```C++
#include <iostream>
#include <vector>

std::vector<int> arr{ 1, 2, 3, 4, 5 };
std::vector<int> SegmentTree;

void Make_SegmentTree(int Node, int Start, int End) {
    if (Start == End) SegmentTree[Node] = arr[Start];
    else {
        int Mid = (Start + End) / 2;
        Make_SegmentTree(Node * 2, Start, Mid);
        Make_SegmentTree(Node * 2 + 1, Mid + 1, End);
        SegmentTree[Node] = SegmentTree[Node * 2] + SegmentTree[Node * 2 + 1];
    }
}

void Update_SegmentTree(int Node, int Start, int End, int Index, int Value) {
    if (Index < Start || Index > End) return;

    if (Start == End) {
        arr[Index] = Value;
        SegmentTree[Node] = Value;
        return;
    }
    
    int Mid = (Start + End) / 2;
    Update_SegmentTree(Node * 2, Start, Mid, Index, Value);
    Update_SegmentTree(Node * 2 + 1, Mid + 1, End, Index, Value);
    SegmentTree[Node] = SegmentTree[Node * 2] + SegmentTree[Node * 2 + 1];
}

int Sum(int Node, int Start, int End, int Left, int Right)
{
    if (Left > End || Right < Start) return 0;
    if (Left <= Start && End <= Right) return SegmentTree[Node];

    int Mid = (Start + End) / 2;
    int Left_Result = Sum(Node * 2, Start, Mid, Left, Right);
    int Right_Result = Sum(Node * 2 + 1, Mid + 1, End, Left, Right);
    return Left_Result + Right_Result;
}

int main()
{
    int N = arr.size();

    // Make
    int Tree_Height = (int)ceil(log2(N));
    int Tree_Size = (1 << (Tree_Height + 1));
    SegmentTree.resize(Tree_Size);
    Make_SegmentTree(1, 0, N - 1);

    // Update
    int Index = 1;
    int Value = 5;
    int Diff = Value - arr[Index];
    arr[Index] = Value;
    Update_SegmentTree(1, 0, N - 1, Index, Diff);
    
    // Sum 구하기
    int sum = Sum(1, 0, N - 1, 1, 2); // 배열 index 1 ~ 2의 합인 5 구하기
    std::cout << sum << '\n';
    
    return 0;
}
```

#### 구간합 외의 세그먼트 트리
최솟값 뿐 아니라 최댓값, 곱, XOR연산 등도 모두 가능하다.  
최솟값을 예시로 설명한다면 구간합에서 +로 사용했던 부분들을 모두 min으로 바꿔주면 된다.  
```C++
#include <iostream>
#include <cmath>
#include <vector>
using namespace std;

vector<int> arr{ 1, 2, 3, 4, 5 };
vector<int> SegmentTree;

void Make_SegmentTree(int Node, int Start, int End) {
    if (Start == End) SegmentTree[Node] = arr[Start];
    else {
        int Mid = (Start + End) / 2;
        Make_SegmentTree(Node * 2, Start, Mid);
        Make_SegmentTree(Node * 2 + 1, Mid + 1, End);
        SegmentTree[Node] = min(SegmentTree[Node * 2], SegmentTree[Node * 2 + 1]);
    }
}

void Update_SegmentTree(int Node, int Start, int End, int Index, int Value) {
    if (Index < Start || Index > End) return;

    if (Start == End) {
        arr[Index] = Value;
        SegmentTree[Node] = Value;
        return;
    }

    int Mid = (Start + End) / 2;
    Update_SegmentTree(Node * 2, Start, Mid, Index, Value);
    Update_SegmentTree(Node * 2 + 1, Mid + 1, End, Index, Value);
    SegmentTree[Node] = min(SegmentTree[Node * 2], SegmentTree[Node * 2 + 1]);
}

int SearchMin(int Node, int Start, int End, int Left, int Right) {
    // sum에선 0을 return했는데 0을 더해도 결과가 안바뀌니 그랬던것이고
    // 이번엔 절대 나오지 않는 값을 return해서 해당 값이면 거르게 만드는 것이 차이점
    if (Left > End || Right < Start) return -1;
    if (Left <= Start && End <= Right) return SegmentTree[Node];

    int Mid = (Start + End) / 2;
    int Left_Result = SearchMin(Node * 2, Start, Mid, Left, Right);
    int Right_Result = SearchMin(Node * 2 + 1, Mid + 1, End, Left, Right);

    if (Left_Result == -1) return Right_Result;
    else if (Right_Result == -1) return Left_Result;
    else return min(Left_Result, Right_Result);
}

int main(void)
{
    int N = arr.size();

    // Make
    int Tree_Height = (int)ceil(log2(N));
    int Tree_Size = (1 << (Tree_Height + 1));
    SegmentTree.resize(Tree_Size);
    Make_SegmentTree(1, 0, N - 1);

    // Update
    int Index = 1;
    int Value = 5;
    Update_SegmentTree(1, 0, N - 1, Index, Diff);

    // Min 구하기
    int minimum = SearchMin(1, 0, N - 1, 1, 2);
    std::cout << minimum << '\n';

    return 0;
}
```


## 8. 예시 문제

#### 구간 합 예시문제 
[https://www.acmicpc.net/problem/2042](https://www.acmicpc.net/problem/2042)
풀이 : [https://smallpants.tistory.com/278](https://smallpants.tistory.com/278)

[https://www.acmicpc.net/problem/1275](https://www.acmicpc.net/problem/1275)
풀이 : 그냥 위에 있는 코드 그대로 가져다 써도 됨

#### 구간 최소값 예시문제
[https://www.acmicpc.net/problem/10868](https://www.acmicpc.net/problem/10868)
풀이 : [https://smallpants.tistory.com/277](https://smallpants.tistory.com/277)

[https://www.acmicpc.net/problem/14427](https://www.acmicpc.net/problem/14427)
[https://www.acmicpc.net/problem/14428](https://www.acmicpc.net/problem/14428)
[https://www.acmicpc.net/problem/14438](https://www.acmicpc.net/problem/14438)

#### 구간 최소값과 최대값 동시에 구하는 예시문제
[https://www.acmicpc.net/problem/2357](https://www.acmicpc.net/problem/2357)
풀이 : [https://smallpants.tistory.com/276](https://smallpants.tistory.com/276)

#### 구간 곱 예시문제
[https://www.acmicpc.net/problem/11505](https://www.acmicpc.net/problem/11505)
풀이 : [https://smallpants.tistory.com/275](https://smallpants.tistory.com/275)






※ 참고문헌
[https://yabmoons.tistory.com/431](https://yabmoons.tistory.com/431)
[https://book.acmicpc.net/ds/segment-tree](https://book.acmicpc.net/ds/segment-tree)