
# Git 기초
`Git`은 데이터를 파일 시스템 스탭샷의 연속으로 취급하고 크기가 아주 작다.
`Git`은 커밋하거나 프로젝트의 상태를 저장할 때마다 파일이 존재하는 그 순간을 중요하게 여긴다.
파일이 달라지지 않았으면 `Git`은 성능을 위해서 파일을 새로 저장하지 않는다.
단지 이전 상태의 파일에 대한 링크만 저장한다. `Git`은 데이터를 **스냅샷의 스트림**처럼 취급한다.
### 거의 모든 명령을 로컬에서 실행
`Git`은 프로젝트의 히스토리를 조회할 때 서버 없이 조회한다.
그냥 로컬 데이터베이스에서 히스토리를 읽어서 보여준다. 그래서 빠르게 히스토리를 조회할 수 있다.
### Git의 무결성
`Git`은 데이터를 저장하기 전에 항상 체크섬을 구하고 그 체크섬으로 데이터를 관리한다.
그래서 체크섬을 이해하는 `Git`없이는 어떠한 파일이나 디렉토리도 변경할 수 없다.
체크섬은 `Git`에서 사용하는 가장 기본적인 데이터 단위이자 기본 철학이다.
`Git`없이는 체크섬을 다룰 수 없어서 파일의 상태도 알 수 없고 심지어 데이터를 잃어버릴 수도 없다.

`Git`은 `SHA-1`해시를 사용하여 체크섬을 만든다. 만든 체크섬은 40자 길이의 16진수 문자열이다.
`24b9da6552252987aa493b52f8696cd6d3b00373`
`Git`은 모든 것을 해시로 식별하기 때문에 이런 값은 여기저기서 보인다.
실제로 `Git`은 파일을 이름으로 저장하지 않고 해당 파일의 해시로 저장한다.
### 세 가지 상태
- Committed : 데이터가 로컬 데이터베이스에 안전하게 저장됐다는 것을 의미
- Modified : 수정한 파일을 아직 로컬데이터베이스에 커밋하지 않은 것을 의미
- Staged : 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태를 의미
### Git 저장소 만들기
주로 다음 두 가지 중 한 가지 방법으로 `Git`저장소를 쓰기 시작한다.
1. 아직 버전관리를 하지 않는 로컬 디렉토리 하나를 선택해서 `Git`저장소를 적용하는 방법
2. 다른 어딘가에서 `Git`저장소를 Clone하는 방법
### 기존 디렉토리를 Git 저장소로 만들기
Mac
```bash
cd/경로/my_project
```
그리고 아래와 같은 명령을 실행한다.
```bash
git init
```
이 명령은 `.git`이라는 하위 디렉토리를 만든다. `.git` 디렉토리에는 저장소에 필요한 뼈대 파일이 들어있다.
`Git`이 파일을 관리하게 하려면 저장소에 파일을 추가하고 커밋해야 한다.
`git add` 명령으로 파일을 추가하고 `git commit` 명령으로 커밋한다.
```bash
git add *.c
git add LICENSE
git commit -m 'initial project version'
```
### 기존 저장소를 Clone 하기
다른 프로젝트에 참여하려거나 `Git`저장소를 복사하고 싶을 때 `git clone` 명령을 사용한다.
`git clone`을 실행하면 프로젝트 히스토리를 전부 받아온다.
실제로 서버의 디스크가 망가져도 클라이언트 저장소 중에서 아무거나 하나 가져다가 복구하면 된다.
```bash
git clone <클론 할 원격 저장소 주소>
```
### 파일의 상태 확인하기
파일의 상태를 확인하려면 보통 `git status` 명령을 사용한다.
```bash
git status
on branch master
Your branch is up-to-date with 'origin/master' .
nothing to commit, working directory clean
```
위의 내용은 파일을 하나도 수정하지 않았다는 것을 말해준다.
Tracked 파일은 하나도 수정되지 않았다는 의미다.

프로젝트에 `README` 파일을 만들면 `README`파일은 새로 만든 파일이기 때문에 `git status`를 실행하면
`Untracked files`에 들어 있다.
```bash
git status
on branch master
Your branch is up-to-date with 'origin/master' .
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README
nothing added to commit but untracked files present (use "git add" to track)
```
`README` 파일은 "Untracked files" 부분에 속해있는데 이것은 `README` 파일이 Untracked 상태라는 것을 말한다.
`Git` 은 Untracked 파일을 아직 스냅삿(커밋)에 넣어지지 않은 파일이라고 본다.
파일이 Tracked 상태가 되기 전까지는 Git은 절대 그 파일을 커밋하지 않는다.
### 파일을 새로 추적하기
`git add` 명령으로 파일을 새로 추적할 수 있다.
```bash
git add README
```
`git status` 명령을 다시 실행하면 `README` 파일이 Tracked 상태이면서 커밋에 추가될 Staged상태라는 것을 확인할 수 있다.
```bash
git status
On branch master
Your branch is up-to-date with 'origin/master' .
Change to be committed:
  (use "git reset HEAD <file>/..." to unstage)
    new file: README
``` 
"Change to be committed"에 들어있는 파일은 Staged 상태라는 것을 의미한다.
커밋하면 `git add`를 실행한 시점의 파일이 커밋되어 저장소 히스토리에 남는다.
`git add` 명령은 파일 또는 디렉토리의 경로를 아규먼트로 받는다.
디렉토리면 아래에 있는 모든 파일까지 재귀적으로 추가한다.
### Modified 상태의 파일을 Stage 하기
이미 Tracked 상태인 파일을 수정하는 법을 알아보자.
`git add` 명령은 파일을 새로 추적할 때도 사용하고 수정한 파일을 Staged 상태로 만들 때도 사용한다.
Merge 할 때 충돌난 상태의 파일을 Resolve 상태로 만들때도 사용한다.
add의 의미는 프로젝트에 파일을 추가한다기 보다는 다음 커밋에 추가한다고 받아들이는게 좋다.
`git add` 명령을 실행하면 `Git`은 파일을 바로 Staged 상태로 만든다.
### 파일 상태를 짤막하게 확인하기
`git status` 명령으로 확인할 수 있는 내용이 좀 많아 보일 수 있다.
좀 더 간단하게 변경 내용을 보여주는 옵션이 있다.
`git status -s` 또는 `git status --short`처럼 옵션을 주면 현재 변경한 상태를 짤막하게 보여준다.
```bash
git status -s
  M README
MM Rakefile
A lib/git.rb
M lib/simplegit.rb
?? LICENSE.txt
```
아직 추적하지 않는 새 파일 앞에는 `??`표시가 붙는다.
Staged 상태로 추가한 파일 중 새로 생성한 파일 앞에는 `A` 표시가,
수정한 파일 앞에는 `M` 표시가 붙는다.
### 파일 무시하기
어떤 파일은 `Git`이 관리할 필요가 없다.
그런 파일을 무시하려면 `.gitignore`파일을 만들고 그 안에 무시할 파일 패턴을 적는다.
`.gitignore` 파일에 입력하는 패턴의 규칙
- 아무것도 없는 라인이나, `#` 로 시작하는 라인은 무시한다.
- 표준 Glob 패턴을 사용한다.
- 슬래시로 시작하면 하위 디렉토리에 적용되지 않는다.
- 디렉토리는 슬래시를 끝에 사용하는 것으로 표현한다.
- 느낌표로 시작하는 패턴의 파일은 무시하지 않는다.
### Staged와 Unstaged 상태의 변경 내용을 보기
단순히 파일이 변경됐다는 사실이 아니라 어떤 내용이 변경됐는지 살펴보려면 `git status`명령이 아닌
`git diff`명령을 사용해야 한다.
### 변경사항 커밋하기
`Git`은 생성하거나 수정하고 나서 `git add` 명령으로 추가하지 않은 파일은 커밋하지 않는다.
그 파일은 여전히 Modified 상태로 남아 있다.
커밋하기 전에 `git status`명령으로 모든 것이 Staged 상태인지 확인할 수 있다.
그 후에 `git commit` 을 실행하여 커밋한다.
```bash
git commit
```
```vim
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
# new file: README
# modified: CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```
자동으로 생성되는 커밋 메시지의 첫 라인은 비어있고 둘째 라인부터 `git status`명령의 결과가 채워진다.
커밋한 내용을 쉽게 기억할 수 있또록 이 메시지를 포함할 수 잇고 메시지를 전부 지우고 새로 작성할 수 있다.
내용을 저장하고 편집기를 종료하면 `Git`은 입력된 내용(#로 시작하는 내용을 제외한)으로 새 커밋을 하나 완성한다.
### 되돌리기
종종 오나료한 커밋을 수정해야 할 때가 있다.
다시 커밋하고 싶으면 파일을 수정 작업을 하고 Staging Area에 추가한 다음 `--ammend` 옵션을 사용하여
커밋을 재작성 할 수 있다.
```bash
git commit --ammend
```
이 명령은 Staging Area를 사용하여 커밋한다. 만약 마지막으로 커밋하고 나서 수정한 것이 없다면
조금 전에 한 커밋과 모든 것이 같다. 이때는 커밋 메시지만 수정한다.

편집기가 실행되면 이전 커밋 메시지가 자동으로 포함된다. 메시지를 수정하지 않고 그대로 커밋해도 기존의
커밋을 덮어쓴다.

커밋을 했는데 Stage 하는 것을 깜빡하고 빠뜨린 파일이 있으면 아래와 같이 고칠 수 있다.
```bash
git commit -m 'initial commit'
git add forgotten_file
git commit --ammend
```
여기서 실행한 명령어 3개는 모두 커밋 한 개로 기록된다. 두 번째 커밋은 첫 번째 커밋을 덮어쓴다.
### 파일 상태를 Unstage로 변경하기
`git reset HEAD <file>` 이 명령으로 Unstaged 상태로 변경할 수 있다.
### 리모트 저장소
리모트 저장소는 인터넷이나 네트워크 어딘가에 있는 저장소를 말한다.
저장소는 여러 개가 있을 수 있는데 어떤 저장소는 읽고 쓰기 모두 할 수 있고 어떤 저장소는 읽기만 가능할 수 있다.
다른 사람과 함께 일한다는 것은 리모트 저장소를 관리하면서 데이터를 거기에 Push하고 Pull하는 거싱다.
리모트 저장소를 관리한다는 것은 저장소를 추가, 삭제하는 것뿐만 아니라 브랜치를 관리하고 추적할지 말지 등을 관리하는 것을 말한다.
### 리모트 저장소 확인하기
`git remote`명령으로 현재 프로젝트에 등록된 리모트 저장소를 확인할 수 있다.
### 리모트 저장소 추가하기
기존 워킹 디렉토리에 새 리모트 저장소를 쉽게 추가할 수 있는데 `git remote add <단축이름> <url>`명령을 사용한다.
```bash
git remote
origin
git remote add pb <url>
git remote -v
origin <경로>(fetch)
origin <경로>(push)
pb <경로>(fetch)
pb <경로>(push)
```
### 리모트 저장소를 Pull 하거나 Fetch 하기
```bash
git fetch <remote>
```
이 명령은 로컬에는 없지만, 리모트 저장소에 있는 데이터를 모두 가져온다.
`git fetch` 명령은 리모트 저장소의 데이터를 모두 로컬로 가져오지만, 자동으로 Merge 하지 않는다.
그래서 로컬에서 하던 작업을 정리하고 나서 수동으로 Merge 해야 한다.

`git pull` 명령은 리모트 저장소 브랜치에서 데이터를 가져올 뿐만 아니라 자동으로 로컬 브랜치와 Merge
시킬 수 있다.
### 리모트 저장소에 Push 하기
프로젝트를 공유하고 싶을 때 Upstream 저장소에 Push 할 수 있다.
```bash
git push origin master
```
이 명령은 Clone 한 리모트 저장소에 쓰기 권한이 있고, Clone 하고 난 이후 아무도 Upstream 저장소에
Push하지 않았을 때만 사용할 수 있다.
다시 말해 Clone 한 사람이 여러 명 있을 때, 다른 사람이 Push 한 후에 Push 하려고 하면 Push 할 수 없다.
먼저 다른 사람이 작업한 것을 가져와서 Merge한 후에 Push 할 수 있다.
