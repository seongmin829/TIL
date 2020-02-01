# push-pull 과정에서 오류 해결 방법

일반적으로, 작업이 끝나고 `push`, 작업 시작 전에 `pull`을 통해서 원격저장소 내용을 업데이트하면

문제가 발생하지 않는다.

다만, 서로 다른 이력으로 구성되는 경우 `push`를 하려고 하면 아래의 메시지가 뜬다.

```bash
$ git push origin master
To https://github.com/seongmin829/TIL.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/seongmin829/TIL.git'
# 원격 저장소와 로컬 저장소의 이력이 다르다.
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. 
# 원격 저장소의 변경 사항을 pull로 반영한 다음, 다시 push 해라.
You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

```bash
$ git pull origin master
```

* pull 하는 과정에서 Vim 편집기 창이 뜰 수 있다.

* merge commit 이 발생되는 것으로,

  * `esc` 를 누르고 `:wq` 입력을 하면 커밋을 저장 할 수 있다.
    * `w` : write(저장)
    * `q`: quit(종료)

* 혹시 merge conflict가 발생하는 경우에는 동일 파일이 수정된 경우고, 이때는 충돌을 해결한 이후(파일을 직접 열어서 충돌난 지점을 수정한다.)에 아래의 명령어를 입력하고, 위의 과정으로 저장한다.

  ```bash
  $ git commit
  ```

  