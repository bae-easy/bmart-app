# 🌳 Git 브랜치 전략 구조
`main`: 라이브 배포 브랜치 (PR - 최소 1명 이상 승인 필요)

`develop`: 개발 통합 브랜치 (PR - 승인 없이 머지 가능)

`feat/<기능명>`: 기능 개발 브랜치

---

# 🚀 PR 작업 흐름 정리

feat ➔ develop ➔ main 브랜치 배포 흐름 정리 내용입니다.

## 1. 초기 프로젝트 세팅 & 브랜치 생성

```
# 1. 원격 저장소 클론 (main 브랜치는 자동 생성 및 추적 연결됨)
git clone <원격 저장소 URL>

# 2. 원격 최신 이력 확인
git fetch origin

# 3. develop 브랜치 생성 및 원격 추적 연결
git switch -c develop --track origin/develop

# 4. 기능 개발 브랜치 생성 (주의: 반드시 기준이 될 브랜치(develop)에서 생성)
git switch develop
git pull origin develop
git switch -c feat/<기능명>
```

> 📌 **주의사항**
브랜치를 생성할 때는 **현재 작업 중인 HEAD(현재 브랜치)의 커밋 위치**를 기준으로 새 브랜치가 생성됩니다. 따라서 기능 브랜치를 만들 때는 반드시 develop 위치에서 최신 코드를 받고 생성해야 합니다.


## 2. 작업 내용 커밋 & PR (feat ➔ develop)

`main`과 `develop`은 Ruleset 설정으로 **직접 push가 막혀 있어**, 작성한 코드 반영을 위해서는 반드시 별도의 기능 브랜치(`feat/<기능명>`)에서 **PR을 제출**해야 합니다.

⚠️ **현재 위치한 브랜치가** `feat/<기능명>`**인지 먼저 확인하신 후 작업을 진행해 주세요.**

### ① 로컬 작업 및 원격 푸시
```
git add .
git commit -m "feat: 기능 설명"

# 최초 푸시 시 -u 옵션으로 원격 브랜치 연결
git push -u origin feat/<기능명>
```

> 이미 진행한 커밋을 취소하고 git add 전(커밋 전) 상태로 되돌리려면 ``git reset HEAD~1``로 원복이 가능합니다. (로컬 내용 삭제 없음)

> 원격 저장소의 브랜치, PR 요청 응답에 변경사항이 일어나도 히스토리가 맞지 않아 push가 이뤄지지 않을 수 있습니다. `git pull origin feat/<기능명>`으로 원격의 최신 내용을 가져와서 합친 후 push 진행이 필요합니다. (히스토리 해결방법 md파일 참고)

### ② GitHub PR (Pull Request) 진행

1. GitHub 저장소에서 **[Compare & pull request]** 클릭

2. ⚠️ **브랜치 방향 확인 (필수)**: `base: develop` ← `compare: feat/<기능명>`

3. PR 제목 및 내용 작성 후 **[Create pull request]**

4. **[Merge pull request] → [Confirm merge]** 클릭하여 머지 완료

#### 🗑️ 실수로 올린 PR을 취소/닫고 싶을때

1. **PR을 취소하고 닫고 싶을 때 (가장 일반적)**
하단 댓글 입력창 근처에 있는 Close pull request 버튼을 클릭하면 해당 PR이 머지되지 않고 닫힙니다.
이 경우, 원격과 로컬의 히스토리가 맞지 않아 

2. **아직 수정 중이라 검토를 잠시 중단하고 싶을 때**
우측 사이드바 또는 하단 관리 영역에서 Convert to draft를 클릭하면 임시 저장(Draft) 상태로 변경되어 팀원들의 머지를 막을 수 있습니다.

3. **코드만 수정해서 PR을 유지하고 싶을 때**
PR 화면에서 별도의 버튼을 누를 필요 없이, 로컬에서 해당 브랜치에 코드를 수정 후 다시 git push하면 기존 PR에 최신 내역이 자동으로 반영됩니다.

### ③ 작업 완료 브랜치 삭제
```
# 1. GitHub 상에서 [Delete branch] 버튼 클릭
# 2. 로컬 브랜치 삭제 (삭제하려는 브랜치가 아닌 다른 브랜치로 이동 후 삭제)
git switch develop
git branch -d feat/<기능명>

# 삭제 확인
git branch
```

---

# 🔄 브랜치 동기화 및 릴리즈 절차

## 개발 시 상시 동기화 필수 (develop 최신화)
개발 작업을 진행하는 동안 `develop` 브랜치의 변경 사항을 주기적으로 반영합니다.

```
git switch develop
git pull origin develop
```

## main 브랜치 업데이트 (release)
`develop`의 변경 사항을 `main`으로 반영하고 로컬을 최신화합니다.

> 참고: `develop` 브랜치에서 충분히 검증된 기능에 한해 `main` 브랜치에 병합하여 운영 환경에 배포합니다.

1. ~~**GitHub PR 진행**: `base: main` ← `compare: develop`~~

2. ~~**Ruleset 검증**: 최소 1명 이상의 리뷰어 승인 후 머지 완료~~

3. **로컬 `main` 동기화 및 확인**:
```
git switch main
git pull origin main

# 커밋 히스토리 및 브랜치 그래프 최종 확인
git log --oneline --graph --all
```

<fieldset>
<legend>📌 요약 내용</legend>

- **기능 개발 (**`feat`**):** develop 브랜치에서 생성 후 작업, PR을 통해 승인 없이 develop에 머지 및 로컬 브랜치 삭제

- **개발 통합 (**`develop`**):** 작업 중 수시로 git pull origin develop을 실행하여 최신 변경 사항 동기화

- **라이브 배포 (**`main`**):** develop ➔ main PR 생성 후 Ruleset 정책에 따라 최소 1명 이상 승인을 받아 머지 완료

</fieldset>