# priority_queue

## 1. 개념
- 우선순위큐
- 큐에 먼저 들어간 값이 앞에 가는게 아니라 지정된 우선순위에 따라 값이 앞으로 가는 방식
- 가장 우선순위가 높은 값이 front를 유지한다.
	- 기본값으로는 가장 큰 값이 가장 앞으로 감

- 우선순위큐는 [[Heap]]으로 구현되어있다.
- 우선순위 결정 방식에 맞게 만들어지는 heap 구조
- 그러므로 push 후 정렬되는 과정은 O(logN)
- 다만 항상 완벽한 정렬상태를 유지하는 것이 아닌 첫번째 원소만 루트 node에 있고 나머지는 느슨한 정렬상태

## 2. 구현
- C++기반의 Priority_queue STL : [[C++/C++ STL/Container/컨테이너 어댑터/priority_queue|priority_queue]]

## 3. 시간복잡도
- insert : O(logN)
- pop : O(1)         // 1번노드를 가져오면 되므로
- 탐색 : O(N)
- erase : O(N)      // 먼저 탐색 후 삭제해야 하므로