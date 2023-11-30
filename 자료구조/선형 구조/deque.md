# deque

## 1) 개념
- 양쪽에서 끝나는 큐라는 의미의 Double ended queue의 약자로 `[데크]` 또는 `[덱]`이라고 읽음

- 메모리 크기가 가변적임
- 새로운 공간에 값을 넣을때 앞부터 채우는게 아니라 앞과 뒤에 공간을 남기고 넣음
- 그래서 앞과 뒤 양쪽에 값을 넣거나 뺄때 효율적임
- 새로운 값을 양쪽에 넣을 일이 많을 때 사용
- 중간위치에 삽입/삭제는 front나 back만큼의 효율은 아니지만 좋은 편

>[!note] Double Ended Queue?
> stack의 경우 top에서는 pop과 push가 가능하지만 반대쪽에서는 아무것도 할 수 없음  
> queue의 경우 front에서는 pop을, back에서는 push를 할 수 있지만 한쪽에서 pop/push 둘다는 불가능  
> deque는 stack과 queue가 합쳐진것이라고 볼 수 있으며 양쪽 끝에서 삽입과 삭제가 모두 가능

## 2) 구현
- C++기반의 Deque STL : [[C++/STL/Container/시퀀스 컨테이너/deque|deque]]

## 3) 시간복잡도
- push : O(1)
- pop : O(1)
- 탐색 : O(1)      // Index 기반의 사용이 가능