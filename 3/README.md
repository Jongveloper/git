# Git 브랜치
코드를 통째로 복사하고 나서 원래 코드와는 상관없이 독립적으로 개발을 진행할 수 있는데, 이렇게 독립적으로 개발하는 것이 브랜치다.
다른 버전 관리 시스템과는 달리 Git은 브랜치를 만들어 작업하고 나중에 Merge 하는 방법을 권장한다.심지어 하루에 수십번씩 해도 괜찮다.
### 브랜치란 무엇인가
Git은 데이터를 Change Set이나 변경사항(Diff)으로 기록하지 않고 일련의 스냅샷으로 기록한다.
커밋하면 Git은 현 Staging Area에 있는 데이터의 스냅샷에 대한 포인터, 저자나 커밋 메시지 같은 메타데이터, 이전 커밋에 대한 포인터 등을 포함하는 커밋 개체를 저장한다. 이전 커밋 포인터가 적어도 하나씩 있고 브랜치를 합친 Merge 커밋 같은 경우에는 이전 커밋 포인터가 여러 개가 있다.
`git commit`으로 커밋하면 먼저 루트 디렉토리와 각 하위 디렉토리의 트리 개체를 체크섬과 함께 저장소에 저장한다.
그다음에 커밋 개체를 만들고 메타데이터와 루트 디렉토리 트리 개체를 가리키는 포인터 정보를 커밋 개체에 넣어 저장한다.
그래서 필요하면 언제든지 스냅샷을 다시 만들 수 있다.

이 작업을 마치고 나면 Git저장소에는 다섯 개의 데이터 개체가 생긴다. 각 파일에 대한 Blob 세 개, 파일과 디렉토리 구조가 들어 있는 트리 개체 하나, 메타데이터와 루트 트리를 가리키는 포인터가 담긴 커밋 개체 하나이다.
### 새 브랜치 생성하기
```bash
git branch testing
```
새로 만든 브랜치도 지금 작업하고 있던 마지막 커밋을 가리킨다.
지금 작업 중인 브랜치가 무엇인지 Git은 어떻게 파악할까. 다른 버전 관리 시스템과는 달리 Git은 `HEAD` 라는 특수한 포인터가 있다. 이 포인터는 지금 작업하는 로컬 브랜치를 가리킨다. 브랜치를 새로 만들었지만, Git은 아직 `master`브랜치를 가리키고 있다. `git branch` 명령은 브랜치를 만들기만 하고 브랜치를 옮기지 않는다.
`git log`명령에 `--decorate`옵션을 사용하면 쉽게 브랜치가 어떤 커밋을 가리키는지도 확인할 수 있다.
### 브랜치 이동하기
`git checkout` 명령으로 다른 브랜치로 이동할 수 있다.
```bash
git checkout testing
```
이렇게 하면 HEAD는 testing 브랜치를 가리킨다.

### 브랜치와 Merge의 기초
실제 개발과정에서 겪을 만한 예제를 하나 살펴보자. 브랜치와 Merge는 보통 이런식으로 진행한다.
1. 웹사이트가 있고 뭔가 작업을 진행하고 있다.
2. 새로운 이슈를 처리할 새 Branch를 하나 생성한다.
3. 새로 만든 Branch에서 작업을 진행한다.
이때 중요한 문제가 생겨서 그것을 해결하는 Hotfix를 먼저 만들어야 한다. 그러면 아래와 같이 할 수 있다.
1. 새로운 이슈를 처리하기 이전의 운영(Production) 브랜치로 이동한다.
2. Hotfix 브랜치를 새로 하나 생성한다.
3. 수정한 Hotfix 테스트를 마치고 운영 브랜치로 Merge한다.
4. 다시 작업하던 브랜치로 옮겨가서 하던 일을 진행한다.
### 브랜치의 기초
지금 작업하는 프로젝트에서 이전에 `master` 브랜치에 커밋을 몇 번 했다고 가정한다.
이슈 관리 시스템에 등록된 53번 이슈를 처리한다고 하면 이 이슈에 집중할 수 있는 브랜치를 새로 하나 만든다.
브랜치를 만들면서 Checkout까지 한 번에 하려면 `git checkout` 명령에 `-b`라는 옵션을 추가한다.
```bash
git chekcout -b iss53
Switched to a new branch "iss53"
```
위 명령은 아래 명령을 줄여놓은 것이다.
```bash
git branch iss53
git checkout iss53
```
`iss53`브랜치를 Checkout 했기 때문에(즉,`HEAD`는 `iss53`브랜치를 가리킨다) 뭔가 일을 하고 커밋하면 `iss53`브랜치가 앞으로 나아간다.

다른 상황을 가정해보자. 만드는 사이트에 문제가 생겨서 즉시 고쳐야 한다. 버그를 해결한 Hotfix에 `iss53`이 섞이는 것을 방지하기 위해 `iss53`과 관련된 코드를 어딘가에 저장해두고 원래 운영 환경의 소스로 복구해야 한다. Git 을 사용하면 이런 노력을 들일 필요 없이 그냥 `master`브랜치로 돌아가면 된다.

그렇지만, 브랜치를 이동하려면 해야 할 일이 있다. 아직 커밋하지 않은 파일이 Checkout 할 브랜치와 충돌 나면 브랜치를 변경할 수 없다. 브랜치를 변경할 때는 워킹 디렉토리를 정리하는 것이 좋다. 이런 문제를 다루는 방법은(주로, Stash이나 커밋 Amend에 대해) 나중에 Stasingrhk Cleaning에서 다룰 것이다. 지금은 작업하던 것을 모두 커밋하고 `master`브랜치로 옮긴다.
```bash
git checkout master
Switched to branch 'master'
```
이때 워킹 디렉토리는 53번 이슈를 시작하기 이전 모습으로 되돌려지기 때문에 새로운 문제에 집중할 수 있는 환경이 만들어진다. Git은 자동으로 워킹 디렉토리에 파일들을 추가하고, 지우고, 수정해서 Checkout 한 브랜치의 마지막 스냅샷으로 되돌려 놓는다는 것을 기억해야 한다.

이젠 해결해야 할 핫픽스가 생겼을 때를 살펴보자. `hotfix`라는 브랜치를 만들고 새로운 이슈를 해결할 때까지 사용한다.
```bash
git checkout -b hotfix
Switched to a new branch 'hotfix'
vim index.html
git commit -a -m 'fixed the broken email address'
[hotfix 1fb7853] fixed the broken email address
 1 file changed, 2 insertions(+)
```
운영 환경에 적용하려면 문제를 제대로 고쳤는지 테스트하고 최종적으로 운영환경에 배포하기 위해 `hotfix`브랜치를 `master`브랜치에 합쳐야 한다. `git merge`명령으로 아래와 같이 한다.
```bash
git checkout master
git merge hotfix
Updating f42c576..3a0874c
Fast-forward
  index.html | 2 ++
  1 file changed, 2 insertions(+)
```
Merge 메시지에서 "fast-forward"가 보이는가. `hotfix` 브랜치가 가리키는 `C4` 커밋이 `C2` 커밋에 기반한 브랜치이기 때문에 브랜치 포인터는 Merge 과정 없이 그저 최신 커밋으로 이동한다. 이런 Merge 방식을 "Fast forward"라고 부른다. 다시 말해 A 브랜치에서 다른 B 브랜치를 Merge 할 때 B 브랜치가 A 브랜치 이후의 커밋을 가리키고 있으면 그저 A 브랜치가 B 브랜치와 동일한 커밋을 가리키도록 이동시킬 뿐이다.

급한 문제를 해결하고 `master` 브랜치에 적용하고 나면 다시 일하던 브랜치로 돌아가야 한다. 이제 더 이상 필요없는 `hotfix`브랜치는 삭제한다. `git branch` 명령에 `-d` 옵션을 주고 브랜치를 삭제한다.
```bash
git branch -d hotfix
Deleted branch hotfix (3a0874c) .
```

위에서 작업한 `hotfix`가 `iss53` 브랜치에 영향을 끼치지 않는다는 점을 이해하는 것이 중요하다.
`git merge master` 명령으로 `master` 브랜치를 `iss53`브랜치에 Merge 하면 `iss53` 브랜치에 `hotfix`가 적용된다. 아니면 `iss53`브랜치가 `master`에 Merge 할 수 있는 수준이 될 때까지 기다렸다가 Merge하면 `hotfix`와 `iss53`브랜치가 합쳐진다.
### Merge의 기초
53번 이슈를 다 구현하고 master 브랜치에 Merge 하는 과정을 살펴보자.
`iss53` 브랜치를 `master` 브랜치에 Merge하는 것은 앞서 살펴본 `hotfix` 브랜치를 Merge하는 것과 비슷하다.
`git merge` 명령으로 합칠 브랜치에서 합쳐질 브랜치를 Merge 하면 된다.
```bash
git checkout master
Switched to branch 'master'
git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1+
1 file changed, 1 insertion(+)
```
`hotfix`를 Merge 했을 때와 메시지가 다르다. 현재 브랜치가 가리키는 커밋이 Merge 할 브랜치의 조상이 아니므로
Git은 `Fast-forward`로 Merge 하지 않는다. 이 경우에 Git은 각 브랜치가 가리키는 커밋 두 개와 공통 조상 하나를 사용하여 3-way Merge를 한다.
단순히 브랜치 포인터를 최신 커밋으로 옮기는 게 아니라 3-way Merge 의 결과를 별도의 커밋으로 만들고 나서 해당 브랜치가 그 커밋을 가리키도록 이동시킨다. 그래서 이런 커밋은 부모가 여러 개고 Merge 커밋이라고 부른다.

iss53 브랜치를 master에 Merge하고 나면 더는 iss53 브랜치가 필요 없다. 다음 명령으로 브랜치를 ㅅ각제하고 이슈의 상태를 처리 완료로 표시한다.
```bash
git branch -d iss53
```
### 충돌의 기초
가끔씩 3-way Merge가 실패할 때도 있다. Merge 하는 두 브랜치에서 같은 파일의 한 부분을 동시에 수정하고 Merge하면 Git은 해당 부분을 Merge 하지 못한다. 예를 들어, 53번 이슈와 `hotfix`가 같은 부분을 수정했다면 Git은 Merge하지 못하고 아래와 같은 충돌 메시지를 출력한다.
```bash
git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```
Git은 자동으로 Merge 하지 못해서 새 커밋이 생기지 않는다. 변경사항의 충돌을 개발자가 해결하지 않는 한 Merge과정을 진행할 수 없다. Merge 충돌이 일어났을 때 Git이 어떤 파일을 Merge 할 수 없었는지 살펴보려면 `git status` 명령을 이용한다.
```bash
git status
You have unmerged paths.
  (fix conflicts and run "git commit")
Unmerged paths:
  (use "git add <file>..." to mark resolution)
  
    both modified:      index.html
no changes added to commit (use "git add" and/or "git commit -a")
```
충돌이 일어난 파일은 unmerged 상태로 표시된다. Git은 충돌이 난 부분을 표준 형식에 따라 표시해준다. 그러면 개발자는 해당 부분을 수동으로 해결한다.
### 브랜치 관리
지금까지 브랜치를 만들고, Merge하고, 삭제하는 방법에 대해서 살펴봤다. 브랜치를 관리하는 데 필요한 다른 명령도 살펴보자.
`git branch` 명령은 단순히 브랜치를 만들고 삭제하는 것이 아니다. 아무런 옵션 없이 실행하면 브랜치의 목록을 보여준다.
```bash
git branch
iss53
* master
testing
```
`*`기호가 붙어있는 `master` 브랜치는 현재 Checkout 해서 작업하는 브랜치를 나타낸다.
즉, 지금 수정한 내용을 커밋하면 `master`브랜치에 커밋되고 포인터가 앞으로 한 단계 나아간다.
`git branch -v` 명령을 실행하면 브랜치마다 마지막 커밋 메시지도 함께 보여준다.
각 브랜치가 지금 어떤 상태인지 확인하기에 좋은 옵션도 있다. 현재 Checkout 한 브랜치를 기준으로 `--merged`와 `--no-merged`옵션을 사용하여 Merge 된 브랜치인지 그렇지 않은지 필터링해 볼 수 있다. `git branch --merged` 명령으로 이미 Merge 한 브랜치 목록을 확인한다.
```bash
git branch --merged
iss53
* master
```
`iss53`브랜치는 앞에서 이미 Merge 했기 때문에 목록에 나타난다. `*`기호가 붙어 있지 않은 브랜치는 `git branch -d`명령으로 삭제해도 되는 브랜치다. 이미 다른 브랜치와 Merge 했기 때문에 삭제해도 정보를 잃지 않는다.

반대로 현재 Checkout 한 브랜치에 Merge하지 않은 브랜치를 살펴보려면 `git branch --no-merged`명령을 사용한다.
```bash
git branch --no-merged
testing
```
위에는 없었던 다른 브랜치가 보인다. 아직 Merge 하지 않은 커밋을 담고 있기 때문에 `git branch -d`명령으로는 삭제되지 않는다.
```bash
git branch -d testing
error: The branch 'testing' is not fully merged.
If you are sure you want to delete it, run 'git branch -D testing'
```
Merge 하지 않은 브랜치를 강제로 삭제하려면 `-D` 옵션으로 삭제한다.

### 브랜치 워크플로
브랜치를 만들고 Merge 하는 것을 어디에 써먹어야 할까. 이 절에서는 Git 브랜치가 유용한 몇 가지 워크플로를 살펴본다.

### Long-Running 브랜치
Git은 꼼꼼하게 3-way Merge를 사용하기 때문에 장기간에 걸쳐서 한 브랜치를 다른 브랜치와 여러 번 Merge 하는 것이 쉬운 편이다. 그래서 개발 과정에서 필요한 용도에 따라 브랜치를 만들어 두고 계속 사용할 수 있다. 그리고 정기적으로 브랜치를 다른 브랜치로 Merge 한다.

이런 접근법에 따라서 Git 개발자가 많이 선호하는 워크플로가 하나 있다. 배포했거나 배포할 코드만 `master` 브랜치에 Merge해서 안정 버전의 코드만 `master` 브랜치에 둔다. 개발을 진행하고 안정화하는 브랜치는 `develop`이나 `next`라는 이름으로 추가로 만ㄷ르어 사용한다. 이 브랜치는 언젠가 안정 상태가 되겠지만, 항상 안정 상태를 유지해야하는 것이 아니다. 테스트를 거쳐서 안정적이라고 판단되면 `master` 브랜치에 Merge한다. 토픽 브랜치(앞서 살펴본 `iss53` 브랜치 같은 짧은 호흡 브랜치)에도 적용할 수 있는데, 해당 토픽을 처리하고 테스트해서 버그도 없고 안정적이면 그때 Merge 한다.

사실 우리가 얘기하는 것은 커밋을 가리키는 포인터에 대한 얘기다. 커밋 포인터를 만들고 수정하고 분리하고 합치는지에 대한 것이다. 개발 브랜치는 공격적으로 히스토리를 만들어 나아가고 안정 브랜치는 이미 만든 히스토리를 뒤따르며 나아간다.

코드를 여러 단계로 나누어 안정성을 높여가며 운영할 수 있다. 프로젝트 규모가 크면 `proposed` 혹은 `pu`(proposed updates)라는 이름의 브랜치를 만들고 `next`나 `master` 브랜치에 아직 Merge 할 준비가 되지 않은 것을 일단 Merge시킨다. 중요한 개념은 브랜치를 이용해 여러 단계에 걸쳐서 안정화해 나아가면서 충분히 안정화가 됐을 때 안정 브랜치로 Merge 한다는 점이다. 다시 말해서 Long-Running의 브랜치가 여러 개일 필요는 없지만 정말 유용하다는 점이다. 특히 규모가 크고 복잡한 프로젝트일수록 그 유용성이 빛난다.
### 토픽 브랜치
토픽 브랜치는 프로젝트 크기에 상관없이 유용하다. 토픽 브랜치는 어떤 한 가지 주제나 작업을 위해 만든 짧은 호흡의 브랜치다. 앞서 사용한 `iss53` 이나 `hotfix` 브랜치가 토픽 브랜치다. 우리는 브랜치를 새로 만들고 어느 정도 커밋하고 나서 다시 `master` 브랜치에 Merge하고 브랜치 삭제도 해 보았다. 보통 주제별로 브랜치를 만들고 각각은 독립돼 있기 때문에 매우 쉽게 컨텍스트 사이를 옮겨 다닐 수 있다. 묶음별로 나눠서 일하면 내용별로 검토하기에도, 테스트하기에도 더 편하다. 각 작업을 하루든 한 달이든 유지하다가 `master`브랜치에 Merge 할 시점이 되면 순서에 관계없이 그때 Merge 하면 된다.
### 리모트 브랜치
리모트 Refs는 리모트 저장소에 있는 포인터인 레퍼런스다. 리모트 저장소에 있는 브랜치, 태그, 등등을 의미한다. `git ls-remote [remote]` 명령으로 모든 Refs를 조회할 수 있다. `git remote show [remote]` 명령은 모든 리모트 브랜치와 그 정보를 보여준다. 리모트 Refs가 있지만 보통은 리모트 트래킹 브랜치를 사용한다.

리모트 트래킹 브랜치는 리모트 브랜치를 추적하는 레퍼런스이며 브랜치다. 리모트 트래킹 브랜치는 로컬에 있지만 임의로 움직일 수 없다. 리모트 서버에 연결할 때마다 리모트의 브랜치 업데이트 내용에 따라서 자동으로 갱신될 뿐이다.
리모트 트래킹 브랜치는 일종의 북마크라고 할 수 있다. 리모트 저장소에 마지막으로 연결했던 순간에 브랜치가 무슨 커밋을 가리키고 있었는지를 나타낸다.

### Push 하기
로컬의 브랜치를 서버로 전송하려면 쓰기 권한이 있는 리모트 저장소에 Push 해야 한다. 로컬 저장소의 브랜치는 자동으로 리모트 저장소로 전송되지 않는다. 명시적으로 브랜치를 Push 해야 정보가 전송된다. 따라서 리모트 저장소에 전송하지 않고 로컬 브랜치에만 두는 비공개 브랜치를 만들 수 있다. 또 다른 사람과 협업하기 위해 토픽 브랜치만 전송할 수도 있다.
### 브랜치 추적
리모트 트래킹 브랜치를 로컬 브랜치로 Checkout하면 자동으로 트래킹 브랜치가 만들어진다(트래킹하는 대상 브랜치를 Upstream 브랜치라고 부른다.). 트래킹 브랜치는 리모트 브랜치와 직접적인 연결고리가 있는 로컬 브랜치이다. 트래킹 브랜치에서 `git pull`명령을 내리면 리모트 저장소로부터 데이터를 내려받아 연결된 리모트 브랜치와 자동으로 Merge한다.

서버로부터 저장소를 Clone을 하면 Git은 자동으로 `master` 브랜치를 `origin/master` 브랜치의 트래킹 브랜치로 만든다. 트래킹 브랜치를 직접 만들 수 있는데 리모트를 `origin`이 아닌 다른 리모트로 할 수도 있고, 브랜치도 `master`가 아닌 다른 브랜치로 추적하게 할 수 있다.
### Pull 하기
`git fetch` 명령을 실행하면 서버에는 존재하지만, 로컬에는 아직 없는 데이터를 받아와서 저장한다. 이 때 워킹 디렉토리의 파일 내용은 변경되지 않고 그대로 남는다. 서버로부터 데이터를 가져와서 저장해두고 사용자가 Merge하도록 준비만 해둔다. 간단히 말하면 `git pull` 명령은 대부분 `git fetch`명령을 실행하고 나서 자동으로 `git merge`명령을 수행하는 것 뿐이다. 바로 앞 절에서 살펴본 대로 `clone`이나 `checkout` 명령을 실행하여 추적 브랜치가 설정되면 `git pull` 명령은 서버로부터 데이터를 가져와서 현재 로컬 브랜치와 서버의 추적 브랜치를 Merge한다.
일반적으로 `fetch` 와 `merge` 명령을 명시적으로 사용하는 것이 `pull` 명령으로 한번에 두 작업을 하는 것보다 낫다
### 리모트 브랜치 삭제
동료와 협업하기 위해 리모트 브랜치를 만들었다가 작업을 마치고 `master` 브랜치로 Merge했다. 협업하는 데 사용했던 그 리모트 브랜치는 이제 더 이상 필요하지 않기에 삭제할 수 있다. `git push` 명령에 `--delete` 명령을 사용하여 리모트 브랜치를 삭제할 수 있다.
```bash
git push origin --delete serverfix
```
위 명령을 실행하면 서버에서 브랜치 (즉 커밋을 가리키는 포인터) 하나가 사라진다. 서버에서 가비지 컬렉터가 동작하지 않는 한 데이터는 사라지지 않기 때문에 종종 의도치 않게 삭제한 경우에도 커밋한 데이터를 살릴 수 있다.
### Rebase 하기
Git에서 한 브랜치에서 다른 브랜치로 합치는 방법으로는 두 가지가 있다. 하나는 Merge 이고 다른 하나는 Rebase다.
### Rebase 의 기초
```bash
git checkout experiment
git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```
일단 두 브랜치가 나뉘기 전인 공통 커밋으로 이동하고 나서 그 커밋부터 지금 Checkout 한 브랜치가 가리키는 커밋까지 diff를 차례로 만들어 어딘가에 임시 저장해 놓는다. Rebase 할 브랜치(역주 -experiment)가 합칠 브랜치(역주 -master)가 가리키는 커밋을 가리키게 하고 아까 저장해 놓았던 변경사항을 차례대로 적용한다.
그리고나서 `master` 브랜치를 Fast-forward 시킨다.
```bash
git checkout master
git merge experiment
```
Merge 든 Rebase 든 둘 다 합치는 관점에서는 서로 다를 게 없다. 하지만, Rebase가 좀 더 깨끗한 히스토리를 만든다. Rebase 한 브랜치의 Log를 살펴보면 히스토리가 선형이다. 일을 병렬로 동시에 진행해도 Rebase 하고 나면 모든 작업이 차례대로 수행된것처럼 보인다.

Rebase는 보통 리모트 브랜치에 커밋을 깔끔하게 적용하고 싶을 때 사용한다. 아마 이렇게 Rebase 하는 리모트 브랜치는 직접 관리하는 것이 아니라 그냥 참여하는 브랜치일 것이다. 메인 프로젝트에 Patch를 보낼 준비가 되면 하는 것이 Rebase 니까 브랜치에서 하던 일을 완전히 마치고 `origin/master`로 Rebase 한다. 이렇게 Rebase 하고 나면 프로젝트 관리자는 어떠한 통합작업도 필요 없다. 그냥 master 브랜치를 Fast-forward 시키면 된다.

Rebase를 하든지, Merge를 하든지 최종 결과물은 같고 커밋 히스토리만 다르다는 것이 중요하다.
Rebase의 경우는 브랜치의 변경사항을 순서대로 다른 브랜치에 적용하면서 합치고 Merge의 경우는 두 브랜치의 최종 결과만을 가지고 합친다.

### Rebase의 위험성
Rebase가 장점이 많은 기능이지만 단점이 없는 것은 아니니 조심해야 한다. 그 주의사항은 아래 한 문장으로 표현할 수 있다.
**이미 공개 저장소에 Push 한 커밋을 Rebase 하지 마라**
이 지침만 지키면 Rebase를 하는 데 문제 될 게 없다.

Rebase는 기존의 커밋을 그대로 사용하는 것이 아니라 내용은 같지만 다른 커밋을 새로 만든다. 새 커밋을 서버에 Push하고 동료 중 누군가가 그 커밋을 Pull 해서 작업을 한다고 할 때, 그 커밋을 `git rebase`로 바꿔서 Push해버리면 동료가 다시 Push 했을 때 동료는 다시 Merge 해야 한다. 그리고 동료가 다시 Merge 한 내용을 Pull 하면 내 코드는 정말 엉망이 된다.
### Rebase 한 것을 다시 Rebase 하기
어떤 팀원이 강제로 내가 한일을 덮어썼을 때, 내가 했던 일이 무엇이고 덮어쓴 내용이 무엇인지 알아내야 한다.

커밋 SHA 체크섬 외에도 Git은 커밋에 Patch 할 내용으로 SHA-1 체크섬을 한번 더 구한다. 이 값은 patch-id라고 한다.

덮어쓴 커밋을 받아서 그 커밋을 기준으로 Rebase 할 때 Git은 원래 누가 작성한 코드인지 잘 찾아낸다.
그래서 Patch가 원래대로 잘 적용된다.

Push 하기 전에 정리하려고 Rebase 하는 것은 괜찮다.
