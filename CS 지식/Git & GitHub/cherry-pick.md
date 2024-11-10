# cherry-pick

## 1. 체리픽이란?
- 다른 브랜치에 있는 커밋을 내 브랜치에 적용시킬 때 사용하는 명령어
- merge나 rebase의 경우 다른 브랜치의 모든 내용을 현재 브랜치에 반영하게 되지만 체리픽은 원하는 특정 커밋만 가져와서 반영할 수 있다.
- 다만 체리픽은 같은 내용의 커밋을 여러개 생성하므로 꼭 필요할 때만 사용하기

## 2. 사용 방법
![](https://blog.kakaocdn.net/dn/brmrgO/btsKDQSdF1S/kpk1IOyWj9YH7WaYaQOgek/img.png)
위와 같은 상황일 때 feature/#1 브랜치의 커밋 중 34b4cab와 13f03ab 커밋을 master 브랜치에 적용하고 싶다면 아래와 같이 사용한다.
```
# git cherry-pick {커밋 해시}
git cherry-pick 34b4cab
git cherry-pick 13f03ab

# 또는 
git cherry-pick 34b4cab 13f03ab

# 연속적인 커밋일 경우
git cherry-pick 34b4cab..fe834e9
// 단 연속 커밋의 경우 내가 적용하고자 하는 커밋의 다음 값을 지정해야 한다.
```

#### 충돌 발생 시
1) 충돌난 코드 수정
2) git add {충돌난 코드 경로}
3) git cherry-pick --continue

#### cherry-pick 중단 시
- git cherry-pick --abort

## 3. 소스트리에서 사용 방법
1) 병합받고자 하는 브랜치로 체크아웃
2) 체리픽 해올 커밋의 브랜치로 가서 해당 커밋 우클릭 후 체리 픽
![](https://blog.kakaocdn.net/dn/On7Hl/btsKDCmmEPj/wAnokDRTUvSXu98LCY5V61/img.png)