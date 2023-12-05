# BFS, DFS, 트리순회

## 1. 개념

그래프와 트리에 관련된 알고리즘인 만큼 [[그래프]]와 [[트리]] 자료구조에 대한 이해가 먼저 필요함  

그래프 순회(탐색)알고리즘은 여러종류가 있는데 그 중 대표적인 것이 BFS와 DFS이다.  
또한 그래프에는 많은 종류가 있으며 그 중 하나인 **무방향 연결그래프**가 트리이다.  
즉 트리는 그래프의 일종이며, 그러므로 BFS나 DFS같은 탐색은 그래프에도, 트리에도 적용할 수 있다.  


## 2. BFS와 DFS의 용도 및 장단점

#### BFS
- 용도 : 모든 간선의 가중치가 같은 그래프에서 최단거리 탐색

- 장점
1) 너비를 우선으로 탐색하므로 답이 되는 경로가 여러가지여도 최단 경로임이 보장됨
2) 최단 경로가 존재한다면, 어느 한 경로가 무한히 깊어진다 해도 반드시 최단 경로를 찾을 수 있음
3) 탐색하고자 하는 노드의 위치가 얕을 때 유리함
4) 노드의 갯수가 적을때 유리함

- 단점
1) 노드의 갯수가 많은 경우 다음에 탐색할 노드들을 모두 Queue에 저장하기 때문에 필요없는 노드들까지 저장해야하므로 저장 공간이 많이 필요해짐

#### DFS
- 용도 : 완전탐색, 백트래킹

- 장점
1) BFS에 비해 저장공간의 필요성이 적음, 백트래킹을 해야하는 노드들만 저장해주면 됨
2) 탐색하고자 하는 노드의 위치가 깊을수록, 그 노드가 좌측에 있을수록 BFS보다 유리함

- 단점
1) 찾고자 하는 노드가 없는 경로를 탐색할 때 그 경로가 매우 깊다면 쓸데없는 비효율이 발생함
2) 찾은 경로가 최단경로라는 보장이 없음


## 3. BFS

#### 1) 개념
너비우선탐색(BFS, Breadth First Search)  

이름처럼 너비를 우선순위로 탐색하는 방식으로 먼저 같은 레벨에 있는 노드를 모두 탐색한 후 다음 레벨의 노드로 이동해서 탐색하는 방식  

![[Pasted image 20231205181928.png|300x300]]  
위 트리를 BFS로 탐색한다면 탐색순서는 0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6이 되는 것  

#### 2) 구현

BFS는 Queue를 이용해서 구현  

쉽게 생각하려면 방문된 노드는 큐에 들어감  
그 다음 큐에서 값을 빼서 해당 값의 자식노드들이 방문되었었는지 검사받음  
그리고 그 자식노드들 또한 방문됐으니 큐에 들어감  
```C++
#include <iostream>
#include <vector>
#include <queue>

std::vector<bool> visited;
std::vector<std::vector<int>> adj;

void bfs(int u)
{
	visited[u] = true;
	std::cout << u << ' ';

	std::queue<int> q;
	q.push(u);

	while (q.size()) {
		int here = q.front();
		q.pop();
		for (int there : adj[here]) {
			if (visited[there]) continue;

			visited[there] = true;
			std::cout << there << ' ';
			q.push(there);
		}
	}
}

int main()
{
	std::ios::sync_with_stdio(false);
	std::cin.tie(NULL);

	int number_of_vertex, number_of_edge, start_vertex;
	std::cin >> number_of_vertex >> number_of_edge >> start_vertex;

	visited.resize(number_of_vertex + 1);
	adj.resize(number_of_vertex + 1);

	int u, v;
	for (int i = 0; i < number_of_edge; i++) {
		std::cin >> u >> v;
		adj[u].push_back(v);
		adj[v].push_back(u);
	}

	bfs(start_vertex);

	return 0;
}
```


## 4. DFS

#### 1) 개념

깊이우선탐색(DFS, Depth First Search)  

이름처럼 깊이를 우선순위로 두고 탐색하는 방식으로 한 방향으로 끝까지 내려가서 탐색을 하고 더이상 탐색할 곳이 없으면 한칸 위로 올라와서 다른 깊이로 탐색하는 방식  

![[Pasted image 20231205182437.png|300x300]]  
위 트리를 DFS로 탐색한다면 탐색 순서는 0 -> 1 -> 3 -> 4 -> 2 -> 5 -> 6이 되는 것

#### 2) 구현

DFS는 재귀함수를 이용해서 구현하고 두 가지 방법으로 나뉘며 둘 다 사용할 줄 알아야 함  
1) 다음 vertex가 방문됐었는지 검사 한 후 가는 방식  
2) 무작정 가보고 방문됐었던 곳이면 그냥 return하는 방식  

1번방식(노크하고 진입)  
```C++
void dfs(int here){
    visited[here] = 1; 
    
    for(int there : adj[here]){
        if(!visited[there]) dfs(there);
    }
}
```

2번방식(무작정 진입)  
```C++
void dfs(int here){
    if(visited[here]) return;
    visited[here] = 1;
    
    for(int there : adj[here]){ 
        dfs(there);
    }
}
```

1번방식으로 구현해본 전체 코드
```C++
#include <iostream>
#include <vector>

std::vector<bool> visited;
std::vector<std::vector<int>> adj;

void dfs(int u)
{
	visited[u] = true;
	std::cout << u << ' ';

	for (int v : adj[u]) {
		if (!visited[v]) dfs(v);
	}
}

int main()
{
	std::ios::sync_with_stdio(false);
	std::cin.tie(NULL);

	int number_of_vertex, number_of_edge, start_vertex;
	std::cin >> number_of_vertex >> number_of_edge >> start_vertex;

	visited.resize(number_of_vertex + 1);
	adj.resize(number_of_vertex + 1);

	int u, v;
	for (int i = 0; i < number_of_edge; i++) {
		std::cin >> u >> v;
		adj[u].push_back(v);
		adj[v].push_back(u);
	}

	dfs(start_vertex);

	return 0;
}
```


## 5. 트리순회

트리순회(Tree traversal)는 트리 구조에서 각 노드를 정확히 한번만, 체계적으로 방문하는 과정을 의미  
방문순서에 따라 전위순회, 중위순회, 후위순회, 레벨순회가 있음  

설명은 이진트리 기반으로 되어있지만 다른종류의 트리에서도 일반화시킬 수 있음

#### 1) 전위순회(preorder traversal)

전위순회는 먼저 자신의 노드를 방문하고 그 다음 노드들을 방문하는 방식  
일반적인 DFS의 모습  
```C++
preorder( node )
    if (node.visited == false)
        node.visited = true
        preorder( node->left )
        preorder( node->right )
```

#### 2) 중위순회(inorder traversal)

왼쪽 노드를 먼저 방문 후 자신의 노드를 방문하고 그 다음 오른쪽 노드를 방문하는 방식  
```C++
inorder( node )
    if (node.visited == false) 
        inorder( node->left )
        node.visited = true
        inorder( node->right )
```

#### 3) 후위순회(postorder traversal)

자식들 노드를 먼저 방문한 후 자신의 노드를 방문하는 방식
```C++
postorder( node )
    if (node.visited == false) 
        postorder( node->left ) 
        postorder( node->right )
        node.visited = true
```

#### 4) 레벨순회(level traversal)

루트로부터 위에서 아래로 레벨순으로 순회하는 방식
일반적인 BFS의 모습

#### 5) C++ 기반의 전위순회/중위순회/후위순회
```C++
#include <bits/stdc++.h>
using namespace std; 
vector<int> adj[1004]; 
int visited[1004];

void preOrder(int here) {
  	if(visited[here] == 0) {
  		visited[here] = 1; 
  		cout << here << ' ';
  		if(adj[here].size() == 1) 
	  		preOrder(adj[here][0]);
  		if(adj[here].size() == 2) {
  			preOrder(adj[here][0]); 
  			preOrder(adj[here][1]);
		}
	}
}  

void inOrder(int here) {   	
	if(visited[here] == 0) { 
  		if(adj[here].size() == 1) { 
  			inOrder(adj[here][0]); 
	  		visited[here] = 1; 
	  		cout << here << ' ';
		}
		else if(adj[here].size() == 2) {
  			inOrder(adj[here][0]); 	  		
			visited[here] = 1; 			
	  		cout << here << ' ';  			
			inOrder(adj[here][1]);
		}
		else {
	  		visited[here] = 1; 
	  		cout << here << ' '; 
		}
	}
}

void postOrder(int here) { 
  	if(visited[here] == 0) { 
  		if(adj[here].size() == 1)
	  		postOrder(adj[here][0]);
  		if(adj[here].size() == 2) {
  			postOrder(adj[here][0]); 
  			postOrder(adj[here][1]);
		}
  		visited[here] = 1; 
  		cout << here << ' ';
	} 
} 
 
int main(){
	adj[1].push_back(2);
	adj[1].push_back(3);
	adj[2].push_back(4);
	adj[2].push_back(5); 
	int root = 1;
    cout << "\n 트리순회 : postOrder \n";
    postOrder(root); memset(visited, 0, sizeof(visited));
    cout << "\n 트리순회 : preOrder \n"; 
    preOrder(root); memset(visited, 0, sizeof(visited)); 
    cout << "\n 트리순회 : inOrder \n"; 
    inOrder(root); 
    return 0;
}
```




★ 참고 문헌
[https://velog.io/@vagabondms/DFS-vs-BFS](https://velog.io/@vagabondms/DFS-vs-BFS)
[https://blog.naver.com/jhc9639/222289089015](https://blog.naver.com/jhc9639/222289089015)