# 반복자(iterator)

컨테이너의 원소에 접근할 수 있는 index 또는 포인터같은 객체  
예를들어 vector는 []를 이용해서 직접접근도 가능하고, iterator를 이용해서도 가능  

- 컨테이너에 iterator 멤버 타입으로 정의되어 있음  
  
- vector의 반복자는 begin과 end함수로 얻을 수 있음  
vector.begin()은 벡터가 시작하는 위치를 가리키지만, vector.end()는 벡터의 끝보다 한칸뒤를 가리킴  
vector.begin() == vector.end() 라면 벡터가 비어있다라고 판단할 수 있게 하기 위해  
  
std::vector<int>::iterator itr = vec.begin(); 이라고 했을 때 itr은 포인터처럼 쓰이므로  
vec의 첫 값을 알고싶으면 cout<< *itr; 처럼 사용하면 됨  
  
vector 원소들의 반복은 Range Based for를 쓰면 더 좋지만 포인터나 배열처럼 쓰고 싶다면  
itr != vec.end(); 처럼 쓰면 끝이 아니면 반복한다 처럼 쓸 수 있음  
  
vector.insert(itr값, 넣을값); 처럼 써서 값을 중간에 넣을 수 있음  
vector.insert(vec.begin() + 2, 10);  
  
vector.erase(itr값); 처럼 써서 해당 위치의 값을 지울 수 있음  
vector.erase(vec.begin()+3); // vec[3]을 지우고 4부터 한칸씩 땡김  
  
* vector에 insert나 erase할때의 주의점  
벡터에 값을 추가하거나 삭제할 경우 기존의 itr은 무의미해짐(저절로 한칸 옮겨서 계산 안해주나봄)  
그러므로 insert나 erase 바로 뒷줄에 itr = vec.begin()같은걸로 다시 값을 지정해줘야함

이런 불편함을 해결하기 위해 remove 사용  
  
const_iterator  
std::vector<int>::const_iterator citr = vec.cbegin() + 2;  
이렇게 쓰면 *citr = 30; 같은건 오류가 난다.  
많은 경우 반복자로 값을 참조만 하고 변경은 하지 않으니 이럴땐 const를 쓰는게 안전하다  
const_iterator는 begin end 앞에 c를 붙여서 cbegin(), cend()로 사용  
  
reverse_iterator(역반복자)  
벡터 뒤에서부터 거꾸로 가는 반복자  
ritr = vec.rbegin() 는 벡터의 마지막 원소를 가리키고  
vec.rend()는 벡터의 첫번째 원소보다 한칸 앞을 가리킴  
그러므로 itr을 ++ 하면 한칸씩 앞으로감(일반적으로 인덱스 쓸때 --로 하던거랑 반대로)  
  
const_reverse_iterator도 있음 이건 crbegin(), crend()로 사용  
  
reverse_iterator를 쓰는 이유  
for(auto i = vec.size()-1; i>=0; i--)  
cout << vec[i];   
같은 상황일때 잘 될거같지만 vector의 index를 담는 변수가 unsigned 변수라서 i가 -1이 되는게 아니라  
오버플로우가 일어나서 가장 큰 수가 됨  
그러니 무한루프에 빠지게 되므로 저 코드는 사용할 수 없는 코드임  
그러니 반대로 수행하는 반복문은 반드시 reverse_iterator를 쓰자  
  