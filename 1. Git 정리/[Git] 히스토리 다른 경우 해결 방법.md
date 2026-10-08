# 로컬이 원격보다 뒤쳐진 경우
```
git push -u origin <로컬 브랜치>
To https://github.com/원격_저장소_경로.git
 ! [rejected]        <로컬 브랜치> -> <원격 저장소 브랜치>(non-fast-forward)
error: failed to push some refs to 'https://github.com/원격_저장소_경로.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. If you want to integrate the remote changes,
hint: use 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

원격 저장소에 이미 올라가 있는 최신 커밋이 내 로컬 컴퓨터의 브랜치에는 없어서 원격보다 내 로컬이 뒤처져 있기 때문(behind)에 발생한 rejection 에러입니다.

## 💡 왜 발생했을까?
GitHub PR 화면에서 PR을 닫거나(Closed) / Draft 상태 변경 등의 작업을 하셨거나, 원격 브랜치에 이미 다른 커밋이 추가되어 있어서 원격과 로컬의 이력이 달라졌기 때문입니다.

## 🛠️ 해결 방법
상황에 맞게 두 가지 방법 중 하나를 선택하시면 됩니다.

### 방법 1. 원격의 최신 내역을 가져와서 합친 후 다시 Push (안전한 방법)
```
# 1. 원격 저장소의 최신 내역 당겨와서 병합
git pull origin docs/git

# 2. 다시 push 진행
git push -u origin docs/git
```

### 방법 2. 로컬 코드 상태로 원격 브랜치를 강제 덮어쓰기 (원격 내역 무시)
만약 로컬에 작성된 내용이 최신이고, 원격의 변경 사항을 무시하고 그대로 덮어씌워도 상관없는 상황이라면 강제 푸시를 진행합니다.

```
git push -u origin docs/git --force
```