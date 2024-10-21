# Git
## 1. Git
    버전 관리를 할 수 있는 소프트웨어

|  | 중앙 집중식 | 분산식 |
| --- | --- | --- |
| 특징 | 버전이 중앙 서버에 저장 <br> 중앙 서버에서 파일을 가져와 다시 중앙에 업로드  | 버전을 여러 개 복제된 저장소에 저장 및 관리 <br> 개발자들 간의 작업 충돌을 줄임, 생산성 향상 <br> 백업과 복구가 용이 <br> 인터넷이 연결되지 않아도 작업 가능 |

### Working Directory
    실제 작업 중인 파일들의 위치

### Staging area
    Working directory에서 변경된 파일 중 다음 버전에 포함시킬 파일들을 선택적으로 추가하거나 제외

### Repository
    버전 이력과 파일이 영구적으로 저장

    Commit(버전) = 변경된 파일들을 저장하는 행위, snapshot이라고도 함

| 명령어 | 기능 | 주의사항 |
| --- | --- | --- |
| git init | 현재 디렉토리를 working directory로 만드는 것 <br> 로컬 저장소 설정(초기화) | git 로컬 저장소 내에 또 다른 git 로컬 저장소를 만들지 말기 <br> 우리가 직접 관리하지 않는 폴더에 git init 하지 않기 |
| git add | 변경 사항이 있는 파일을 staging area에 추가 <br> git add . → 현재 변경사항을 모두 staging area로 |  |
| git commit | staging area에 있는 파일들을 저장소에 기록 <br> 해당 시점의 버전 생성 후 변경 이력 남기기 |  |
| git status | git 현재 상태를 알 수 있음 <br> - 잘못된 부분, 해결방법까지 알려줌 <br> - 습관적으로 쳐서 확인 필요 |  |
| git commit —amend | 1. 직전 commit 메시지 수정 <br> 2. commit 전체 수정(이전 commit과 staging area에 있는 것과 함께 수정) |  |

    문법들은 외우지 말고 내 상태가 어떤지 파악한 후에 GPT를 통해 명령어를 물어보기

## 2. Remote Repository
| 명령어 | 기능 |
| --- | --- |
| git remote add origin remote_repo_url | 깃을 원격 저장소에 추가 (origin이라는 별칭, 여러 개 가능) 주소 <br> → 별칭을 사용해 로컬 저장소 한 개에 여러 원격 저장소를 추가 가능 |
| git remote -v | remote 저장소 확인 |
| git remote rm origin remote_repo_url | 원격 저장소 삭제 |

<br>

| 명령어 | 기능 |
| --- | --- |
| push | 로컬에서 remote로 보내는 거 |
| pull | remote에서 로컬로 가져오는 거, 로컬과 remote가 연결되어 있을 때 동기화 하는 거 |
| clone | 로컬이 없을 때 다운로드하는 거 |

 <br> 

| 명령어 | 기능 |
| --- | --- |
| git push origin master | remote에 commit목록을 업로드 <br> working directory에 있는 repository 히스토리를 올리는 것 (클라우드 개념 X) |
| git pull origin master | remote의 변경사항(버전 히스토리)만을 받아옴 |
| git clone remote_repo_url | remote 전체를 복제(다운로드), remote repository는 이미 git init이 되어 있음수정 후에 push를 하면 정상 작동 |
| git push -u origin master | origin을 생략하고도 origin remote에 push하는 것 <br> upstream = origin 상류에서 가져온다는 개념 <br> 이후에는 git push / git pull만 작성해도 작동함 |

| 명령어 | 기능 |
| --- | --- |
| .gitignore | staging area에서 무시하고 commit할 대상들을 해당 파일에 추가하여 commit을 하지 않는 것 <br> touch .gitignore로 파일 생성 → .gitignore 문서 사이트에서 코드 받아서 복붙 → 맨 아래 쪽에 무시할 파일명을 텍스트로 작성하면 됨 |
| .gitkeep | 빈 폴더도 staging area로 올릴 수 있게 해주는 파일 |

### 문서화
- 폴더마다 READ.ME를 가질 수 있음
- 최상위 폴더에서 하이퍼링크를 통해 하위 폴더의 READ.ME로 이동할 수 있게 할 수 있음
- 공유할 목적이므로 Userflow를 잘 설계하여 보기 쉽게 만들기

## 3. Branch
- 장점
    1. 독립된 개발 환경을 형성 → 원본(master)에 대해 안전
    2. 하나의 작업은 하나의 브랜치로 나눠서 진행 → 체계적으로 협업과 개발 가능

| 명령어 | 기능 |
| --- | --- |
| git branch | 브랜치 목록 확인 |
| git branch -r | 원격 레포지토리의 브랜치 목록 확인 |
| git branch <브랜치 이름> | 새로운 브랜치 생성 |
| git branch -d <브랜치 이름> | 브랜치 삭제(병합된 브랜치만 삭제 가능) |
| git branch -D <브랜치 이름> | 브랜치 삭제(강제 삭제) |

### HEAD

- 현재 브랜치나 commit을 가리키는 포인터(현재 내가 바라보는 위치)
    - 다른 시점의 커밋을 보고 싶다면 HEAD만 이동하면 됨
    - HEAD가 가리키는 커밋이 그 브랜치의 가장 최신 커밋

| 명령어 | 기능 |
| --- | --- |
| git switch <다른 브랜치 이름> | 다른 브랜치로 전환 |
| git switch -c <다른 브랜치 이름> | 새 브랜치 생성 후 전환 |
| git switch -c <다른 브랜치 이름> <commit ID> | 특정 커밋에서 새 브랜치 생성 후 전환 |
- git switch 하기 전 working directory에 올리고 하기

### git log

| 명령어 | 기능 |
| --- | --- |
| git log --oneline | 커밋들을 확인 |
| git log --oneline --all | 모든 브랜치의 커밋들을 모두 확인 |
| git log --oneline --graph --all | 모든 브랜치의 커밋들을 그래프처럼 확인 |

## 4. Merge

1. 수신 브랜치(병합 브랜치를 가져오고자 하는 브랜치) 확인
    - git branch 명령어 →  HEAD가 올바른 수신 브랜치를 가리키는지 확인
    - 병합 진행 위치는 반드시 **수신 브랜치에서 진행**
2. 최신 커밋 상태 확인하기
    - 수신 브랜치와 병합 브랜치 모두 최신 상태인지 확인

| 명령어 | 기능 |
| --- | --- |
| git merge <브랜치 이름> | Fast-Forward Merge |

---

### Fast-Forward Merge

- 조상 브랜치가 같은지 확인
- 브랜치를 실제로 병합 X → 브랜치 상태를 대상 브랜치 상태로 이동(빨리 감기)
    - Merge 과정 없이 브랜치 포인터가 앞으로 이동
- branch 생성 → 수정 후 커밋 → merge → 생성한 branch 삭제

### 3-Way Merge

1. 동일한 곳을 수정하지 않았을 때(각자 다른 파일을 만들었을 때)
- 조상 브랜치의 모습 + master의 모습 + 병합 브랜치의 모습을 합쳐서 새로운 commit을 생성하고 수신 브랜치가 그 commit을 바라보도록 함
2. 동일한 곳을 수정했을 때
- 어떻게 합칠 건지 결정한 후 commit 생성 후 수신 브랜치가 그 commit을 바라보도록 함

## 5. 협업 방식

### 1. Shared Repository

- 공통된 원격 저장소에 팀원들을 초대 → 각자 브랜치에서 만들고 Pull Request → Merge

### 2. Fork & Pull

- Fork
    - 원본 저장소와 연결을 유지하면서 독립적인 복사본 생성
    - 원본 저장소에 영향을 주지 않으면서 개발 가능
    - 작업 흐름
        1. Fork 한 저장소(내 저장소)를 로컬에 clone
        2. 브랜치 생성 후 변경 사항 작업
        3. 변경 사항을 fork한 저장소에 push
        4. 원본 저장소로 Pull Request 생성하여 변경 사항 제안