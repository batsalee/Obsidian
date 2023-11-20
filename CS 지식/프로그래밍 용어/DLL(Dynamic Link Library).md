# DLL(Dynamic Link Library)

## 라이브러리란?  

표준화된 함수나 데이터 타입들을 미리 만들어서 모아둔것  
라이브러리가 언제 메인 프로그램에 연결되냐에 따라 Static Link와 Dynamic Link로 나뉜다  
DLL은 후자인 Dynamic Link에 해당한다  
  
1) Static Link(정적 링크)  
컴파일 시점에 라이브러리가 링커에 연결되어 실행 파일의 일부분이 됨  
자주 쓰는 `#include <iostream>`같은 것이 정적 링크된 라이브러리  

2) Dynamic Link(동적 링크)  
Dynamic Link Library의 약자가 DLL  
정적 링크와는 다르게 실행 파일에서 해당 라이브러리의 기능을 사용할때만 라이브러리 파일을 참조해서 기능을 호출함  
정적링크는 컴파일 시점에 파일에 함수를 복사하고, 동적링크는 함수의 위치만 갖고 있다가 필요할때 그 함수를 호출할 수 있음  
  
## DLL의 이점

1) 더 적은 리소스 사용  
- 코드 하나를 여러 프로그램이 동시에 사용 가능하므로 메모리가 절약됨  
- 메모리 뿐 아니라 저장공간도 줄일 수 있음(정적 링크는 실행파일에 라이브러리 함수가 모두 포함되어 크기가 커짐)  
- 그런 이유로 OS와 프로그램이 더 빠르게 실행됨  

2) 효율적인 개발  
- DLL을 사용하면 모듈식 프로그램을 효율적으로 개발 할 수 있다.  
- 여러 언어 버전이 필요한 큰 프로그램이나 모듈식 아키텍처가 필요한 프로그램을 개발할 수 있다.  

3) 쉬운 배포와 설치  
- DLL내의 함수를 업데이트/수정 하는 경우 DLL을 재배포하기만 하면 프로그램과 DLL을 다시 연결하는 과정이 필요 없음  
- 하나의 DLL만 재배포하면 모든 프로그램이 업데이트 적용되는 셈  

4) 프로그래머들의 작업 분담이 쉬워지고, 재사용성이 뛰어남, 또한 코드의 양이 적어지므로 디버깅이 쉬워짐  
  
> [!warning] DLL 사용시 유의점  
프로그램이 DLL을 사용할 때는 종속성 문제로 프로그램이 실행되지 않을 수 있음  
특히 system32 폴더 내의 DLL 파일은 조심해야함  
종속성이란 프로그램이나 DLL이 다른 DLL의 함수를 사용하는 경우 생김  
이런 경우 해당 프로그램은 더이상 개별 프로그램이 아니며,  
참조한 DLL(종속 DLL)이 업데이트/수정되면 프로그램이 실행되지 않을 수 있음  
  
## DLL 생성법

1) visual studio 기준 프로젝트 생성할때 DLL로 생성
2) 헤더파일과 cpp파일 생성  
  헤더파일은 아래와 같이 작성(DLLStudy.h)  
```
#pragma once
 
#ifdef DLLSTUDY_EXPORTS
#define DLLSTUDY_EXPORTS __declspec(dllexport)
#else
#define DLLSTUDY_EXPORTS __declspec(dllimport)
#endif
 
extern "C" DLLSTUDY_H void usingDLLPrint();
```
extern "C"나 define부의 export, import도 다 적어줘야함  
dll프로젝트는 dllexport 해주고 dll을 사용할 프로젝트는 dllimport를 해주는 것  

3) cpp파일은 아래와 같이 작성(DLLStudy.cpp)
```
#include "pch.h" // 프리 컴파일드 헤더인데 이것도 꼭 필요한건지 확인해봐야 할듯
#include "DLLStudy.h"
#include <iostream>
 
using namespace std;
 
extern "C" DLLSTUDY_H void usingDLLPrint()
{
    cout << "이건 DLL을 링크해서 출력한 문장!!" << endl;
}
```

4) DLLSTUDY_EXPORTS을 속성 -> C/C++ -> 전처리기 -> 전처리 정의에 넣어주고 컴파일 -> 빌드하면 .dll과 .lib 생성완료

## DLL 적용법

dll을 암시적으로 적용해주려면 몇가지 속성을 적용해줘야 함

솔루션 탐색기에서 프로젝트 우클릭 -> 속성
1) 속성 -> C/C++ -> 일반에서 추가 포함 디렉토리에 dll header가 있는 위치를 추가
2) 2) 속성 -> 링커 -> 일반 -> 추가 라이브러리 디렉터리에 .lib 파일이 있는 위치를 추가
3) 3) 속성 -> 링커 -> 입력 -> 추가 종속성에 파일명.lib 을 명시해줍니다.(여기선 DLLStudy.dll)

그리고 나서 아래처럼 사용 가능
```
#include <iostream>
#include "DLLStudy.h"
 
using namespace std;
 
void main()
{
    usingDLLPrint();
}
```

이런 식으로 .dll과 .lib와 .h가 있다면 여러 프로그램에서 dll 하나로 사용 가능

#프로그래밍용어