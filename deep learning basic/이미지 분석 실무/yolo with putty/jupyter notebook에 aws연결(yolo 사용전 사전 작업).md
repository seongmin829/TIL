# jupyter notebook에 aws연결(yolo 사용전 사전 작업)

```python
jupyter notebook --generate-config
# 위 명령어 출력 결과
#/home/username/.jupyter :  jupyter_notebook_config.py

# python3 실행
python3

from notebook.auth import passwd

passwd()

# 빠져나오기
ctrl+z

'sha1:c66b7e36017f:e106beacca18646a176263a737e9e733354908a1'

# vim 에디터 실행
vim ~/.jupyter/jupyter_notebook_config.py

# vim 에디터 이용해서 아래 2줄 입력
c = get_config()
c.NotebookApp.password = u'sha1:c66b7e36017f:e106beacca18646a176263a737e9e733354908a1'

# 연결
jupyter notebook --ip 0.0.0.0 --port 8893
```

위 작업 실행 후 chrome 주소창에  받은 ip + port 입력한다.

13.124.193.28:8893