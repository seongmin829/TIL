# day2

* header

```python
import requests
import json
import urllib
from bs4 import BeautifulS```
oup
```

## 웹에서 url 받아오기(네이버)

요청 url 기본 틀은 query뒤에 키워드를 입력하는 것이다.

ex)  네이버에 요청할 경우 : 'https://search.naver.com/search.naver?query= ' + '키워드'

ex) 구글에 요청할 경우 : 'https://www.google.com/search?q=' + '키워드'

* 방법1

```python
# 부산광역시 날씨 요청
# https://search.naver.com/search.naver?query=부산광역시
# 날씨 받아오는 방법 1
url = 'https://search.naver.com/search.naver?query=%EB%B6%80%EC%82%B0%EA%B4%91%EC%97%AD%EC%8B%9C%EB%82%A0%EC%94%A8'
res = requests.get(url)
html = res.text
print(html)
```

* 방법2

```python
# 날씨 받아오는 방법2
url = 'https://search.naver.com/search.naver?query=%EB%B6%80%EC%82%B0%EA%B4%91%EC%97%AD%EC%8B%9C%EB%82%A0%EC%94%A8'
html = urllib.request.urlopen(url).read()
print(html)
```

* 방법3

```python
# beautifulsoup을 이용하면 한글도 자동으로 encoding 된다.
# 날씨 받아오는 방법3
bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
bs = BeautifulSoup(requests.get(url).text, 'html.parser')

print(bs.html)
```

---

---

## 원하는 정보 받아오기

```python
# naver에 url 요청
city = '부산광역시'
url = 'https://search.naver.com/search.naver?query='
url = url + urllib.parse.quote_plus(city + '날씨')
print(url)
```

* BeautifulSoup Api 에서 제공하는 기능으로 받아오기

```python
bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')

# BeautifulSoup에서 제공하는 원하는 특정 tag 가져오는 기능 -> API 기능이다 -> html 표준 방식(형식)이 아니다.
# finaAll(tag명, class명)
temp = bs.findAll("span", "todaytemp")
desc = bs.findAll("p", "cast_txt")

print(temp)
# temp는 list형식이다.
print(temp[0])
print(desc)

# 첫번쨰 반환된 객체의 text 불러오기(여기서는 온도 정보이다.)
print(temp[0].text)
print(desc[0].text)
```

```
[<span class="todaytemp">2</span>, <span class="todaytemp">-5</span>, <span class="todaytemp">7</span>, <span class="todaytemp">1</span>, <span class="todaytemp">9</span>]
<span class="todaytemp">2</span>
[<p class="cast_txt">맑음, 어제보다 4˚ 낮아요</p>, <p class="cast_txt">맑음</p>, <p class="cast_txt">구름많음</p>, <p class="cast_txt">구름많음</p>, <p class="cast_txt">맑음</p>]
2
맑음, 어제보다 4˚ 낮아요
```

* 표준 방식으로 받아오기

```python
# 표준방식
# select(tag명.class명)
temp = bs.select('span.todaytemp')
print(temp[0].text)
desc = bs.select('p.cast_txt')
print(desc[0].text)
```

```
2
맑음, 어제보다 4˚ 낮아요
```

---

---

* 웹상에서 원하는 정보에 대한 tag 및 class 정보 찾기

![원하는 정보 위치](images/원하는 정보 위치.png)

원하는 위치를 드래그 한다 -> 오른쪽 마우스 클릭 후 검사를 누른다.

![select 정보](images/select 정보.png)

원하는 태그를 클릭 후 오른쪽 클릭 한다. ->  Copy selector를 누른다. -> 메모장 등에 붙여넣기를 한다.

tag에 대한 절대 경로(full 경로) 나온다.

```
# select 문법이다. 절대 경로로 full 경로가 표현된다.
#main_pack > div.sc.cs_weather._weather > div:nth-child(2) > div.weather_box > div.weather_area._mainArea > div.today_area._mainTabContent > div.main_info > div > p > span.todaytemp
```

---

---

* 함수와 하기(좋지 않은 return 형식)

```python
# 위의 작업들 함수와 하기
# city(도시 이름) 넘겨주면 그 도시 날씨 정보 출력
def getWeather(city):
    #city = '부산광역시'
    url = 'https://search.naver.com/search.naver?query='
    # naver에 url 요청
    url = url + urllib.parse.quote_plus(city + '날씨')
    print(url)
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    temp = bs.select('span.todaytemp')
    desc = bs.select('p.cast_txt')
    # 좋지 않은 return 형식이다 / return 후에 정보를 가공하기 불편해진다.
    return temp[0].text + "˚/" + desc[0].text

print(getWeather("제주도"))
print(getWeather("부산"))
```

```
https://search.naver.com/search.naver?query=%EC%A0%9C%EC%A3%BC%EB%8F%84%EB%82%A0%EC%94%A8
1˚/흐림, 어제보다 4˚ 낮아요
https://search.naver.com/search.naver?query=%EB%B6%80%EC%82%B0%EB%82%A0%EC%94%A8
2˚/맑음, 어제보다 6˚ 낮아요
```



* 함수화 하기(개선된 return 형식)

```python
# return 형식을 dictionary로 바꿔준다. / dictionary 형식이라 return 후에 json 으로도 이용할 수 있다.
def getWeather(city):
    #city = '부산광역시'
    url = 'https://search.naver.com/search.naver?query='
    # naver에 url 요청
    url = url + urllib.parse.quote_plus(city + '날씨')
    print(url)
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    temp = bs.select('span.todaytemp')
    desc = bs.select('p.cast_txt')
    return {"temp" : temp[0].text, "desc" : desc[0].text}

print(getWeather("제주도"))
print(getWeather("부산"))
```

```
https://search.naver.com/search.naver?query=%EC%A0%9C%EC%A3%BC%EB%8F%84%EB%82%A0%EC%94%A8
{'temp': '0', 'desc': '흐림, 어제보다 4˚ 낮아요'}
https://search.naver.com/search.naver?query=%EB%B6%80%EC%82%B0%EB%82%A0%EC%94%A8
{'temp': '2', 'desc': '맑음, 어제보다 6˚ 낮아요'}
```



## 날씨 정보 출력하기

* header

```python
import requests
import json
import urllib
from bs4 import BeautifulSoup
```

* function(google - Dialogflow(day1).md 에 작성된 함수이다.)

```python
def get_answer(text, sessionId):
    data_send = {
        'query': text, 'sessionId': sessionId,
        'lang': 'ko', 'timezone' : 'Asia/Seoul'
    }
    data_header = {
        'Authorization': 'Bearer ab14c5016a0c40acb627887b88f002db',
        'Content-Type': 'application/json; charset=utf-8'
    }

    dialogflow_url = 'https://api.dialogflow.com/v1/query?v=20150910'
    res = requests.post(dialogflow_url, data=json.dumps(data_send), headers=data_header)
    if res.status_code == requests.codes.ok:
       return res.json()    
    return {}
```

```python
# return 형식을 dictionary로 바꿔준다. / dictionary 형식이라 return 후에 json 으로도 이용할 수 있다.
def getWeather(city):
    #city = '부산광역시'
    url = 'https://search.naver.com/search.naver?query='
    # naver에 url 요청
    url = url + urllib.parse.quote_plus(city + '날씨')
    print(url)
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    temp = bs.select('span.todaytemp')
    desc = bs.select('p.cast_txt')
    return {"temp" : temp[0].text, "desc" : desc[0].text}
```

```python
while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']

    if dict['result']['metadata']['intentName'] == 'order2' and dict['result']['actionIncomplete'] == False:
        price = {"짜장" : 5000, "짬뽕" : 10000, "탕수육" : 20000}
        params = dict['result']['parameters']['food_number']
        
        output = [food.get("number-integer", 1)*price[food["food"]] for food in params]
        
        print(sum(output))
    elif dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
        date = dict['result']['parameters']['date']
        geo_city = dict['result']['parameters']['geo-city']
        
        info = getWeather(geo_city)
        print(f"{geo_city} 날씨 정보 : {info['temp']}˚ / {info['desc']}")
        
        #print("날씨 ", date, geo_city)
    else:
        print("Bot:" + answer)
```

```
->오늘 부산 날씨 어떄?
https://search.naver.com/search.naver?query=%EB%B6%80%EC%82%B0%EA%B4%91%EC%97%AD%EC%8B%9C%EB%82%A0%EC%94%A8
부산광역시 날씨 정보 : 3˚ / 맑음, 어제보다 6˚ 낮아요
->오늘 대구 날씨 어떄?
https://search.naver.com/search.naver?query=%EB%8C%80%EA%B5%AC%EA%B4%91%EC%97%AD%EC%8B%9C%EB%82%A0%EC%94%A8
대구광역시 날씨 정보 : 0˚ / 맑음, 어제보다 5˚ 낮아요
->오늘 제주 날씨 어떄?
https://search.naver.com/search.naver?query=%EC%A0%9C%EC%A3%BC%EC%8B%9C%EB%82%A0%EC%94%A8
제주시 날씨 정보 : 3˚ / 흐림, 어제보다 4˚ 낮아요
->오늘 제주도 날씨 어때?
Bot:어느 도시를 말씀하시는 건가요?
```

제주도는 geo-city로 인식하지 못해서 `dict['result']['parameters']['geo-city']` 안의 값이 비어 있다.

제주도도 인식 시키고 싶은면 entity를 geo-city 외에 다른 것으로 쓰는 방식을 찾아보아야 한다.



## 단어에 대한 검색 결과 가져오기

네이버 지식 백과 : "https://search.naver.com/search.naver?where=kdic&query=" + "키워드"

지식 백과로 검색 하려면 ?where=kdic 을 추가하면 된다.



첫번쨰 결과를 가져온다.( p class = "txt_box")

![word1](images/word1.png)



* 지식 백과에 갑분싸 검색 url 가져오기

```python
word = "갑분싸"
url = "https://search.naver.com/search.naver?where=kdic&query="
url = url + urllib.parse.quote_plus(word)
print(url)
```

```python
bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')

output = bs.select('p.txt_box')
# 여러개의 정의 중 첫번째 정의 출력
print(output[0].text)
```

```
‘갑자기 분위기가 싸해진다.’, ‘갑자기 분위기가 싸해지는데’ 등의 뜻을 가지고 있는 신조어이다. ‘갑자기 분위기가 싸해진다’, ‘갑자기 분위기가 싸해지는데’를 줄여 이르는 신조어이다. ‘갑분싸’는... 
```

* 함수화 하기

```python
def getQuery(word):
    #word = "갑분싸"
    url = "https://search.naver.com/search.naver?where=kdic&query="
    url = url + urllib.parse.quote_plus(word)
    print(url)
    
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    output = bs.select('p.txt_box')
    # word 에 대한 모든 정의를 return 하지 못해 좋지 못한 return 형식이다.
    #return output[0].text
	return [node.text for node in output]

# '갑분싸'에 대한 정의 list를 return 받은 후 그 중 첫번째 정의 출력
print(getQuery("갑분싸")[0])
```



## query intent

![qintent1](images/qintent1.png)

![qintent2](images/qintent2.png)

query intent 를 생성한다.  갑분싸에 대한 entity 는 sys.any로 해준다.



![qintent3](images/qintent3.png)

any 에 대해서 잘 인식하지 못하는 경우에는 training phrases에 가능한 text를 더 추가해 준다.



### 코드와 query intent 연동하기

* header

```python
import requests
import json
import urllib
from bs4 import BeautifulSoup
```

* function

```python
def getQuery(word):
    #word = "갑분싸"
    url = "https://search.naver.com/search.naver?where=kdic&query="
    url = url + urllib.parse.quote_plus(word)
    print(url)
    
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    output = bs.select('p.txt_box')

    return [node.text for node in output]
    # return output[0].txt
```

* 연동 코드

```python
while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']
    intentName = dict['result']['metadata']['intentName']

    if intentName == 'query':
        word = dict['result']['parameters']['any']
        print(getQuery(word)[0])
        
    elif dict['result']['metadata']['intentName'] == 'order2' and dict['result']['actionIncomplete'] == False:
        price = {"짜장" : 5000, "짬뽕" : 10000, "탕수육" : 20000}
        params = dict['result']['parameters']['food_number']
        
        output = [food.get("number-integer", 1)*price[food["food"]] for food in params]
        
        print(sum(output))
    elif dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
        date = dict['result']['parameters']['date']
        geo_city = dict['result']['parameters']['geo-city']
        
        info = getWeather(geo_city)
        print(f"{geo_city} 날씨 정보 : {info['temp']}˚ / {info['desc']}")
        
        #print("날씨 ", date, geo_city)
    else:
        print("Bot:" + answer)
```

```
->안녕
Bot:안녕하세요!
->갑분싸가 뭐야?
https://search.naver.com/search.naver?where=kdic&query=%EA%B0%91%EB%B6%84%EC%8B%B8
 ‘갑자기 분위기가 싸해진다.’, ‘갑자기 분위기가 싸해지는데’ 등의 뜻을 가지고 있는 신조어이다. ‘갑자기 분위기가 싸해진다’, ‘갑자기 분위기가 싸해지는데’를 줄여 이르는 신조어이다. ‘갑분싸’는... 
->python이 뭐야?
https://search.naver.com/search.naver?where=kdic&query=python%EC%9D%B4+%EB%AD%90%EC%95%BC%3F
최근 몇 년 사이 프로그래밍을 비전공자들에게 알려주는 문화가 전세계적으로 확산되고 있다. 하버드나 예일대 같은 해외 대학 뿐만 아니라 카이스트, 국민대, 성균관대 등...
->오늘 부산 날씨 어떄?
https://search.naver.com/search.naver?query=%EB%B6%80%EC%82%B0%EA%B4%91%EC%97%AD%EC%8B%9C%EB%82%A0%EC%94%A8
부산광역시 날씨 정보 : 4˚ / 맑음, 어제보다 5˚ 낮아요
```





### 텍스트 음성으로 바꾸기

* 구글의 google tts 이용하기 : text를 넘겨주면 자동으로 mp3 파일로 바꿔준다.

gtts 설치한다.

![instsallgtts](images/instsallgtts.png)

* 예제

```python
from gtts import gTTS
import IPython.display as ipd

text = "자기 분위기가 싸해진다, 자기 분위기가 싸해지는데"
# lang='ko' : 입력 text가 한국어라고 명시
# text를 google 서버에 보내고 mp3 file이 return 된다.
tts = gTTS(text=text, lang='ko')
# local에 저장된다.
tts.save("output.mp3")
```

* jupyter notebook에서 밑의 코드를 실행시키면 자동으로 재생된다.

```python
ipd.Audio('output.mp3', autoplay=True, )
```

![play](images/play.png)



* 챗봇 코드에 적용시키기

```python
def getTTS(text):
    tts = gTTS(text=text, lang='ko')
    tts.save("output.mp3")
    print(text)
    ipd.display(ipd.Audio('output.mp3', autoplay=True, ))

while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']
    intentName = dict['result']['metadata']['intentName']

    if intentName == 'query':
        word = dict['result']['parameters']['any']
        text = getQuery(word)[0]
        getTTS(text)
        
    elif dict['result']['metadata']['intentName'] == 'order2' and dict['result']['actionIncomplete'] == False:
        price = {"짜장" : 5000, "짬뽕" : 10000, "탕수육" : 20000}
        params = dict['result']['parameters']['food_number']
        
        output = [food.get("number-integer", 1)*price[food["food"]] for food in params]
        getTTS(f'{sum(output)}')
        #print(sum(output))
    elif dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
        date = dict['result']['parameters']['date']
        geo_city = dict['result']['parameters']['geo-city']
        
        info = getWeather(geo_city)
        getTTS(f"{geo_city} 날씨 정보 : {info['temp']}˚ / {info['desc']}")
        #print(f"{geo_city} 날씨 정보 : {info['temp']}˚ / {info['desc']}")
        
        #print("날씨 ", date, geo_city)
    else:
        getTTS(answer)
        #print("Bot:" + answer)
```

![result](images/result.png)



* jupyter notebook에 dialogflow 띄우기

![dialog](images/dialog.png)

Integrations 클릭 -> Web Demo 클릭 -> 코드 복사 -> jupyter notebook에 붙여넣기

```python
from IPython.core.display import HTML

HTML(
    """
    
<iframe
    allow="microphone;"
    width="350"
    height="430"
    src="https://console.dialogflow.com/api-client/demo/embedded/bb18764e-5165-44f9-9399-4dd88c781281">
</iframe>
"""
)
```

![output2](images/output2.png)





1. 지금까지는 console 환경에서 작업을 했기 때문에 현재 상태로는 다른 서버(ex) 라인) 등과 연동을 할 수 없다.
2.  console 환경이라 while 문을 써서 작업을 한 것이지 함수화 한 후에 실행 시에만 호출하면 된다.
3. 실제 연동시 매번 `dict = get_answer(txt, 'user01')` 이 작업을 하는 것이 아니라 자동으로 dict 정보를 받는다.



## Fulfillment



* flask 이용해서 서버와 연결하기
  * .py 파일은 계속해서 실행 중이기 떄문에 jupyter notebook을 이용하지 못하고 .py 파일을 만들어 주어야 한다.

![server1](images/server1-1580885687683.png)

jupyter notebook에서 text 파일 생성 후 이름을 server.py로 변경해준다.

![server2](images/server2.png)

server.py 파일이 있는곳으로 이동 후 -> server.py 를 실행 시켜준다.

![server3](images/server3.png)

3000번 port로 설정 했으니 3000번으로 접속한다.

---

---

* debug 를 True 로 설정하면 코드에서 변경한 내용이 server를 재시작 할 필요 없이 실시간으로 수정이 된다.(server.py)

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "hello^^"

if __name__=='__main__':
    # 사설망에서 매번 바뀌는 아이피를 매번 작성하는것이 불가능하기 때문에 0.0.0.0 으로 작성해주면 local ip를 자동으로 찾아서 적용시켜준다.
    # 사설망에서 로컬 주소를 127.0.0.1 로 하면 로컬 주소로 찾아준다.
    app.run(host='0.0.0.0', port=3000, debug=True)
```





### 날씨 정보 출력해보기

(server.py)

```python
from flask import Flask, request
import requests
import json
import urllib
from bs4 import BeautifulSoup

app = Flask(__name__)

def getWeather(city):
    #city = '부산광역시'
    url = 'https://search.naver.com/search.naver?query='
    # naver에 url 요청
    url = url + urllib.parse.quote_plus(city + '날씨')
    print(url)
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    temp = bs.select('span.todaytemp')
    desc = bs.select('p.cast_txt')
    return {"temp" : temp[0].text, "desc" : desc[0].text}

@app.route('/')
def home():
    name = request.args.get("name")
    return "hello~~~~~~~~~~~" + name

@app.route('/weather')
def weather():
    city = request.args.get("city")
    info = getWeather(city)
    
    return "<font color=red>" + info['temp'] + "도    " + info['desc'] + "</font>"


if __name__=='__main__':
    app.run(host='0.0.0.0', port=3000, debug=True)
```

`http://127.0.0.1/weather?city=부산` 으로 접속하면 weather 정보 빨간색으로 출력된다.

![output3](images/output3.png)

* dictionary 데이터를 문자열 데이터(json 포맷)로 바꿔서 return 하기 (json.dumps 이용)

```python
@app.route('/weather')
def weather():
    city = request.args.get("city")
    info = getWeather(city)
    
    return json.dumps(info)
```

![output4](images/output4.png)

* json.dumps() 대신 jsonify를 이용할 수도 있다.
  * heder에 `from flask import jsonify`를 추가해 주어야 한다.

(server.py)

```python
from flask import Flask, request, jsonify
import requests
import json
import urllib
from bs4 import BeautifulSoup

app = Flask(__name__)

def getWeather(city):
    #city = '부산광역시'
    url = 'https://search.naver.com/search.naver?query='
    # naver에 url 요청
    url = url + urllib.parse.quote_plus(city + '날씨')
    print(url)
    bs = BeautifulSoup(urllib.request.urlopen(url).read(), 'html.parser')
    
    temp = bs.select('span.todaytemp')
    desc = bs.select('p.cast_txt')
    return {"temp" : temp[0].text, "desc" : desc[0].text}

@app.route('/')
def home():
    name = request.args.get("name")
    return "hello~~~~~~~~~~~" + name

@app.route('/weather')
def weather():
    city = request.args.get("city")
    info = getWeather(city)
    return jsonify(info)

if __name__=='__main__':
    app.run(host='0.0.0.0', port=3000, debug=True)
```

![output5](images/output5.png)



* jupyter notebook에 위의 url 호출하면 json 데이터 그대로 받아진다.

```python
url = 'http://127.0.0.1:3000/weather?city=%EC%84%9C%EC%9A%B8'
res = requests.get(url)
html = res.text
print(html)
```

```
{
  "desc": "\ub9d1\uc74c, \uc5b4\uc81c\ubcf4\ub2e4 4\u02da \ub0ae\uc544\uc694", 
  "temp": "5"
}
```



---

---

* dialogflow와 통신하기
  * dialogflow는 POST 방식으로 user와 통신을 한다. -> 함수를 POST 로 호출할 때만 실행되게 설정해주어야 한다.(methods=['POST'])

```python
# dialogflow 의 규칙에 맞게 return 을 해야한다. 다른 함수처럼 아무것이나 return 하면 안된다.
@app.route('/dialogflow', methods=['POST'])
def dialogflow():
    # fulfillmentText : dialogflow에서 user에게 출력할 것을 입력하는 정해진 format
    res = {'fulfillmentText': 'Hello~~~'}
    return jsonify(res)
```

* 위의 방식으로 설정하면 개발단계에서 접속을 할 수가 없다.(get 방식이 아니라서)
  * 따라서 개발단계에서는 post, get 둘다 활성화를 시켜주고 나중에는 post만 활성화 시킨다.

![output6](images/output6.png)

```python
# dialogflow 의 규칙에 맞게 return 을 해야한다. 다른 함수처럼 아무것이나 return 하면 안된다.
@app.route('/dialogflow', methods=['POST', 'GET'])
def dialogflow():
    # fulfillmentText : dialogflow에서 user에게 출력할 것을 입력하는 정해진 format
    res = {'fulfillmentText': 'Hello~~~'}
    return jsonify(res)
```



### Secure Tunnels(NGROK)

* 공인 IP 아니여도 외부에서 접속 가능하게 하는 터널 프로그램
* 방화벽 내부 서버를 외부에서 접속 가능
* Mac, Linux, Windows, 모바일 지원
* 유료와 무료 버전 존재



### NGROK 사용하기

* ngrok을 설치해준다.
* 압축을 풀어주고 파일을 실행 해준다.
* 실행 파일이 있는 위치로 cd 를 이용해서 이동한다.
* `ngrok http 포트번호`를 입력한다. (진행하고 예제의 포트번호는 3000)

![ngrok4](images/ngrok4.png)



1. Fulfillment 로 가서 Webhook를 활성화 시켜준다.

2. dialogflow는 http가 아닌 https만 지원을 한다. -> ngrok는 https도 지원해 준다.

3. ngrok에서 받은 ip의 http에 s를 붙인다음  Fulfillment의 URL에 넣어준다.

4. save를 해준다.

![ngrok](images/ngrok-1580892432569.png)



5. intent (ex) query) 로 가서 Fulfillment의 Enable webhook call for this intent 를 활성화 시켜준다.

![ngrok2](images/ngrok2.png)

6. dialogflow의 Fullfillment에 ngrok에서 받은 루트 주소를 입려해 주었기 때문에 server.py의 route에 해당하는 함수를 수정해준다.(Dialogflow에서는 post로 받기 떄문에 post로 설정)

```python
@app.route('/', methods=['POST'])
def home():
    res = {'fulfillmentText': 'Hello~~~world'}
    return jsonify(res)
```

![ngrok5](images/ngrok5.png)

7. fulfillment를 설정해 둔 intent에 맞는 text를 입력했을 때 response text가 반환되지 않고 server.py 에서 return 한 json data 가 return 된다. 

8. 만약 server 가 꺼져 있는 상황이라면 dialogflow는 일정시간동안 계속 기다렸다가 intent 에 있는 response text를 출력한다.



* agent 별로 하나의 Fulfillment 주소에 연결할 수 있다. 
  * 즉 intent 별로 다른 주소에 연결하는 것이 아니라 하나의 서버에서 intent 별로 다른 작동을 하게 만들어야 한다.

