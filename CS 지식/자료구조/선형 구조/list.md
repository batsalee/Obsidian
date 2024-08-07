# list(연결리스트)

## 1. 개념
- 메모리의 동적할당을 기반으로 구현

- node라고 하는 여러개의 메모리구조에 데이터를 저장
- node는 데이터필드와 링크필드로 구분되서 데이터필드에는 값을, 링크필드에는 다음 노드의 주소를 저장
- 즉, 각 node들이 자신의 값과 다음 node의 주소를 갖는 구조

- 보통 마지막 노드는 다음 노드가 없으니 링크필드에 nullptr을 넣음
- 링크필드가 있기 때문에 메모리에 순차적으로 존재할 필요가 없고, 링크에 의해 논리적인 순서를 표현하는 자료구조

- 추가, 삽입, 제거 등에서 효율이 좋으나 탐색에선 효율이 좋지 않음
- 추가, 삽입시에는 만약 2번노드와 3번노드 사이에 삽입하려면 2번노드의 링크필드를 새 노드의 주소로 변경하고 새 노드의 링크필드를 3번노드의 주소로 작성하면 끝
- 제거시에는 데이터를 실제 삭제할 필요 없이 2, 3, 4번 노드가 있을때 3번 노드를 삭제하려면 2번노드의 링크필드를 4번노드의 주소로 변경만 하면 끝

- 탐색시에는 연결리스트는 메모리에 순차적으로 있지 않으므로 시작점부터 노드를 하나씩 타면서 탐색해야 하다보니 시간복잡도가 O(n)이 되므로 비효율이 발생할 수 있음

## 2. 종류

- 단순 연결 리스트(단방향 연결 리스트), 이중 연결 리스트(양방향 연결 리스트), 원형 연결 리스트가 있음

#### 단순 연결 리스트(단방향 연결리스트)
- 1, 2, 3번 노드가 있다면 1번은 2번만, 2번은 3번만 가리킴

- 다음 노드만 가리키면 되므로 링크필드의 크기가 1칸만 필요하므로 메모리 공간면에서 이득
- 삽입 삭제시에도 링크필드를 1칸만 변경하면 되므로 처리속도가 약간 더 빠름

- 탐색면에서는 비효율적이고 성능이 비교적 적음          
- 탐색은 현재 위치가 99번 노드라면 98번 노드를 찾기위해 한칸 앞으로 가서 끝인게 아니라 1번노드부터 탐색해야함

#### 이중 연결 리스트(양방향 연결리스트)
- 1번과 2번이 서로 가리키고, 2번과 3번이 서로 가리킴

- 중간위치에 삽입, 삭제가 많을때 사용하면 유리함
- 단순 연결 리스트보다 양방향의 노드를 저장해야 하므로 노드의 메모리 크기가 커지지만 탐색면에서 유리

#### 원형 연결 리스트
- 1번이 2번을, 2번이 3번을, 3번이 1번을 가리킴

## 3. 구현
- C++기반의 단방향 연결리스트 STL : [[forward_list]]
- C++기반의 양방향 연결리스트 STL : [[C++/C++ STL/Container/시퀀스 컨테이너/list|list]]

## 4. 시간복잡도
- node 검색 : O(n)
- node 삽입 : O(1)
- node 삭제 : O(1)