# 콜백함수(Callback Func)

일반적인 함수들은 내가 원하는 시점에 호출한다.  
그러나 가끔은 인터럽트가 들어와서 내가 원하지 않는 시점에 함수가 호출 될 수도 있다.  
이때 호출 되는 함수가 콜백함수이다.  
  
예를들면 폰으로 영상을 보고 있다면 영상은 내가 호출한 것  
이때 전화가 오면 전화가 영상 화면을 가려버리는데  
이 전화가 내가 의도한것이 아닌 OS쪽에서 스스로 실행한 콜백함수인것  
  
콜백함수를 흔하게 볼 수 있는 곳은 Application과 OS간 호출  
Application에 콜백함수를 구현하고, OS에 콜백함수를 호출하는 함수를 구현  
그리고 OS에서 특정 이벤트가 발생하면 콜백함수가 호출됨  
  
콜백함수는 함수포인터를 이용해서 구현  

```C++
void EventFunc(){                  // Application에 해당
    printf("이벤트 발생!!");
}
 
void CallbackFunc(void (*fp)()){    // OS에 해당
    if(event 발생 시) fp();
}
 
int main(){
   CallbackFunc(EventFunc);
}
```

위처럼 이벤트가 발생하면 콜백함수가 호출됨  

#프로그래밍용어