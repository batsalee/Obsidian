# stash(스테이시)

## 1. 스테이시란?
- 작업이 아직 완료되지 않은 상태에서 다른 브랜치로 체크아웃 하고 싶을 때, 결과물을 스테이시 영역에 잠시 보관할 수 있다.

## 2. 사용 방법
- `git stash` : 스테이시에 보관
- `git stash list` : 스테이시 목록 조회
- `git stash pop` : 저장내용을 현재 브랜치에 적용 후 stash 목록에서 drop
- `git stash apply` : stash에 저장된 내용을 브랜치에 적용하되 목록에서 drop하지는 않음
- `git stash drop stash@{O}` : stash에 저장된 내용을 삭제