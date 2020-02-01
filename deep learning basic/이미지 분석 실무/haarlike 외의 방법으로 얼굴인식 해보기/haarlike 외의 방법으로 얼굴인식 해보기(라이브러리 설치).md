# haarlike 외의 방법으로 얼굴인식 해보기(라이브러리 설치)

```
# 파이썬 3.6으로 가상환경(py36) 생성
conda create -n py36 python=3.6 anaconda
# 가상환경 활성화
conda activate py36

# 라이브러리 설치
pip install opencv-python
pip install imutils

# dlib 라이브러리 직접 다운받아서 설치
https://pypi.org/simple/dlib/
# dlib-19.8.1-cp36-cp36m-win_amd64.whl 이 라이브러리 받은 폴더로 이동 후 명령어 실행
pip install dlib-19.8.1-cp36-cp36m-win_amd64.whl

pip install --no-dependencies face_recognition

pip install git+https://github.com/ageitgey/face_recognition_models
```

