# DLL(Dynamic Link Library)

## 1. DLL 개념

[[CS 지식/프로그래밍 용어/DLL(Dynamic Link Library)|DLL(Dynamic Link Library)]]

## 2. DLL 생성법

1) visual studio 기준 프로젝트 생성할때 DLL로 생성
2) 헤더파일과 cpp파일 생성  
  헤더파일은 아래와 같이 작성(DLLStudy.h)  
```C++
#pragma once
 
#ifdef DLLSTUDY_EXPORTS
#define DLLSTUDY_EXPORTS __declspec(dllexport)
#else
#define DLLSTUDY_EXPORTS __declspec(dllimport)
#endif
 
extern "C" DLLSTUDY_EXPORTS void usingDLLPrint();
```
extern "C"나 define부의 export, import도 다 적어줘야함  
dll프로젝트는 dllexport 해주고 dll을 사용할 프로젝트는 dllimport를 해주는 것  

3) cpp파일은 아래와 같이 작성(DLLStudy.cpp)
```C++
#include "pch.h" // 프리 컴파일드 헤더인데 dll 프로젝트 생성시 만들어지는 헤더
#include "DLLStudy.h"
#include <iostream>
 
using namespace std;
 
extern "C" DLLSTUDY_EXPORTS void usingDLLPrint()
{
    cout << "이건 DLL을 링크해서 출력한 문장!!" << endl;
}
```

4) DLLSTUDY_EXPORTS을 속성 -> C/C++ -> 전처리기 -> 전처리 정의에 넣어주고 컴파일 후 빌드까지하면 .dll과 .lib 생성완료

## 3. DLL 적용법

dll을 암시적으로 적용해주려면 몇가지 속성을 적용해줘야 함  

솔루션 탐색기에서 프로젝트 우클릭 -> 속성  
1) 속성 -> C/C++ -> 일반에서 추가 포함 디렉토리에 dll header가 있는 위치를 추가
2) 속성 -> 링커 -> 일반 -> 추가 라이브러리 디렉터리에 .lib 파일이 있는 위치를 추가
3) 속성 -> 링커 -> 입력 -> 추가 종속성에 파일명.lib 을 명시해줍니다.(여기선 DLLStudy.dll)

그리고 나서 아래처럼 사용 가능
```C++
#include <iostream>
#include "DLLStudy.h"
 
using namespace std;
 
void main()
{
    usingDLLPrint();
}
```

이런 식으로 .dll과 .lib와 .h가 있다면 여러 프로그램에서 dll 하나로 사용 가능