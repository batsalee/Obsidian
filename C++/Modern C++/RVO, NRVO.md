# RVO, NRVO

RVO와 NRVO의 개념에 대한 기록  
굳이 복잡한 내용까지 쓸 필요는 없을듯하고 간단하게 개념만 정리해둬도 충분할듯하다.  
핵심은 return은 컴파일러에 의해 최적화되니 move를 사용하지 말자라는 것  

RVO = Return Value Optimization  
반환값 최적화 기법  

NRVO = Named RVO  
그냥 이름 있는 RVO다.  

이름 없는 RVO란 아래와 같은것이고  
```C++
return A{};
```

이름 있는 RVO는   
```C++
A a;
return a;
```

무슨 말이냐면 return하는 시점에서 컴파일러가 복사를 수행하는게 아닌 최적화를 수행한다.  
최적화 방법은 애초에 반환할 값을 처음부터 반환할 메모리 공간에 생성해서 사용한다.  
이러면 이동이나 복사같은게 필요없다.  
그래서 이것때문에 return쪽에 move를 쓰면 오히려 최적화를 막게되므로 return에는 move를 쓰지 않는다.  

move를 쓰면 오히려 이동생성자가 있다는 전제하에 불필요한 이동이 발생하게 되는데 RVO는 이미 그 위치에 값이 가있으니 이동시킬 이유가 없다.  
만약 최적화가 어려워서 불가피하게 복사해야 하는 경우에는 Guaranteed 키워드를 사용하면 복사 없이 사용 가능하다고 한다.  






※ 참고 문헌  
[https://post.naver.com/viewer/postView.nhn?volumeNo=9735713&memberNo=559061](https://post.naver.com/viewer/postView.nhn?volumeNo=9735713&memberNo=559061)