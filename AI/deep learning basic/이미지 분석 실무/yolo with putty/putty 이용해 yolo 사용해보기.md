# putty 이용해 yolo 사용해보기

* 멀티캠퍼스_이미지_분석_AI_서비스_개발_실무_과정_실습장비_매뉴얼(B) (1)zip 파일에 putty 인증키 있다.
* dark.zip 파일에 dog.jpg, coco.names, yolov3.cfg 파일 있다.

---

---

git clone https://github.com/pjreddie/darknet.git

vi Makefile

CUDA 사용 GPU=1

opencv 사용 OPENCV=1

make

wget https://pjreddie.com/media/files/yolov3.weights

./darknet detect cfg/yolov3.cfg yolov3.weights data/dog.jpg
