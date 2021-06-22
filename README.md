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
  *查詢天氣
輸入所在地區或是想查詢的地區，就會跑出接下來36小時的天氣概況，若是想知道更多天氣情況可以點擊按鈕進入中央氣象局看更多天氣詳情。
  *查詢匯率
輸入想要查詢的外幣，便會顯示該幣值目前對於台幣的匯率，此匯率是透過台灣銀行的資料庫來進行顯示
  *翻譯功能
輸入欲翻譯的語言代碼，再輸入要翻譯的文字，便能顯示翻譯後的文字

* 使用流程:

![image](https://user-images.githubusercontent.com/51864985/122882701-0a259d80-d36f-11eb-8383-397ca6b36065.png)
## Details of the approach
## Results
## References
## 成為好友吧
![image](https://user-images.githubusercontent.com/51864985/122883500-d8f99d00-d36f-11eb-899c-0bd98c71e570.png)
