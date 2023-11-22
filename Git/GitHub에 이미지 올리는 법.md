# GitHub에 이미지 올리는 법

1단계) GitHub 서버에 이미지를 업로드해야합니다.  
방법은 아무 레포지토리의 Issues탭에 New Issue로 아래처럼 글쓰기 상태를 만들고 이미지파일을 드래그해서 내용쪽에 드랍합니다.  
![[Pasted image 20231123063528.png|300x300]]

2단계) 업로드가 완료되면 아래와 같이 해당 파일의 경로가 출력됩니다.  
![[Pasted image 20231123063626.png|300x300]]

3단계) 위의 경로에서 URL 부분만 복사해서 아래처럼 README.md 파일에 사용하면 됩니다.

```
<img width="해상도 비율%" src="이미지 경로"/>
```

```
<img width="80%" src="https://github.com/batsalee/TaskManager/assets/109213754/18fe6053-d77d-4462-bf72-aa606aa45a37"/>
```