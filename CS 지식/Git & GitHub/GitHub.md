# GitHub

## 1. github란?

- git 리포지토리를 업로드할 수 있는 웹사이트
- 다른 사람들과 협력을 매우 용이하게 해줌

## 2. github를 쓰는 이유

깃허브는 Git리포지토리를 저장하는 기능 외에도 여러 이점이 있음  
- 이슈를 사용해 버그를 기록하거나 개발하고 싶은 새로운 기능을 구체화 할 수 있음
- 다른 브랜치나 다른 기능에 협력할 수 있음
- 진행중인 작업을 확인할 수 있으며 최근 변경내역도 확인할 수 있고 팀 전체의 진척상황도 확인할 수 있음

## 3. github에 처음 코드 올리는 법

- 개발중인 프로젝트가 있다면  레포지토리 생성 후 처음 뜨는 창에서 두번째꺼를 복사해서 터미널에 붙여넣기 하면 됨
- 그 내용이 아래의 3줄
```
git remote add origin (원격 저장소 주소)
git branch -M main
git push -u origin main
```
1) 첫줄은 원격 저장소 주소를 origin이라는 별명으로 설정하는 개념, 당연히 다른 이름도 가능  
	만약 github에도 올리고 gitlab에도 올리고 다른곳도 올리고 싶을 수 있으니 각 주소를 별명을 만들어서 add하는 것
2) 두번째 줄은 깃허브의 브랜치의 이름을 main으로 변경
	기존에는 master였는데 이게 인종차별적/신분제도적 여지가 있어서  main으로 쓰는것을 권장한다고 함
3) 세번째 줄은 로컬의 main브랜치를 origin이라고 이름치은 원격으로 push하는 것

## 4. github에서 코드 다운받는 법

1) .git 제외 다운받는 법(Clone)
	그냥 .git을 제외한 코드뭉치들만 다운받으려면 레포지토리 오른쪽위에 <>Code 클릭하고 local에서 download zip 선택  

2) .git 포함 다운받는 법(fork)
	버전관리 내역까지 받고싶다거나 협업이라 .git까지 같이 다운받아야 하는 경우 <>Code에서 https 주소 복사 후  
	로컬의 다운받을 위치의 폴더로 가서 우클릭 -> git bash 실행 -> git clone 주소 하면 복사 됨  

> [!note] git pull과의 차이점
> pull은 이미 코드 다운받은 상황에서 origin에서 변경된 내용만 받아오는 것, 위 두가지는 처음에 코드 다운받는 내용

## 5. git push  

위의 3번문단에서 git push -u origin main을 했었다면 push할 위치가 지정된 것이므로 그 이후부터는 그냥 git push만 하면 됨  

#### 하지만 만약 여러 사람이 동시에 push를 한다면?
만약 1번내용 push된 후 2번내용 push되고 이러면 1번내용은 다 덮어씌워져 버림  
그러므로 여러 사람이 push를 할때는 해결법이 필요함  

push하려는 시점에 이미 origin에 다른 사람이 push한 변경점이 있어서 내 local과 달라진 점이 있다면 push 불가능  
반드시 origin의 내용을 pull받아와서 내 local과 merge한 후 충돌해결까지 다 해야만 push가 가능해짐  
즉 받아올게 있으면 반드시 먼저 받아온 후에 push 해야함  

>[!warning] push 주의사항
> push하려면 항상 원격저장소의 최신버전과 내 로컬 버전이 맞춰져 있어야 한다!

>[!warning] 강제로 push하는 법
> 만약 로컬의 내용이 원격의 내용보다 뒤쳐져있어서 push가 안될때 pull하지 않고 강제로 push하는 방법이 있음  
> 어떨때 쓰냐면 원격에 있는 내용이 문제가 있어서 local의 내용으로 강제로 바꿔버려야 할 때  
> 단 이 경우 협업시에는 모두 합의한 상황에서 써야 함, 다른 사람들이 작업해서 업로드한게 다 날아가므로  
> 로컬의 내용을 충돌 전으로 reset한 후에 `git push --force` 하면 됨

## 6. git pull

pull한 후 push하는 방법  

merge방식과 rebase방식 두가지가 있음  
	단 여기서의 rebase는 원격이 아닌 내 local쪽을 바꾸는 거라서 협업때도 써도 문제 없음  
```
git pull --no-rebase     // merge 방식(default라서 그냥 git pull 까지만 써도 이걸로 적용됨) 
git pull --rebase        // rebase 방식
```
위의 두 방법 중 하나를 사용하면 다른 사람들이 바꾼 내용들이 내 로컬로 와서 변경됨  
여기서도 마찬가지로 rebase 방식은 add, rebase --continue 하면서 해결  

pull로 충돌해결하고 나서 push하면 됨  


>[!note] fetch와 pull의 차이점
> fetch는 확인만 하고 가져오지는 않음  
> pull은 확인 후 가져와서 갱신까지 함  


## 7. git remote

원격 목록 보기 => `git remote`  
만약 github에 origin 하나만 해놨으면 origin만 나오고, 여러 원격이 있으면 다 나옴  
자세히 보려면 `git remote -v`  

## 8. github에서 branch 만들기

1단계) 레포지토리 들어가서 왼쪽위에 main(혹은 master)클릭해서 새 브랜치 이름 적고 아래에 선택하면 됨  
2단계) 아직 원격에만 만들어졌고 로컬의 .git은 모르는 상황이니 갱신해줘야 함 => git fetch  
3단계) fetch로 원격의 변경사항 확인했으면 `git switch -t origin/원격브랜치이름`로  
	원격에 새로 생긴 브랜치를 로컬에도 같은 이름으로 생성해서 연결하고 switch  

원격의 브랜치를 삭제하고 싶다면 `git push (원격 이름) --delete (원격 브랜치명)`


#git