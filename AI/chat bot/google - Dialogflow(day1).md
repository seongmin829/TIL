# google - Dialogflow

## 1. agent

* agent 생성

![agent](images/agent.png)

create new agent로 생성해 준다.



## 2. intent, entity

* intent 생성

![intents2](images/intents2.png)

`+`버튼 또는 create intent로 intent를 생성한다.

---

---

### system entity

![intents3](images/intents3.png)

원하는 글자(entity)를 드래그 하면 system entity 목록이 뜬다.



![intents4](images/intents4.png)

원하는 system entity를 선택한 후 save 한다.

![intents5](images/intents5.png)

text를 입력하면 선택된 intent, entity에 대한 정보가 뜬다.( system-geo-city에는 구글에서 인식한 도시 정보가 뜬다.  구글 입장에서 도시로 인식하지 못하면 도시 정보가 뜨지 않는다.)

![intents6](images/intents6.png)

response에 `$`를 입력하면 entity가 뜬다.

입력 후 save 한 후에 text를 입력하면 entity 정보가 포함된 response가 출력된다.

---

---

* slot filling
  * 모든 경우의 text에 대한 response를 생성하지 않고 빠진 entity에 대한 처리를 할 수 있다.

![intents7](images/intents7.png)

entity를 빼고 입력한 경우 weather intent로 들어왔지만 response 가 원하는 대로 출력되지 않는다.

![intents8](images/intents8.png)

Action and parameters에서 entity를 check 해준 후에 define promts를 클릭한다.

![intents9](images/intents9.png)

entity가 빠졌을 경우 response를 입력해준다.

![intents10](images/intents10.png)

![intents11](images/intents11.png)

save 후에 실행 시켜보면 빠진 entity에 대한 작업을 처리 해준다.

---

---

### 사용자 정의 entity

![entity1](images/entity1.png)

사용자 정의 entity를 생성해준다.

![entity2](images/entity2.png)

목록 작성 후에 save로 생성해준다.

![entity3](images/entity3.png)

새로운 intent를 생성 후에 사용자 정의 entity를 적용시켜준다.

![entity4](images/entity4.png)

response text에도 사용자 정의 entity를 설정 후 text를 입력하면 사용자 정의 entity가 포함되 response가 출력된다.

![entity5](images/entity5.png)

사용자 정의에 포함 되지 않은 entity를 포함해서 text 입력시 response가 제대로 출력되지 않는다.

![entity6](images/entity6.png)

entity 설정을 다시 해준 뒤 다시 text를 입력한다.

![entity7](images/entity7.png)



---

---

### 음식 주문 intent , entity 만들어 보기

* 시나리오

```
사용자 : 메뉴판 주세요. / 음식 주문 할게요
봇 : 어떤 음식을 주문하실건가요?
	사용자 : 짜장면 주세요.
	봇 : 짜장면 주문 접수 할까요?
		사용자 : 네! / 맞아요.
		봇 : 짜장면 주문 접수가 완료되었습니다.
```

- 3개의 intent가 필요하고 3개의 intent가 계층 구조를 가져야 한다.



* 첫번째 intent : orderfood

![orderfood1](images/orderfood1.png)

* orderfood 의 종속 intent를 만들고 이름을 orderfood-menu로 바꿔준다.

![orderfood2](images/orderfood2.png)

![orderfood3](images/orderfood3.png)

![orderfood4](images/orderfood4.png)

custom은 아무 설정이 안되있는 상태로 intent를 생성한다.

![orderfood5](images/orderfood5.png)

![orderfood6](images/orderfood6.png)

orderfood-menu intent 생성후 발화 및 response 설정 해준다.

---

---

![orderfood7](images/orderfood7.png)

계층구조 intent의 경우 바로 하위 계층 intent로 들어가지 않는다.

---

---

* 마지막 intent (orderfood - menu - confirm)

![orderfood8](images/orderfood8.png)

* 여기서 `$`food 를 이용해서 response를 작성하면 제대로 출력되지 않는다.

![orderfood9](images/orderfood9.png)

![orderfood10](images/orderfood10.png)

* 전 계층의 entity를 쓸수 없다.

![orderfood13](images/orderfood13-1580791908422.png)

`#`을 통해서 전 계층 context (자동으로 정의 된다.)입력 후 그 계층의 entity(`.`food) 를 입력한다.

![orderfood12](images/orderfood12.png)

* 설정 후 진행하면 정상 출력된다.

![orderfood15](images/orderfood15.png)

* 위 계층의 intent 외의 다른 계층의 context에 접근 하고 싶을 떄는 직업 입력을 해주면 된다.

![orderfood14](images/orderfood14.png)

* 여러 메뉴 동시에 주문하기

![orderfood16](images/orderfood16.png)

oderfood-menu intent에서 `food` entitiy의 `is List`를 check해 준다.

-> 메뉴가 list 형식으로 넘어가 여러 메뉴 동시 주문 가능해진다.

![orderfood17](images/orderfood17.png)

* 직접 entity 생성 후 종속 intent 에서 사용하기.

![orderfood18](images/orderfood18.png)

orderfood intent 에서 새 parameter 생성

![orderfood19](images/orderfood19.png)

종속 intent에 상위 context 추가하기

![orderfood20](images/orderfood20.png)

response 설정하기

![orderfood21](images/orderfood21.png)

---

---

* 반복적 (음식 + 수량) or (음식) 입력받기

![order2-1](images/order2-1.png)

food_number entity를 만들어 주고 Define synonyms 체크를 해제해준다.

![order2-2](images/order2-2-1580797332625.png)

![order2-3](images/order2-3.png)

order intent 생성후 text 와 response 설정을 해준다.

![order2-14](images/order2-14.png)

---

---

---

* python과 연동하기

![python](images/python.png)

client access token 이 인증 키이다.

```python
import requests
import json

def get_answer(text, sessionId):
    data_send = {
        'query': text, 'sessionId': sessionId,
        'lang': 'ko', 'timezone' : 'Asia/Seoul'
    }
    data_header = {
        # Bearer 뒤에는 client access token 을 입력하면 된다.
        'Authorization': 'Bearer ab14c5016a0c40acb627887b88f002db',
        'Content-Type': 'application/json; charset=utf-8'
    }

    dialogflow_url = 'https://api.dialogflow.com/v1/query?v=20150910'
    # data 에는 보내고 싶은 데에터 보낸다.
    # header 에는 보내는 데이터에 대한 정보 있다.
    res = requests.post(dialogflow_url, data=json.dumps(data_send), headers=data_header)
    if res.status_code == requests.codes.ok:
       return res.json()    
    return {}
```

```python
dict = get_answer("부산 오늘 날씨 어떄", 'user01')
answer = dict['result']['fulfillment']['speech']
print("Bot:" + answer)
```

```
Bot:2020-02-04 부산광역시 날씨 좋아
```

서버와 무한이 연결되있는 것이 아니라 한번 정보 받아오고 연결 끊긴다.

* 한번 작동이 아닌 무한 작동 하기

```python
while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']
    print("Bot:" + answer)
```

```
->주문
Bot:안녕하세요. 무엇을 드실건가요?
->짜장
Bot:짜장 주문 접수 할까요?
->네
Bot:짜장 주문이 완료되었습니다. -> 홍길동
->짜장2개, 짬뽕2개
Bot:짜장 2, 짬뽕 2 확인
->짜장2개, 짬뽕 15개 주문이요.
Bot:짜장 2, 15 짬뽕 확인
```



[online json viewer](http://jsonviewer.stack.hu/) 으로 json 데이터 구조 파악 할수 있다. 이 떄 데이터는 .json()으로 변형 되지 않았거나 변형 되었다면 json.dumps() 함수로 변형 해준 데이터를 이용해야 된다.

* intentName 확인해보기, parameters 정보 확인해보기



slot filling 이 필요한 경우

```python
dict = get_answer("부산 날씨 어때?", 'user01')
answer = dict['result']['fulfillment']['speech']
print("Bot:" + answer)
```

```
# 날짜에 대한 정보가 필요한 상황이다.
Bot:날짜를 알려주세요.
```

```python
# intentName 출력
print(dict['result']['metadata']['intentName'])
# slot filling 이 필요하면 true, 이미 다 채워져 있으면 false가 출력된다.
print(dict['result']['actionIncomplete'])

params = dict['result']['parameters']

for entity in params:
    print(entity, params[entity])
```

```
weather
True
geo-city 부산광역시
date 
```

slot filling이 다 된 경우

```python
dict = get_answer("짬뽕 2개, 짜장면 3개 주문이요.", 'user01')
answer = dict['result']['fulfillment']['speech']
print("Bot:" + answer)
```

```
Bot:짬뽕 2, 짜장면 3 확인
```

```python
# intentName 출력
print(dict['result']['metadata']['intentName'])
# slot filling 이 필요하면 true, 이미 다 채워져 있으면 false가 출력된다.
print(dict['result']['actionIncomplete'])

params = dict['result']['parameters']

for entity in params:
    print(entity, params[entity])
```

```
order2
False
food_number [{'food': '짬뽕', 'number-integer': 2.0}, {'food': '짜장면', 'number-integer': 3.0}]
```



* (weather or order2) intent에 대해서 모든 parameter 값 있을 떄는 parameter 정보 출력 , 빈 값 있을 떄는 응답 출력



* parameter 값 다 있는 경우(weather)

```python
dict = get_answer("오늘 부산 날씨 어떄?.", 'user01')
answer = dict['result']['fulfillment']['speech']

if dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
    date = dict['result']['parameters']['date']
    geo_city = dict['result']['parameters']['geo-city']
    print("날씨 ", date, geo_city)
else:
    print("Bot:" + answer)
```

```
날씨  2020-02-04 부산광역시
```

* parameter 값 중 빈것이 있는 경우(weather)

```python
dict = get_answer("오늘 날씨 어떄?.", 'user01')
answer = dict['result']['fulfillment']['speech']

if dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
    date = dict['result']['parameters']['date']
    geo_city = dict['result']['parameters']['geo-city']
    print("날씨 ", date, geo_city)
else:
    print("Bot:" + answer)
```

```
Bot:어느 도시를 말씀하시는 건가요?
```



* 반복적으로 위 작업 할 떄

```python
while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']

    if dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
        date = dict['result']['parameters']['date']
        geo_city = dict['result']['parameters']['geo-city']
        print("날씨 ", date, geo_city)
    else:
        print("Bot:" + answer)
```

```
->오늘 날씨
Bot:어느 도시를 말씀하시는 건가요?
->오늘 부산 날씨 어떄
날씨  2020-02-04 부산광역시
->내일 대구 날씨 오떄
날씨  2020-02-05 대구광역시
->대구 날씨
Bot:날짜를 알려주세요.
```



* order2, weather 종합 코드(본인)

```python
while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']

    if dict['result']['metadata']['intentName'] == 'order2' and dict['result']['actionIncomplete'] == False:
        result = 0
        for param in dict['result']['parameters']["food_number"]:
            food = param['food']
            number_integer = param['number-integer']
            if food == '짜장':
                result += number_integer*5000
                print(food, number_integer*5000, "원")
            elif food == '짬뽕':
                result += number_integer*10000
                print(food, number_integer*10000, "원")
            elif food == '탕수육':
                result += number_integer*20000
                print(food, number_integer*20000, "원")
        print("총 ", result, "원")
    elif dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
        date = dict['result']['parameters']['date']
        geo_city = dict['result']['parameters']['geo-city']
        print("날씨 ", date, geo_city)
    else:
        print("Bot:" + answer)
```

```
->오늘 부산 날시
Bot:죄송해요. 다시 들려 주실래요?
->오늘 부산 날씨
날씨  2020-02-04 부산광역시
->짬뽕1, 짜장2
짬뽕 10000.0 원
짜장 10000.0 원
총  20000.0 원
```



* 강사님 코드

```python
while True:
    txt = input("->")
    dict = get_answer(txt, 'user01')
    answer = dict['result']['fulfillment']['speech']

    if dict['result']['metadata']['intentName'] == 'order2' and dict['result']['actionIncomplete'] == False:
        price = {"짜장" : 5000, "짬뽕" : 10000, "탕수육" : 20000}
        params = dict['result']['parameters']['food_number']
        
        # 만약 입력을 '짜장, 짬뽕2' 이렇게 했을 때는 number-interger에 값이 없기 때문에 1을 넘겨주도록 get("number-integer", 1) 이렇게 작성한다.
        output = [food.get("number-integer", 1)*price[food["food"]] for food in params]
        
        print(sum(output))
    elif dict['result']['metadata']['intentName'] == 'weather' and dict['result']['actionIncomplete'] == False:
        date = dict['result']['parameters']['date']
        geo_city = dict['result']['parameters']['geo-city']
        print("날씨 ", date, geo_city)
    else:
        print("Bot:" + answer)
```

```
->짜장1
5000.0
->짬뽕2, 짜장1
25000.0
```





## 3. integrations

* 다른 서버와 연동하는 것을 GUI로 가능하게 해주는 기능이다.



![step](images/step.png)

1. integrations 버튼을 누른다.

2. 연동하고 싶은 것의 버튼을 활성화 한다.

![step2](images/step2-1580780928988.png)

3. 글로벌한 본인 챗봇의 주소가 나온다.

![step3](images/step3.png)

4. 클릭하면 접속할 수 있다.(phone으로 실행시 마이크도 사용할 수 있다.)