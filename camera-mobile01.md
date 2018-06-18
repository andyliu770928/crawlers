
# Mobile01 爬蟲練習

<font size=2>&emsp;&emsp;主要目的是以爬蟲程式抓取<a href="https://www.mobile01.com/forumtopic.php?c=20&p=1" title="mobile01相機討論區">mobile01相機討論區</a>所有文章列表並儲存檔案，並以csv檔案形式儲存(共20頁)，類別應包含 :
  1. 主題
  2. 回應數
  3. 作者名
  4. 作者發文時間
  5. 最新回應人名
  6. 最新回應時間
  7. 主題網頁路徑

## 分析大綱
[1. 觀察網頁](#1)

[2. PyQuery實作](#2)

> [2.1 單頁資料擷取](#2.1)

> [2.2 多頁資料擷取](#2.2)

[3. 檔案輸出](#3)

[4. 完整程式碼](#4)


<h2 id="1">1. 觀察網頁</h2>

<font size=2>&emsp;&emsp;觀察爬蟲的目標網頁，可以發現他的討論區頁面長的很像表格。因此，我第一個想法是去嘗試用表格存至DataFrame的方式，再去做資料整理。




![Imgur](https://i.imgur.com/x5lIl7O.png)


```python
import pandas as pd
from pyquery import PyQuery as pq
import time
import random

url = "https://www.mobile01.com/forumtopic.php?c=20&p=1"
df = pd.read_html(url)[0]
print(df.head())
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    .dataframe thead th {
        text-align: left;
    }
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>主題</th>
      <th>回覆</th>
      <th>作者</th>
      <th>最新回應</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[Nikon D4 x 紐西蘭南島] [圖多] [有後製請注意]</td>
      <td>7</td>
      <td>2018-05-22 23:09juor2</td>
      <td>2018-05-24 00:39juor2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>【A7 III】她說旅途若是卷底片 ：春櫻，山陽</td>
      <td>8</td>
      <td>2018-05-22 16:39cybercat</td>
      <td>2018-05-23 11:20十八點王子</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Panasonic Lumix GX9 平衡性能代表作 美少... » 234</td>
      <td>30</td>
      <td>2018-05-21 09:38ki_min</td>
      <td>2018-05-23 08:01RogerF200</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D750 五月台北行 九份 陽明山（圖多後製） » 2</td>
      <td>11</td>
      <td>2018-05-21 18:58topx</td>
      <td>2018-05-23 01:13ching43202</td>
    </tr>
    <tr>
      <th>4</th>
      <td>【XT-2】韓國 首爾 跟團初訪。圖多</td>
      <td>1</td>
      <td>2018-05-21 14:06nokye</td>
      <td>2018-05-23 01:03ching43202</td>
    </tr>
  </tbody>
</table>
</div>



<font size=2>&emsp;&emsp;由上圖，可以看出使用read_html的方式，可以完整擷取網頁內容，但是主題後面的">> 234"，這種的分頁數字是我不想要的，如果要處理需要使用Reglaur Experssion的方式處理，問題變得比較複雜，因此我選擇使用PyQuery去做爬蟲處理。(若使用jupyter notebook做編譯，可能需先安裝PyQuery)

<h2 id="2">2. PyQuery實作</h2>

<h3 id="2.1">2.1 單頁資料擷取</h3>

<font size=2>&emsp;&emsp;首先，先試著完成第1頁，之後再處理20頁迴圈的部份。步驟如下:


```python
#1. 使用pyquery去取得網頁內容
url = "https://www.mobile01.com/forumtopic.php?c=20&p=1"
html_doc = pq(url)

#2. 再使用selector Gadget去找各個屬性的css tag
topic_css = ".subject-text > a"
topic_css = ".subject-text > a"
comment_number_css ="td.reply"
author_css =".authur p+ p"
post_time_css = ".authur p:nth-child(1)"
last_commenter_css = ".latestreply p+ p"
last_comment_time_css =".latestreply p:nth-child(1)"
```

<font size=2>&emsp;&emsp;以上網頁上CSS都可以找到，但是還缺少了各個主題的link，這個部份需要利用topic的css，去找pq文件中的 .attrib[ 'href' ] ，並將它前綴加上"https://www.mobile01.com/" ，就可以得到完整的連結。


```python
#3. 分別將資料放進個別的list中
  #主題
topic = []
for tag in html_doc(topic_css):
    topic.append(tag.text)
  #回應數
comment_number = []
for tag in html_doc(comment_number_css):
    comment_number.append(tag.text)
  #作者
author = []
for tag in html_doc(author_css):
    author.append(tag.text)
  #作者發文時間
post_time = []
for tag in html_doc(post_time_css):
    post_time.append(tag.text)
  #最後回應人名
last_commenter = []
for tag in html_doc(last_commenter_css):
    last_commenter.append(tag.text)
  #最後回應時間
last_comment_time = []
for tag in html_doc(last_comment_time_css):
    last_comment_time.append(tag.text)
  #主題連結
topic_link = []
for tag in html_doc(topic_css):
    topic_link.append('https://www.mobile01.com/'+tag.attrib['href'])
```


```python
#4. 將所有list放入dict型式的容器裡
camera_info = {
    "主題": topic,
    "回應數": comment_number,
    "作者名": author,
    "作者發文時間": post_time,
    "最新回應人名": last_commenter,
    "最新回應時間": last_comment_time,
    "主題網頁路徑": topic_link,
}

#5. 創造df格式去放camera_info
camera_info_df = pd.DataFrame(camera_info)
print(camera_info_df.head())
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    .dataframe thead th {
        text-align: left;
    }
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>主題</th>
      <th>主題網頁路徑</th>
      <th>作者名</th>
      <th>作者發文時間</th>
      <th>回應數</th>
      <th>最新回應人名</th>
      <th>最新回應時間</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[Nikon D4 x 紐西蘭南島] [圖多] [有後製請注意]</td>
      <td>https://www.mobile01.com/topicdetail.php?f=248...</td>
      <td>juor2</td>
      <td>2018-05-22 23:09</td>
      <td>7</td>
      <td>juor2</td>
      <td>2018-05-24 00:39</td>
    </tr>
    <tr>
      <th>1</th>
      <td>【A7 III】她說旅途若是卷底片 ：春櫻，山陽</td>
      <td>https://www.mobile01.com/topicdetail.php?f=254...</td>
      <td>cybercat</td>
      <td>2018-05-22 16:39</td>
      <td>8</td>
      <td>十八點王子</td>
      <td>2018-05-23 11:20</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Panasonic Lumix GX9 平衡性能代表作 美少...</td>
      <td>https://www.mobile01.com/topicdetail.php?f=250...</td>
      <td>ki_min</td>
      <td>2018-05-21 09:38</td>
      <td>30</td>
      <td>RogerF200</td>
      <td>2018-05-23 08:01</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D750 五月台北行 九份 陽明山（圖多後製）</td>
      <td>https://www.mobile01.com/topicdetail.php?f=248...</td>
      <td>topx</td>
      <td>2018-05-21 18:58</td>
      <td>11</td>
      <td>ching43202</td>
      <td>2018-05-23 01:13</td>
    </tr>
    <tr>
      <th>4</th>
      <td>【XT-2】韓國 首爾 跟團初訪。圖多</td>
      <td>https://www.mobile01.com/topicdetail.php?f=246...</td>
      <td>nokye</td>
      <td>2018-05-21 14:06</td>
      <td>1</td>
      <td>ching43202</td>
      <td>2018-05-23 01:03</td>
    </tr>
  </tbody>
</table>
</div>



<font size=2>&emsp;&emsp;以上整理出單頁的網站資訊列表，但是我發現在主題那一個欄位，會發生因主題太長而無法顯示完全的情形，如同上圖的第3行所示。為了改善這個情形，利用字串.endwith("...")判斷式去找出"..."結尾的主題，並進入主題網頁，再次進行pyquery，取得完整主題名稱。


```python
#主題(完整主題名稱)
topic = []
for tag in html_doc(topic_css):
    #以"..."結尾的名稱就必須進入它的主題連結，去獲得完整主題名稱
    if (tag.text.endswith("...")==True):
        #得到主題連結
        topic_url = 'https://www.mobile01.com/'+ tag.attrib['href']
        #進入主題連結取得主題連結的網頁內容
        topic_html_doc=pq(topic_url)
        #得到完整主題的CSS
        advance_topic_css= ".topic"
        #再將完整主題放入topic中
        for tag in topic_html_doc(advance_topic_css):
            topic.append(tag.text)
    else:
        topic.append(tag.text)
```
<h3 id="2.2">2.2 多頁資料擷取</h3>

<font size=2>&emsp;&emsp;現在已經完成一頁的網頁爬蟲，並把過程寫成"get_camera_info"的function。那麼如果要完整爬完Mobile01相機討論區，共20頁的內容，該怎麼做呢?
  1. 直接取得下一頁的網址
  2. 設定按下一頁的按鈕 => 必須使用Selenium的技巧
    
<font size=2>&emsp;&emsp;我們可以觀察到Mobile01討論區的網址有一個特性，就是會把頁碼擺在最後面，例如:「 https://www.mobile01.com/forumtopic.php?c=20&p=2 」這對於爬蟲的人來說是一大福音，因為可以簡單地替換最後一個數字，以取得其他分頁的網址，因此我會選擇方案1，利用迴圈變更頁數去擷取多頁的資料。


```python
#先處理第一頁，同時創建camera_info_all容器
url1 = 'https://www.mobile01.com/forumtopic.php?c=20&p=1'
camera_info_all = get_camera_info(url1)        

#以迴圈的型式處理剩餘的19頁資料
for i in range(2,21):
    #利用網址後方未加密參數去連結所有頁面
    urls = 'https://www.mobile01.com/forumtopic.php?c=20&p=' + str(i)
    #得到新資料
    new_data = get_camera_info(urls)
    #將新資料放入camera_info_all容器中
    camera_info_all = camera_info_all.append(new_data,ignore_index=True)  
```

<font size=2>&emsp;&emsp;在只爬1頁的時候，時間大概是1~2秒就可以完成，速度很快也很順利，但是當我要20頁一起執行時，我發現程式做道第4、5頁時就會卡住，不會有錯誤訊息，但是就是持續執行中跳不出來。這是怎麼回事呢?推測應該是被反爬蟲程式擋住了，市面上常見的反爬蟲方法分成以下幾種：
  1. 通過User-Agent來控制訪問，限制headers要是一般電腦
  2. 統計單位時間的request數量，進行連線剔除
  3. 固定IP短時間內大量爬蟲，進行IP封鎖
  4. 通過JS腳本來防止爬蟲(ex.驗證碼，滑動解鎖)

<font size=2>&emsp;&emsp;接下來我就要來反反爬蟲了，首先目前先觀察mobile01的反爬蟲機制。當我的程式被卡住後，重新啟動再次執行還是可以取得前4-5頁後才被卡住，所以可以得知，對於我的爬蟲程式mobile01並沒有做IP封鎖的動作。因此，我決定先試試修改User-Agent與減慢爬蟲程式的執行速度，查看是否可以執行。
  1. User-Agent修改:我制定了一個長得像一般電腦的headers，並放入pyquery的變數中
  2. 減緩爬蟲速度:我利用time與random套件，寫出一個讓程式暫停執行的指令"time.sleep(random.randint(5,15))"，在每次下request完都讓爬蟲休息5-15秒不等的時間，也讓mobile01的server連線負擔可以不要太重。


```python
#更改headers假裝自己是一般電腦
headers = {
    'User-Agent':'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6'
}

#使用pyquery去取得網頁內容
html_doc = pq(url, headers = headers)

#減緩發request頻率
time.sleep(random.randint(5,15))
```

<font size=2>&emsp;&emsp;經由以上的修改後，我確實能完整得到20頁mobile01討論區的文章資料，總共有612筆與估算值相符，那麼最後一個步驟就是得輸出到CSV檔了。

<h2 id="3">3. 檔案輸出</h2>

<font size=2>&emsp;&emsp;檔案輸出雖然看似簡單，但是名稱與版次管理在建立檔案時就必須去考慮，所以我考量討論區會持續有新文章出現，所以我在檔名的命名時，希望能增加時間戳記，以利未來資料分析時可以更有效率。這邊我引入datetime功能，它可叫出今天的年月日時分...等數據，並以此數據命名csv檔。


```python
from datetime import datetime 
#幫檔案做時間戳記到年月日時
time = datetime.now().strftime("%Y%m%d%H%M")  
#輸出csv檔
camera_info_all.to_csv('mobile01_camera_'+ time +'.csv')
```

![Imgur](https://i.imgur.com/iZywSSn.png)


<h2 id="4">4. 完整程式碼</h2>


```python
import pandas as pd
from pyquery import PyQuery as pq
import time
import random
%matplotlib inline


def get_camera_info(url):
        
    """
    Get certain camara info from mobile01
    topic, comment_number, author, post_time, last_commenter, last_comment_time, topic_link 
    """
    #更改headers假裝自己是一般電腦
    headers = {
        'User-Agent':'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6'
    }
    
    #使用pyquery去取得網頁內容
    html_doc = pq(url, headers = headers)
    #確認讀入的註記
    print(url)    

    #找到各個資訊的css tag
    topic_css = ".subject-text > a"
    comment_number_css ="td.reply"
    author_css =".authur p+ p"
    post_time_css = ".authur p:nth-child(1)"
    last_commenter_css = ".latestreply p+ p"
    last_comment_time_css =".latestreply p:nth-child(1)"


    #topic
    topic = []
    for tag in html_doc(topic_css):
        #方法1:寫法簡單，但是主題名稱太長的會不全，以"..."結尾
#         topic.append(tag.text)  

        #方法2:以"..."結尾的名稱就必須進入它的主題連結，去獲得完整主題名稱
        if (tag.text.endswith("...")==True):
            #得到主題連結
            topic_url = 'https://www.mobile01.com/'+ tag.attrib['href']
            #進入主題連結取得主題連結的網頁內容
            topic_html_doc=pq(topic_url,headers = headers)
            #減緩發request頻率
            time.sleep(random.randint(5,15))
            #得到完整主題的CSS
            advance_topic_css= ".topic"
            #再將完整主題放入topic中
            for tag in topic_html_doc(advance_topic_css):
                topic.append(tag.text)
        else:
            topic.append(tag.text)
            
    #comment_number
    comment_number = []
    for tag in html_doc(comment_number_css):
        comment_number.append(tag.text)
    #author
    author = []
    for tag in html_doc(author_css):
        author.append(tag.text)
    #post_time
    post_time = []
    for tag in html_doc(post_time_css):
        post_time.append(tag.text)
    #last_commenter
    last_commenter = []
    for tag in html_doc(last_commenter_css):
        last_commenter.append(tag.text)
    #last_comment_time
    last_comment_time = []
    for tag in html_doc(last_comment_time_css):
        last_comment_time.append(tag.text)
    #topic_link
    topic_link = []
    for tag in html_doc(topic_css):
        topic_link.append('https://www.mobile01.com/'+tag.attrib['href'])
    
    #將所有list放入dict型式的容器裡
    camera_info = {
        "主題": topic,
        "回應數": comment_number,
        "作者名": author,
        "作者發文時間": post_time,
        "最新回應人名": last_commenter,
        "最新回應時間": last_comment_time,
        "主題網頁路徑": topic_link,
    }
    
    #創造df格式去接camera_info
    camera_info_df = pd.DataFrame(camera_info)
    
    #本頁已執行完畢的註記
    print("check")
    return camera_info_df


#先處理第一頁，同時創建camera_info_all容器
url1 = 'https://www.mobile01.com/forumtopic.php?c=20&p=1'
camera_info_all = get_camera_info(url1)
#減緩發request頻率
time.sleep(random.randint(5,15))         

#以迴圈的型式處理剩餘的19頁資料
for i in range(2,21):
    #利用網址後方未加密參數去連結所有頁面
    urls = 'https://www.mobile01.com/forumtopic.php?c=20&p=' + str(i)
    #得到新資料
    new_data = get_camera_info(urls)
    #將薪資料放入camera_info_all容器中
    camera_info_all = camera_info_all.append(new_data,ignore_index=True)
    #減緩發request頻率
    time.sleep(random.randint(5,15))  
    
from datetime import datetime 
#幫檔案做時間戳記到年月日時
time = datetime.now().strftime("%Y%m%d%H%M")  
#輸出csv檔
camera_info_all.to_csv('mobile01_camera_'+ time +'.csv')    

```
