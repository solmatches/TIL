## ⚒️ Github 프로필에 커밋 그래프 반영이 안될 때

---
<br/>

[[Github 공식 문서]](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/managing-contribution-graphs-on-your-profile/why-are-my-contributions-not-showing-up-on-my-profile)

### 🔷 그래프 커밋 반영 기준

- 등록 시간은 <span id="footnote_utc2">[UTC](#footnote_utc)</span> 시간대를 기준으로 한다.
- issue 등록, pull requests, 토론 활동을한다.
  + **fork한 레포지토리에서의 활동은 인정 안 됨.**
- commit을 한다. (아래 1~3 조건 모두 충족 해야함)
  1. 로컬의 git email 설정이 github에 등록한 email과 같아야 함
  2. 레포지토리의 default (또는 gh-pages) 브런치에 커밋해야 인정됨
  3. fork한 레포지토리에서의 커밋은 인정 안됨. (PR 요청해서 병합해야 인정)
  + 위 조건만 맞으면 24시간 안으로 표시됨

<br/>

### 🔷 나의 케이스
Github 계정 -> 개인 이메일
<br/>
로컬 git 이메일 설정 -> 회사 이메일

**! Github 계정과 로컬 git 이메일을 같게 만들어야 함**

방법은 3가지!
1. (Best) Github 계정에 회사 이메일을 추가
2. 로컬 git 이메일을 Github 계정에 등록된 이메일로 변경
3. Github, 로컬 둘다 새 이메일로 변경

이 중에 나는 3번 방법을 선택.

<span style="color: gray; font-size: 0.9em;">1번은 Github 페이지에 이메일을 추가하려면 메일 인증을 받아야하는데 회사 이메일이 없어졌음.<br/>
2번은 Github에 등록한 이메일이 사용하지 않는 이메일임
</span>
<br/>

### 🔷 해결 방법
1. Github 사이트 로그인 -> 내프로필 누르고 -> Settings -> Emails 클릭.<br/>
2. 등록된 이메일은 예전 계정이므로  ~~삭제하고~~ 새로 사용할 이메일 계정 추가.
<br/><span style="color: gray; font-size: 0.9em;">기존에 등록된 이메일을 삭제하니 기존 이메일로 등록됐던 몇몇 커밋이 사라져서 다시 등록 (텅텅 비어서 커밋 하나도 소중해😭)</span>
3. 로컬의 git email 설정 확인 후 변경

``` bash
# 명령어를 치면 list가 나오는데, 
# user.email=myemail 부분을 확인
git config --list

# 이메일 변경하기
git config user.email "변경할 이메일 주소"

# 현재 프로젝트말고 전역으로 설정하고 싶으면
git config --global user.email "변경할 이메일 주소"

# 잘 변경됐나 확인
git config --list

```

>자 이렇게 하면 앞으로 커밋하는건 커밋 그래프에 잘 반영이 되는데, <br/>
**이미 커밋한 건?**

4. rebase 등으로 기존 커밋을 복사해서 강제 덮어씌우기.

<br/>

덮어 씌우는 데는 2가지 방법이 있다.

  1. rebase의 --amend로 하나하나 재 커밋 후 강제 push
  2. 커밋의 Author를 수정해서 재커밋 후 강제 push

 remote로 강제 push를 해야 하니 혼자 사용하는 작은 프로젝트에서만 사용하도록 주의해야 한다.
 
 또, 커밋을 하나하나 변경해주어야 하므로 커밋이 많다면 시간이 굉장히 오래 걸린다.


<br/>

**1번 방법 - rebase 이용하기**
```bash
# 명령어 입력후 제일 아래로 이동해 커밋을 덮어씌울 첫 번째 커밋을 찾아 hash 코드를 복사.
git log --pretty=format:"%h = %an , %ar : %s" --graph

# rebase 하기
git rebase -i -p ${복사한 해시코드}

# 잠시 후 커밋 목록이 쭉 나오면 'a'를 눌러 수정 모드로 전환하고 (vim 명령어임.) 
# 수정해야 할 커밋들의 'pick' 값을 모두 'edit'으로 변경 후 '!wq'를 입력해 저장

# 그리고 명령창에 'amend' 하거나 변경사항이 만족스러우면 'continue'하라는 안내가 나오면

git commit --amend --author="${닉네임 <변경한 이메일>}"

# 입력 후 수정을 위한 화면이 나오는데, 코드 수정은 안 할거라 '!q'를 입력해서 입력 종료 

# 그러면 명령창의 브랜치명 표시 옆에 (rebase 1/수정할 커밋 개수)가 나옴
# 1개 커밋의 수정이 완료된 것.

# 다음 커밋 수정 실행
git rebase --continue
# 반복
git commit --amend --author="${닉네임 <변경한 이메일>}"
# !q로 빠져나오기

# 모든 커밋 수정이 끝나면 remote에 강제 푸시
git push origin +${브런치 이름}
# 또는
git push -f origin ${브런지 이름}

# 끝!
```

**2번 방법 - Author 수정하기**
``` bash
# 레포지토리 클론하기
git clone ${레포지토리 주소}

# 클론한 레포지토리 경로로 이동
cd ${레포지토리 이름}

# Author 변경 스크립트 실행
git filter-branch --env-filter '
OLD_EMAIL="{예전 이메일}"
CORRECT_NAME="{현재 이름}"
CORRECT_EMAIL="{현재 이메일}"

if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
  export GIT_COMMITTER_NAME="$CORRECT_NAME"
  export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi

if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
  export GIT_AUTHOR_NAME="$CORRECT_NAME"
  export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags

# remote에 강제 push
git push -f origin ${브런치 이름}
# 또는
git push origin +${브런치 이름}
```

<br/>
<br/>

### TMI

커밋을 돌리느라 고생을 좀 했지만 이렇게라도 잔디를 찾을 수 있어서 다행이었다.

TIL를 시작한 이상 잔디와 친하게 지내야하기 때문에 첫 작성은 커밋 카운트가 반영 안 되는 이슈 해결법을 작성했는데, 정답이 있는 건 아니지만 블로그처럼 작성하다 보니 TIL과는 안 어울리는 느낌😂

나중에 블로그 완성하면 거기에 옮겨가야겠다

---
참고

https://wellbell.tistory.com/43
https://minemanemo.tistory.com/147

---

<br/>

<b id="footnote_utc"><a href="#footnote_utc2">UTC</a></b>
Universal Time Coordinated. 국제 표준 시간. 한국은 `UTC+9` 시간대로 국제 표준 시간보다 9시간 빠르다.
