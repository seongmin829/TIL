# Git

> Git은 분산버전관리시스템(DVCS)이다.
>
> 소스코드의 버전 및 이력을 관리할 수 있다.

# 준비하기

윈도우에서 git을 활용하기 위해서 [git_bash][https://gitforwindows.org]를 설치한다.

git을 활용하기 위해서 GUI 툴인 `source tree`, `github desktop` 등을 활용할 수도 있다.

초기 설치를 완료한 이후에 컴퓨터에 `author`정보를 입력한다.

이메일 정보를 github에 가입된 이메일로 일치시켜야 커밋 이력들이 관리된다.

```bash
$ git config --global user.name seongmin829
$ git config --global user.email seong_min829@naver.com
```

## 로컬 저장소(respository) 활용하기

### 1. 저장소 초기화

``` bash
$ git init
Initialized empty Git repository in C:/Users/student/Desktop/TIL/.git/
(master) $
```

* `.git`폴더가 생성되며, 여기에 git과 관련된 모든 정보가 저장된다.
* git bash에`(master)` 라고 표현되는데, 이는 `master` 브랜치에 있다는 뜻이다.

### 2. add

`working director`, 즉 작업 공간에서 변경된 사항을 이력을 저장하기 위해서는 반드시 `staging area`를 거쳐야한다.

```bash
$ git add markdwon.md # 특정 파일
$ git add images/ # 특정 폴더
$ git add . #현재 디렉토리
```

* `add` 전 상태

  ```bash
  $ git status
  On branch master
  
  No commits yet
  
  # 트랙킹 되고 있지 않는 파일들
  # => commit 이력에 한번도 담기지 않은 파일들
  Untracked files:
  # 커밋될 것들에 포함시키려면 add 명령어들 사용해
    (use "git add <file>..." to include in what will be committed)
          git.md
          images/
          makerdown.md
  # 아직 커밋될 것들은 없지만,
  # untracked files은 존재한다.
  nothing added to commit but untracked files present (use "git add" to track)
  
  ```

* `add` 후 상태

  ```bash
  $ git add images/
  $ git status
  On branch master
  
  No commits yet
  # 커밋될 변화들
  # => staging area에 있는 파일들
  Changes to be committed:
    (use "git rm --cached <file>..." to unstage)
          new file:   images/aaaa.png
  
  Untracked files:
    (use "git add <file>..." to include in what will be committed)
          git.md
          makerdown.md
  
  ```

###  3. commit

commit은 이력을 확정짓는 명령어로 , 해당 시점의 스냅샷을 기록한다.

커밋시에는 반드시 메세지를 작성 해야되며, 메세지는 변경사항을 알 수 있도록 명확하게 작성한다.

```bash
$ git commit -m '마크다운 및 git정리'
[master (root-commit) 9c54ca2] 마크다운 및 git정리
 3 files changed, 194 insertions(+)
 create mode 100644 git.md
 create mode 100644 images/aaaa.png
 create mode 100644 makerdown.md

```

커밋 이후에는 아래의 명령어를 통해 지금까지 작성된 이력을 확인하자.

```bash
$ git log
commit 9c54ca28888d259c6af629ec09b60a94ad4f9cdf (HEAD -> master)
Author: seongmin829 <seong_min829@naver.com>
Date:   Thu Dec 26 14:36:51 2019 +0900

    마크다운 및 git정리
$ git log --oneline
9c54ca2 (HEAD -> master) 마크다운 및 git정리
$ git log -1
commit 9c54ca28888d259c6af629ec09b60a94ad4f9cdf (HEAD -> master)
Author: seongmin829 <seong_min829@naver.com>
Date:   Thu Dec 26 14:36:51 2019 +0900

    마크다운 및 git정리
```

커밋은 해쉬코드를 바탕으로 구분된다.(이력마다 각각의 해쉬코드가 있다.)

## 원격 저장소( remote repository) 활용하기

원격 저장소 기능을 제공하는 다양한 서비스 중에 github을 기준으로 설명한다.

### 0. 준비사항

* Github에 repository 생성

### 1. 원격 저장소 등록

```bash
$ git remote add origin 깃허브url
# $ git remote add origin https://github.com/seongmin829/TIL.git
```

* 원격저장소(`remote`)로 `origin`이라는 이름으로 `깃허브url`을 등록(`add`)한다.

* 등록된 원격 저장소 목록을 보기 위해서는 아래의 명령어를 활용한다.

  ```bash
  $ git remote -v
  origin  https://github.com/seongmin829/TIL.git (fetch)
  origin  https://github.com/seongmin829/TIL.git (push)
  ```

### 2. `push`- 원격저장소 업로드

```bash
$ git push -u origin master
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 12 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 336.76 KiB | 22.45 MiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To https://github.com/seongmin829/TIL.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.

```

`origin`으로 설정된 원격저장소에 `master`브랜치로 업로드(`push`)

이후 변경사항이 생길 때마다, `add`-`commit`, `push`를 반복하면 된다.

그리고, 항상 모든 명령어 이후에 연관된 상태를 확인하자.

`status`, `log`, `remote -v`

### 3. pull

```bash
$ git pull origin master
```

원격 저장소의 변경 사항을 받아온다.

### 4. `clone`

```bash
$ git clone 깃허브url
```

원격 저장소를 복제 한다.

**주의! `init`명령어와 같이 기억하자!**

