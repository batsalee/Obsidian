# 사용방법

#### 1. stdc++.h 파일 다운로드
https://github.com/batsalee/Property/tree/main/CodingTest/bits

#### 2.Visual Studio가 설치된 폴더의 헤더파일들이 있는곳에 bits 폴더 만들기
visual studio 설치경로는 대략 아래와 같음
C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\VC\\Tools\MSVC\\14.34.31933\\include
![[Pasted image 20240205204044.png|500x350]]

#### 3. bits 폴더 안에 stdc++.h 파일 넣기
![[Pasted image 20240205204147.png|500x350]]

#### 4. 프로젝트 속성 변경
1) 프로젝트 속성페이지 -> C/C++ -> 명령줄 -> 추가옵션에 `/Zc:__cplusplus`추가
2) 프로젝트 속성페이지 -> 구성 속성 -> 일반 -> C++ 언어 표준 원하는 버전으로 변경(예 : C++20)
이걸 안바꿔주면 기본적으로 C++

그 후 사용할때
`#include <bits/stdc++.h>` 추가하고 사용


# 장단점
1) 장점
- 프로그래밍 대회에서 쓸데없는 시간낭비를 줄여주므로 사용하면 좋습니다.
- 필요한 헤더 파일 include 구문을 작성하는데 시간을 줄여줍니다.
- STL이나 GNU C++의 모든 함수들을 기억할 필요가 없습니다.

2) 단점
- bits/stdc++.h 헤더는 GNU C++ 라이브러리의 표준 헤더가 아니기 때문에, GCC가 아닌 다른 컴파일러로 빌드를 하려고 한다면 실패합니다.
- 쓸데없는 파일들까지 추가시켜서 컴파일 시간이 늘어납니다.
- 표준 C++이 아니기 때문에 이식성이 있지도 않고, 컴파일러 종속적입니다.
