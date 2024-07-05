# MST 알고리즘(크루스칼, 프림)

## 1. 최소 신장 트리

최소 신장 트리(Minimum Spanning Tree)를 만들땐 크루스칼 알고리즘이나 프림 알고리즘을 사용하면 된다.  

최소 신장트리에 대해 먼저 알아야 한다.  
[[신장 트리, 최소 신장 트리]]  

또한 크루스칼 알고리즘은 유니온 파인드를 사용하므로 유니온 파인드 개념에 대해 알아야 한다.  
[[유니온 파인드]]  

#### 1) 크루스칼 알고리즘
- 간선을 선택하며 진행한다.
  그래서 ==간선이 적은== 그래프를 MST로 만들때 적합하다.
  간선으로 이어지는 두 노드를 유니온 파인드로 합쳐나가면서 진행한다.
- 사이클 검사가 필요하다.
  간선을 선택하므로 트리가 여러개 생길 수 있고 사이클이 생기지 않도록 그 중에서 선택해야 한다.

#### 2) 프림 알고리즘
- 정점을 선택하며 진행한다.
  그래서 ==간선이 많은== 그래프를 MST로 만들때 적합하다.
- 시작 정점을 정해두고 시작하며 사이클 검사가 필요하지 않다.
  인접한 정점들 중에 선택해 나가는 방식이므로 진행 과정 중에도 그래프의 형태를 띄고 최종적으로 하나의 트리가 생성된다.

#### 3) 간선이 많고 적은 기준
간선이 몇개일때부터 많다고 해야할지 궁금해서 Chat GPT에게 물어봤다.  

E < VlogV 인 경우 간선이 적다고 판단하고 크루스칼 알고리즘 사용  
E >= VlogV인 경우 간선이 많다고 판단하고 프림 알고리즘 사용  

예를들어 정점이 100개인 그래프가 있다면 log100은 6.64정도이므로 VlogV는 664가 된다.  
간선이 664개보다 많다면 프림, 664개보다 적다면 크루스칼이 되는 것이다.  

#### 4) 근데 그냥 크루스칼이 일반적으로 더 빠르다
크루스칼은 시간복잡도가 O(ElogE), 프림은 시간복잡도가 O(ElogV)라고 한다.  
근데 프림의 logV가 자료구조의 성능을 크게 받는다.  
최소힙을 사용하는 구현의 경우 중복되는 정점들이 원소로 많이 삽입되어서 아주 느려지게 되고  
그 결과 문제들 풀어본 결과 간선이 많아도 크루스칼이 훨씬 빠른 경우가 자주 보이는듯하다.  

___
## 크루스칼 알고리즘(Kruskal Algorithm)

#### 1. 동작 원리
1) 모든 간선의 가중치를 오름차순으로 정렬한다.  
2) 가중치가 가장 작은 간선을 선택한다.  
3) 2에서 선택한 간선이 연결하는 2개의 노드가 아직 서로 연결되지 않은 상태라면 해당 간선을 MST 집합에 Union 시킨다.  
4) 2 ~ 3의 과정을 반복한다.  

무조건 가장 적은 비용의 간선부터 선택해 나가는 Greedy 방식이다.  
단 두 정점이 연결되어 있지 않은 경우에만 해당 간선을 선택한다.  

#### 2. 자세한 설명
![300x300](https://blog.kakaocdn.net/dn/c915JB/btsImhL1r9c/oSv1lkR5qUAs6mLypI9RSK/img.png)

위와 같은 그래프에서 크루스칼 알고리즘을 사용한다.  
모든 간선이 끊어진 상태에서 정점들만 놓고 시작한다.  

###### 1) 모든 간선의 가중치를 오름차순으로 정렬한다.

| B - C | A - B | D - E | B - D | C - E | A - D | A - E | C - D |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| 1     | 3     | 3     | 4     | 4     | 5     | 7     | 8     |

###### 2) 가중치가 가장 작은 간선을 선택한다.
B와 C를 잇는 간선의 가중치가 1이다.  

###### 3) 2에서 선택한 간선이 연결하는 두 노드가 연결되어있는지 확인한다.
지금은 다 끊어진 상태로 시작하므로 연결되어 있지 않다.  
그러므로 B - C 구간을 가중치 1인 간선으로 연결한다.  
![300x300](https://blog.kakaocdn.net/dn/zuBBu/btsIlBYn92g/xWKsHCo7T2cm7ePDpb8j21/img.png)

###### 4) 반복
그 다음으로 A - B 간선(가중치 3)을 선택한다.  
A와 B가 현재 연결되어 있지 않으므로 해당 간선으로 연결한다.  
그 다음으로 D - E도 연결하고, B - D도 연결한다.  
그럼 아래와 같은 상황이 된다.  
![300x300](https://blog.kakaocdn.net/dn/ReAD8/btsImbx83X1/q9iFBCHshm43klDMtxajUK/img.png)

이 이후에 C - E를 연결하려고 할때 이미 C와 E는 연결되어 있는 상태이므로 넘어간다.  
그 이후로 A - D, A - E, C - D도 모두 이미 연결되어 있으니 넘어간다.  
모든 간선을 확인했으니 알고리즘을 끝낸다.  
또는 간선이 N - 1개인지 확인하고 조기종료하는 방법도 있다.

#### 3. 코드 구현
1번의 가중치 정렬은 pair에 <가중치, pair<노드1, 노드2>> 순으로 넣고 정렬하면 된다.  
혹은 tuple 써도 되지 않나 싶다. 물론 의미상으로는 이중페어가 더 명확하긴 하다.  

2번부터가 문젠데 두 노드가 연결되어 있는지 아닌지 확인하는 기능이 필요하다.  
이때 union - find 방식을 사용한다. [[유니온 파인드]]  

Parent[] 배열을 사용해서 부모가 같은지 판단한다.  
서로 다른 두 노드가 같은 조상을 가지고 있다면 서로 연결되어 있는 것이고, 서로 다른 조상을 가지고 있다면 서로 연결되어 있지 않은 것이다.  
결론적으로 유니온 파인드로 서로 다른 두 노드의 부모를 확인했을때 두 노드의 부모가 같다면 간선을 연결하지 않는다.  
다르다면 연결한다.  

유니온 파인드로 모든 간선을 확인할때까지 loop를 돌려도 되고 조건을 걸어서 조기종료해도 될듯하다.  
예를들어 모든 정점의 부모 정점이 같아진다면 완성된것이다.  
또는 선택된 간선의 갯수가 N - 1개가 되어도 마찬가지이다.  

#### 예시 코드
주로 누적 거리를 묻는듯하다.  
```C++
#include <bits/stdc++.h>
using namespace std;

vector<int> parent;

int Find(int x) // 노드 x의 부모를 찾는 함수
{
	if (parent[x] == x) return x;
	return parent[x] = Find(parent[x]);
}

void Union(int x, int y) // 노드 x와 y를 합쳐주는 함수
{
	x = Find(x); // 먼저 x의 부모를 찾고
	y = Find(y); // y의 부모를 찾아준다.
	if (x < y) parent[y] = x; // 더 작은 숫자를 부모로 병합
	else if (x > y) parent[x] = y;
	// 만약 두 노드의 부모가 서로 같다면 아무 동작도 하지 않고
	// 다르다면 한쪽 노드의 부모를 연결되는 다른 한쪽 노드로 설정해버림.
	// 이 과정을 통해 노드 x의 부모도 x, 노드 y의 부모도 x로 부모가 같아진다.
}

int main()
{
	ios::sync_with_stdio(false); cin.tie(NULL);

	int V, E;
	cin >> V >> E;

	// 입력받고 비용 기준으로 오름차순 정렬
	vector<tuple<int, int, int>> costs(E); // cost, from, to
	int A, B, C;
	for (int i = 0; i < E; i++) {
		cin >> A >> B >> C;
		costs[i] = { C, A, B }; // sort 쉽게하려고 위치 바꿔주고
	}
	sort(costs.begin(), costs.end());

	// 처음엔 자기 자신을 부모로
	parent.resize(V + 1);
	for (int i = 1; i < V + 1; i++) {
		parent[i] = i;
	}

	// 누적 거리 계산
	int answer = 0, count = 0;
	for (auto t : costs) {
		int cost = get<0>(t);
		int from = get<1>(t);
		int to = get<2>(t);

		if (Find(from) != Find(to)) { // 부모가 다르다면
			answer += cost; // 비용에 더해주고
			Union(from, to); // 두 노드를 연결해주고
			count++; // 간선의 갯수를 count(V - 1개면 끝내려고)
		}

		if (count == V - 1) break;
	}

	cout << answer;

	return 0;
}
```

#### 예시 문제
전형적인 MST 크루스칼 문제 : [https://smallpants.tistory.com/288](https://smallpants.tistory.com/288)  
마을을 둘로 나눈다면? : [https://smallpants.tistory.com/289](https://smallpants.tistory.com/289)  

___
## 프림 알고리즘(Prim Algorithm)

#### 1. 동작 원리
구현 방법은 2가지가 있다.  
- `Dist[]` 배열을 만들어서 정점을 선택할때마다 최단길이인 간선을 찾아서 거리를 업데이트하는 방법  
- 최소힙을 이용한 (사실상 Priority_Queue) 방법  
우선 `Dist[]`를 사용한 방법으로 설명한다.  

사전준비) 
거리를 나타내는 `Dist[]` 배열을 만들고 INF로 채운다.  
시작점을 정한다. 딱히 조건이 없다면 무난하게 1번정점을 선택한다. `Dist[1] = 0`이 된다.
1번 정점에 이어진 정점들까지의 거리를 업데이트한다. 예를들어 `Dist[2] = 5`, `Dist[3] = 3`이 된다.
그 후 아래의 방법을 따른다.
1) 선택된 정점들 그룹과 연결된 간선들 중 가장 짧은 길이의 간선을 연결하고 연결된 정점을 선택한다.
2) 방금 선택된 정점에서 연결된 정점들의 거리를 업데이트한다.
3) 1 ~ 2번 과정을 N - 1회 반복한다.
정점이 총 N개인데 1번정점은 이미 뽑고 시작했으니 N - 1회가 된다.

#### 크루스칼 알고리즘과의 차이점
- 그래프의 틀을 유지한채로 진행하는점
- 간선이 아닌 정점을 선택하며 진행하는 점
- 크루스칼 알고리즘은 진행 과정중에 보면 그래프의 형태가 아니지만 프림은 진행 과정중에도 그래프의 형태이다.

#### 2. 자세한 설명
![300x300](https://blog.kakaocdn.net/dn/dJcG7T/btsInNbS1xz/iCKvMnLscwsw7M5je62xO0/img.png)

먼저 사전준비를 한다.  
거리를 보관하는 `Dist[]` 배열을 만들고 INF로 채운다.  
임의의 시작점을 선택해야 한다. 무난하게 1번을 선택하고 시작한다. `Dist[1] =  0`이 된다.  
시작점(1번)과 연결된 정점들의 거리를 업데이트한다. 위 사진을 예시로 `Dist[2] = 5`, `Dist[3] = 3`이 된다.
![300x300](https://blog.kakaocdn.net/dn/cJHbQN/btsIlDPB2bg/rYtdYtGtOmGLvxodQpMmqK/img.png)

###### 1) 선택된 정점들 그룹과 연결된 간선들 중 가장 짧은 길이의 간선을 연결하고 연결된 정점을 선택한다.
현재 선택된 정점은 1번정점뿐이다.  
1번정점에 연결된 간선 중 가장 짧은 길이는 1 - 3 간선이므로 해당 간선을 연결하고 3번정점을 선택한다.  
이제 선택된 정점 그룹은 { 1, 3 }이다.  

###### 2) 방금 선택된 정점에서 연결된 정점들의 거리를 업데이트한다.
3번정점의 시점에서 연결된 정점인 4번과 5번정점까지의 거리를 업데이트한다.  
![400x300](https://blog.kakaocdn.net/dn/cQjWgj/btsInmyXAY0/FZoTNS2kwCtxpHxO6Nx7Gk/img.png)

###### 3) 반복한다.
현재 선택된 정점 그룹은 {1, 3}이다.  
1번과 3번에 연결된 간선들 중 가장 짧은 길이의 간선은 3 - 4간선이므로 연결해준다.  
이제 4번도 선택된 정점이다.  

방금 선택된 4번에서 연결된 정점들의 거리를 업데이트한다.  
 `Dist[2] = 1`, `Dist[6] = 5`가 된다.
![400x300](https://blog.kakaocdn.net/dn/bTPAou/btsImHDDUaG/0OqjxClmupdJQlqjvxmEUk/img.png)

현재 선택된 정점 그룹은 {1, 3, 4}이다.
1, 3, 4번에 연결된 간선들 중 가장 짧은 길이의 간선은 2 - 4 간선이고 연결해준다.
이제 2번도 선택된 정점이다.

2번정점에서 거리를 업데이트한다.
다만 이미 기존의 값들과 같아서 변경은 발생하지 않는다.
![400x300](https://blog.kakaocdn.net/dn/cNalwz/btsImjXp62t/4XWGxEK5GDwJX9gVufQ5U1/img.png)

이제 흐름은 이해 됐을테니 간략히 설명한다.
또 선택된 그룹들에서 가장 짧은 간선을 선택하면 3 - 5간선이고 5번이 선택된다. 업데이트한다.
그 다음은 4 - 6 간선이 선택되고 6번이 선택된다. 업데이트한다.
최종적으로 아래의 모습과 같다.
![400x300](https://blog.kakaocdn.net/dn/UBYWX/btsInPOjoZl/wiupnuNa8q29z2lczkksPk/img.png)

#### 3. 코드 구현

#### `Dist[]`를 사용하는 방법
첫번째 방법은 위에서 작성한 동작 원리의 순서대로 작성한다.  

사전준비) 
거리를 나타내는 `Dist[]` 배열을 만들고 INF로 채운다.  
시작점을 정한다. 딱히 조건이 없다면 무난하게 1번정점을 선택한다. `Dist[1] = 0`이 된다.
1번 정점에 이어진 정점들까지의 거리를 업데이트한다. 예를들어 `Dist[2] = 5`, `Dist[3] = 3`이 된다.
그 후 아래의 방법을 따른다.
1) 선택된 정점들 그룹과 연결된 간선들 중 가장 짧은 길이의 간선을 연결하고 연결된 정점을 선택한다.
2) 방금 선택된 정점에서 연결된 정점들의 거리를 업데이트한다.
3) 1 ~ 2번 과정을 N - 1회 반복한다.

#### 사전준비 과정 코드
```C++
#include <bits/stdc++.h>
using namespace std;

#define INF 2147483647

vector<int> dist;
vector<bool> selected;

int main()
{
    int Answer = 0;
    int N;
    cin >> N;
    
    // 거리정보 입력받기
    // Cost배열
    
    selected.resize(N + 1, false); // 정점을 1번부터 써서 N + 1로
    dist.resize(N + 1, INF); // INF로 초기화된 dist배열 준비
    
    dist[1] = 0; // 시작점은 1번으로 선택
    selected[1] = true; // 1번 정점은 선택된 정점
    
    // 1번 정점에 이어진 정점들까지의 거리를 업데이트
    for(int i = 0; i < Cost[1].size(); i++) {
    	int Next = Cost[1][i].first;
        int Distance = Cost[1][i].second;
        dist[Next] = Distance;
    }
}
```

#### 1 ~ 3 번과정 코드
```C++
for(int i = 1; i <= N - 1; i++) { // N - 1회 반복
    int Min_Cost = INF;
    int Min_Idx = -1;
    for(int j = 1; j <= N; j++) { // j : 노드 인덱스, 가장 가까운 노드 찾기
    	if(selected[j] == true) continue;
        
        if(Min_Cost > dist[j]) { // 가장 비용이 적은 간선 찾기
            Min_Cost = Dist[j];
            Min_Idx = j;
        }
    }
    // 위 과정을 통해 가장 적은 비용인 Min_Cost와 해당 인덱스인 Min_idx가 정해진다.
    // 만약 Min_idx에 -1이 들어있거나, Min_Cost가 INF라면 모든 정점이 연결된 그래프아 아닌 것
    
    selected[Min_Idx] = true; // 가장 비용이 적은 간선으로 이어진 정점 선택
    for(int j = 0; j < Cost[Min_Idx].size(); j++) {
    	// 선택된 정점에서의 간선 거리들 업데이트
        int Next = Cost[Min_idx][j].first;
        int Distance = Cost[Min_Idx][j].second;
        if(selected[Next] == true) continue;
        if(dist[Next] > Distance) Dist[Next] = Distance;
    }    
    
    Answer = Answer + Min_Cost; // 누적 거리
}
```

#### 통합 코드
```C++
#include <bits/stdc++.h>
using namespace std;

#define INF 2147483647

int Prim()
{
    int Answer = 0;
    
    int N;
    cin >> N;

    // 거리정보 입력받기
    // Cost배열
    // Cost[1][] = 1에서 나가는 간선들
    // Cost[1][].first = 다음 목적지, second = 비용
    vector<vector<pair<int, int>>> Cost;

    vector<int> dist(N + 1, INF); // INF로 초기화된 dist배열 준비
    vector<bool> selected(N + 1, false); // 정점을 1번부터 써서 N + 1로

    dist[1] = 0; // 시작점은 1번으로 선택
    selected[1] = true; // 1번 정점은 선택된 정점

    // 시작점에서 연결된 정점들 거리 업데이트
    for (int i = 0; i < Cost[1].size(); i++) {
        int Next = Cost[1][i].first;
        int Distance = Cost[1][i].second;
        dist[Next] = Distance;
    }

    for (int i = 1; i <= N - 1; i++) { // N - 1회 반복
        int Min_Cost = INF;
        int Min_Idx = -1;
        for (int j = 1; j <= N; j++) {
            if (selected[j] == true) continue;

            if (Min_Cost > dist[j]) { // 가장 비용이 적은 간선 찾기
                Min_Cost = dist[j];
                Min_Idx = j;
            }
        }
    // 위 과정을 통해 가장 적은 비용인 Min_Cost와 해당 인덱스인 Min_idx가 정해진다.
    // 만약 Min_idx에 -1이 들어있거나, Min_Cost가 INF라면 모든 정점이 연결된 그래프아 아닌 것

        selected[Min_Idx] = true; // 가장 비용이 적은 간선으로 이어진 정점 선택
        for (int j = 0; j < Cost[Min_Idx].size(); j++) {
            // 선택된 정점에서의 간선 거리들 업데이트
            int Next = Cost[Min_Idx][j].first;
            int Distance = Cost[Min_Idx][j].second;

            if (selected[Next] == true) continue;
            if (dist[Next] > Distance) dist[Next] = Distance;
        }
    
	    Answer = Answer + Min_Cost; // 누적 거리
    }
    
    return Answer;
}
```

#### Priority_Queue를 사용하는 방법
첫번째 방법에서 사용한 `Dist[]`배열을 사용하지 않는다.  
최소힙을 사용하는 방법이라서 일반적으로 Priority_Queue를 사용한다.  

백준 1992번 문제의 풀이인데 전형적인 프림 코드라 똑같게 남긴다.  
[https://www.acmicpc.net/problem/1922](https://www.acmicpc.net/problem/1922)  
```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    ios::sync_with_stdio(false); cin.tie(NULL);

	int N, M;
	cin >> N >> M;

	// line[1]에는 1과 인접한 간선들의 정보가 들어감
	// line[1][0] 은 1에 인접된 첫번째 간선의 정보이며 to, cost 순서인 pair객체
	vector<vector<pair<int, int>>> lines(N + 1); // 정점을 1번부터 쓰니까 N + 1
	int from, to, cost;
	for (int i = 0; i < M; i++) {
		cin >> from >> to >> cost;
		lines[from].push_back({ to, cost });
		lines[to].push_back({ from, cost }); // 반대로 가도 비용 같으니 넣어주기
	}

	priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
	vector<bool> selected(N + 1, false);

	selected[1] = true; // 시작점 무난하게 1번으로
	for (auto p : lines[1]) { // 1번에 연결된 정점들 정보 업데이트
		int adj_vertex = p.first;
		int distance_to_adj = p.second;
		pq.push({ distance_to_adj, adj_vertex }); // dist로 정렬하기 위해 순서 반대로
	}

	int answer = 0;
	while (pq.size()) {
		int distance_to_this = pq.top().first;
		int this_vertex = pq.top().second;
		pq.pop();
		
		if (selected[this_vertex]) continue;

		selected[this_vertex] = true; // 가장 가까운 곳 선택하고
		for (auto p : lines[this_vertex]) { // 이 정점에서의 거리 업데이트
			int adj_vertex = p.first;
			int distance_to_adj = p.second;
			if(!selected[adj_vertex]) pq.push({ distance_to_adj, adj_vertex });
		}

		answer += distance_to_this;		
	}

	cout << answer;

	return 0;
}
```

다익스트라와 유사한 코드이다.  
다익스트라는 거리를 비교해서 더 짧으면 갱신해나가는 방식이고, 프림은 선택되지 않았으면 선택해나가는 방식이다.  
헷갈리지 않아야 겠다.  

#### 예시문제
전형적인 프림 알고리즘 : [https://smallpants.tistory.com/290](https://smallpants.tistory.com/290)
입력이 인접리스트가 아니라 인접배열이라면? : [https://smallpants.tistory.com/291](https://smallpants.tistory.com/291)






※ 참고 문헌
[https://ansohxxn.github.io/algorithm/mst/](https://ansohxxn.github.io/algorithm/mst/)
[https://blog.naver.com/ndb796/221230994142](https://blog.naver.com/ndb796/221230994142)
[https://yabmoons.tistory.com/186](https://yabmoons.tistory.com/186)
[https://yabmoons.tistory.com/362](https://yabmoons.tistory.com/362)
[https://yabmoons.tistory.com/363](https://yabmoons.tistory.com/363)