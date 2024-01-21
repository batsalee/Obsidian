# Queue

## 1. 개념
- First In First Out(선입선출)
- 사람들이 일자로 줄서있는 모습
- queue로 데이터가 들어오는걸 Enqueue, queue에서 데이터가 나가는걸 Dequeue라고 함

## 2. 종류
- 일반적인 Queue, 원형 Queue, Priority Queue가 있음

#### 일반적인 Queue
- 가장 먼저 들어온 값을 front로, 가장 마지막에 들어온 값을 back으로 칭함
- 즉 pop을 하면 front가 Dequeue되고, push를 하면 back에 Enqueue된다

- 하지만 queue는 front가 나가면 인덱스 0이 비게되므로 모든 값을 한칸씩 당겨줘야 함  
	- 이런 문제점을 해결하기 위해 원형큐라는 방식이 있음

#### 원형 Queue
- front와 back 대신 front와 rear라는 용어 사용
- front는 ==값이 있는== 가장 첫번째 위치를 의미하며 0번 위치를 의미하는게 아님
- rear는 ==값이 있는== 가장 마지막 위치

- 처음 시작시에는 front와 rear 둘다 0에서 시작
- 새로운 값이 들어오면 rear++한다
- pop요청이 들어오면 front에 있는 값이 나가고 front++하면 됨

#### Priority Queue
[[CS 지식/자료구조/선형 구조/Priority_queue|Priority_queue]]

## 3.구현
- C++기반의 Queue STL : [[C++/C++ STL/Container/컨테이너 어댑터/queue|queue]]

## 4. 시간복잡도
- push : O(1)
- pop : O(1)
- search : O(n)