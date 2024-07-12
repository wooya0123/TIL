# Git
    버전 관리를 할 수 있는 소프트웨어

|  | 중앙 집중식 | 분산식 |
| --- | --- | --- |
| 특징 | - 버전이 중앙 서버에 저장 <br> - 중앙 서버에서 파일을 가져와 다시 중앙에 업로드  | - 버전을 여러 개 복제된 저장소에 저장 및 관리 <br> - 개발자들 간의 작업 충돌을 줄임, 생산성 향상 <br> - 백업과 복구가 용이 <br> - 인터넷이 연결되지 않아도 작업 가능 |

### Working Directory
    실제 작업 중인 파일들의 위치

### Staging area
    Working directory에서 변경된 파일 중 다음 버전에 포함시킬 파일들을 선택적으로 추가하거나 제외

### Repository
    버전 이력과 파일이 영구적으로 저장

    Commit(버전) = 변경된 파일들을 저장하는 행위, snapshot이라고도 함

| 문법 | 설명 | 주의 사항 |
| --- | --- | --- |
| git init | - 현재 디렉토리를 working directory로 만드는 것 <br> - 로컬 저장소 설정(초기화) | - git 로컬 저장소 내에 또 다른 git 로컬 저장소를 만들지 말기 <br> - 우리가 직접 관리하지 않는 폴더에 git init 하지 않기 |
| git add | - 변경 사항이 있는 파일을 staging area에 추가 <br> - git add . → 현재 변경사항을 모두 staging area로 |  |
| git commit | - staging area에 있는 파일들을 저장소에 기록 <br> - 해당 시점의 버전 생성 후 변경 이력 남기기 |  |
| git status | - git 현재 상태를 알 수 있음 <br> - 잘못된 부분, 해결방법까지 알려줌 <br> - 습관적으로 쳐서 확인 필요 |  |
| git commit —amend | 1. 직전 commit 메시지 수정 <br> 2. commit 전체 수정(이전 commit과 staging area에 있는 것과 함께 수정) |  |

    문법들은 외우지 말고 내 상태가 어떤지 파악한 후에 GPT를 통해 명령어를 물어보기

## Remote Repository
| 문법 | 설명 |
| --- | --- |
| git remote add origin remote_repo_url | 깃을 원격 저장소에 추가 (origin이라는 별칭, 여러 개 가능) 주소 <br> → 별칭을 사용해 로컬 저장소 한 개에 여러 원격 저장소를 추가 가능 |
| git remote -v | remote 저장소 확인 |
| git remote rm origin remote_repo_url | 원격 저장소 삭제 |