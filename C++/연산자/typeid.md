# typeid

자료형, 클래스, 함수, 객체, enum, 사용자정의 클래스, 멤버함수, 람다함수를 받을수 있는 연산자이며 해당 자료형이 무엇인지 알려주는 기능

`cout << typeid(int).name() << endl;` 는 int를 출력
`cout << typeid(cout).name() << endl;` 는 `class std::basic_ostream,char, struct std::char_traits<char>>`를 출력