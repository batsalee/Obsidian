# nullptr

C언어에서 기존에 NULL은 그냥 `#define NULL 0` 이어서  
이게 진짜 0을 의미하는지 포인터주소값 0을 의미하는지 구분할 수가 없었음  
  
그래서 C++ 11에 nullptr이라는 키워드를 추가했고   
nullptr은 정확히 포인터주소값 0을 의미함  
즉 char* str = nullptr; 처럼 쓴다면 나중에 잘못 사용할 여지를 줄여줌