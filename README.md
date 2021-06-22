# GO旅遊小幫手
## Build process
* 前端
  * 創建Linebot
  
   ![image](https://user-images.githubusercontent.com/51864985/122884482-ca5fb580-d370-11eb-8523-c6831ffa07f4.png)
  * 對話回應
  
   ![image](https://user-images.githubusercontent.com/51864985/122884933-34785a80-d371-11eb-8262-6acf68e4b998.png)

* 後端
  * 將程式部署到Heroku
    * 新增Procfile，告訴Heroku如何啟動這個app
    * 新增requirements.txt，告訴Heroku這個app的環境需要哪些其他套件
    * 新增runtime.txt，告訴Heroku這個app指定的python版本
## Introduction
* 主題發想來源：在出遊的時候常常會遇到各種奇怪的問題，像是不知道當地天氣如何或是花完外幣但還想繼續購物，基於以上種種原因讓我想製作一個旅遊機器人。

* 目的：在旅遊時會需要用到查天氣或翻譯功能，有時還會需要用到查詢匯率功能，但是要這樣單獨個別去查詢的話會太麻煩不方便，才想透過每個人不論男女老少都會用的APP – LINE 來開發具有上述功能的機器人，方便大家直接查詢。

* 說明各項功能
  * 查詢天氣：
    輸入所在地區或是想查詢的地區，就會跑出接下來36小時的天氣概況，若是想知道更多天氣情況可以點擊按鈕進入中央氣象局看更多天氣詳情。
  * 查詢匯率：
    輸入想要查詢的外幣，便會顯示該幣值目前對於台幣的匯率，此匯率是透過台灣銀行的資料庫來進行顯示
  * 翻譯功能：
    輸入欲翻譯的語言代碼，再輸入要翻譯的文字，便能顯示翻譯後的文字

* 使用流程:

 ![image](https://user-images.githubusercontent.com/51864985/122882701-0a259d80-d36f-11eb-8383-397ca6b36065.png)
## Details of the approach
* 建立Flask物件，設定Channel secret及Channel access token
```python
app = Flask(__name__)
# LINE BOT info
line_bot_api = LineBotApi('QENbrXK2ILlWZcsE66gdCG9JZErWh89eaiba8ca9cpbIg+Ief6A6XgOuXIQlB0Z0D6InAMaZeoUf2wp7O9CZFNtZP5CNUW6JxRqOtJLczNGI/za2aNxvPUgAwDDe99vIQPzP7A9ckaS95cSN6oaSKQdB04t89/1O/w1cDnyilFU=')
handler = WebhookHandler('8d69c575546b27890e4fd909143a8e8c')
```
* 建立callback路由
```python
@app.route("/callback", methods=['POST'])
def callback():
    signature = request.headers['X-Line-Signature']
    body = request.get_data(as_text=True)
    app.logger.info("Request body: " + body)
    print(body)
    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        abort(400)
    return 'OK
```
* 獲取縣市天氣資料
```python
def get(city):
    token = 'CWB-E86446FB-80A5-4626-B3FD-A61BBE114060'
    url = 'https://opendata.cwb.gov.tw/api/v1/rest/datastore/F-C0032-001?Authorization=' + token + '&format=JSON&locationName=' + str(city)
    Data = requests.get(url)
    Data = (json.loads(Data.text,encoding='utf-8'))['records']['location'][0]['weatherElement']
    res = [[] , [] , []]
    for j in range(3):
        for i in Data:
            res[j].append(i['time'][j])
    return res
```
* 根據代號選擇翻譯語言
```python
@handler.add(MessageEvent)
def handle_message(event):
    message_type = event.message.type
    user_id = event.source.user_id
    reply_token = event.reply_token
    message = event.message.text
    
    a=[]
    text=message
    a=text.split(':',1)
    translator = Translator()
    if (a[0]=='0'):
        result = translator.translate(a[1],dest='zh-TW').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='1'):
        result = translator.translate(a[1],dest='en').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='2'):
        result = translator.translate(a[1],dest='ja').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='3'):
        result = translator.translate(a[1],dest='ko').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='4'):
        result = translator.translate(a[1],dest='fr').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='5'):
        result = translator.translate(a[1],dest='de').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='6'):
        result = translator.translate(a[1],dest='ru').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
    elif (a[0]=='7'):
        result = translator.translate(a[1],dest='th').text
        line_bot_api.reply_message(reply_token,TextSendMessage(text=result))
```
* 根據貨幣名稱抓取匯率數據
```python
tlist = ['現金買入','現金賣出','即期買入','即期賣出']
    currency = message
    show = currency + '匯率:\n'
    if currency in keys:
        for i in range(4):
            exchange = float(twder.now(currencies[currency])[i+1])
            if i!=3:
                show = show + tlist[i] + ':' + str(exchange) + '\n'
            else:
                show = show + tlist[i] + ':' + str(exchange)
        line_bot_api.reply_message(reply_token,TextSendMessage(text=show))
    #print(show)
    '''else:
        line_bot_api.reply_message(reply_token,TextSendMessage(text="無此貨幣資料!"))
        #print('無此貨幣資料!')'''
```
* 調整天氣輸出格式
```python
if(message[:2] == '天氣'):
        city = message[3:]
        city = city.replace('台','臺')
        if(not (city in cities)):
            line_bot_api.reply_message(reply_token,TextSendMessage(text="查詢格式為: 天氣 縣市"))
        else:
            res = get(city)
            line_bot_api.reply_message(reply_token, TemplateSendMessage(
                alt_text = city + '未來 36 小時天氣預測',
                template = CarouselTemplate(
                    columns = [
                        CarouselColumn(
                            thumbnail_image_url = 'https://i.imgur.com/Ex3Opfo.png',
                            title = '{} ~ {}'.format(res[0][0]['startTime'][5:-3],res[0][0]['endTime'][5:-3]),
                            text = '天氣狀況 {}\n溫度 {} ~ {} °C\n降雨機率 {}'.format(data[0]['parameter']['parameterName'],data[2]['parameter']['parameterName'],data[4]['parameter']['parameterName'],data[1]['parameter']['parameterName']),
                            actions = [
                                URIAction(
                                    label = '詳細內容',
                                    uri = 'https://www.cwb.gov.tw/V8/C/W/County/index.html'
                                )
                            ]
                        )for data in res
                    ]
                )
            ))
```
* 提醒使用者輸入正確格式
```python
line_bot_api.reply_message(reply_token, TextSendMessage(text="查詢格式為: 天氣 縣市\n輸入貨幣名稱 ex:美元、歐元\n翻譯格式:\n中文 0:欲翻譯之句子\n英文 1:欲翻譯之句子\n日文 2:欲翻譯之句子\n韓文 3:欲翻譯之句子\n法文 4:欲翻譯之句子\n德文 5:欲翻譯之句子\n俄文 6:欲翻譯之句子\n泰文 7:欲翻譯之句子"))
```
## Results
* 說明輸入格式
  * 查詢匯率
  * 進行翻譯

![image](https://user-images.githubusercontent.com/51864985/122893195-af913f00-d378-11eb-89b6-20e2d36ccc07.png)

  * 查詢天氣

![image](https://user-images.githubusercontent.com/51864985/122893093-94beca80-d378-11eb-9889-b68afa65eb68.png)
## References
* 吉哥的分享

  https://sites.google.com/jes.mlc.edu.tw/ljj/%E9%A6%96%E9%A0%81?authuser=0
* 課程教材
* API使用
  * 中央氣象局 https://www.cwb.gov.tw/rss/forecast/36_05.xml
  * 台銀匯率 https://rate.bot.com.tw/xrt/all/day
## 成為好友吧
![image](https://user-images.githubusercontent.com/51864985/122883500-d8f99d00-d36f-11eb-899c-0bd98c71e570.png)
