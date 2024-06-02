## 1. 클래스에 붙이는 경우
```C++
class Car final 
{
	...
};
```
상속을 불가능하게 만들때 사용한다.  
즉 위의 Car 클래스는 다른 클래스에서 상속받을 수 없다.    
  
## 2. 함수에 붙이는 경우
```C++
class Parent
{
	virtual void func() {}
};

class Child : Parent
{
	void func() overide final {
    	...
    }
}
```

부모클래스에서 virtual로 선언된 함수를 상속받아서 오버라이딩 하는 경우에만 사용 가능  
여기까지가 마지노선이고 더이상 상속 후 오버라이딩 불가능하다는 의미