
# 線上影音平台 動態爬蟲練習

<font size=2>&emsp;&emsp;主要目的是以爬蟲程式抓取線上影音平台，愛奇藝、KKTV、Line TV、Choco TV、LiTV等五大影音平台，對各平台所擁有的韓劇進行交叉比對，找出共同的韓劇。
> + 愛奇藝韓劇     https://list.tw.iqiyi.com/www/2/17-------------4-1-1-iqiyi--.html
> + KKTV 韓劇     https://www.kktv.me/collection/country/%E9%9F%93%E5%8A%87
> + Line TV韓劇   https://tv.line.me/search?query=%E9%9F%93%E5%8A%87
> + Choco TV韓劇  https://www.chocotv.com.tw/drama?area=kr
> + LiTV韓劇      https://www.litv.tv/vod/drama/advanced_filter.do?f=f_countries%2C%E9%9F%93%E5%9C%8B&p=1

## 1. 愛奇藝

### 1.1 觀察網頁

<font size=2>&emsp;&emsp;讓我們從愛奇藝先開始，首先觀察爬蟲的目標網頁，可以發現他是分頁式的網頁，而且頁碼會隨著跳至下一頁而改動，因此我們可以輕易地切換至下一分頁。


![Imgur](https://i.imgur.com/QvDt65c.png)


<font size=2>&emsp;&emsp;這次的爬重實作，我選用的是「Selenium」套件。簡單為大家科普一下，Selenium是為了測試瀏覽器而生的，但是沒想到在爬蟲的年代，它搖身一變，變成了爬蟲的好工具。Seleninm能控制你的瀏覽器，有模有樣地學人類”看”網頁。那麼你什麼時候會要用到Selenium 呢? 
 > * 發現用普通方法爬不到想要的內容
 > * 網站跟你玩”捉迷藏”，有太多JavaScript內容
 > * 需要像人一樣瀏覽的爬蟲(速度慢，但穩定)


### 1.2 Selenium實作

<font size=2>&emsp;&emsp;引入套件，我使用的瀏覽器是Chrome，需要先把Chrome更新到最新版，並下載driver，指定路徑。利用愛奇藝的網址數字切換，在每頁使用Xpath方法，將劇名append到影片的list，即可輕鬆得到所有劇名，愛奇藝的韓劇約有115部。


```python
from selenium import webdriver
import time

#driver路徑
driver_path = "/home/andy/Downloads/chromedriver"
```


```python
#使用Selenium
driver = webdriver.Chrome(executable_path = driver_path)
#Selenium 內等待元件(隱含等待)
driver.implicitly_wait(3)

films_iqy = []

for i in range(1,4):
    driver.get("https://list.tw.iqiyi.com/www/2/17-------------4-"+ str(i) +"-1-iqiyi--.html")
    film_elem_iqy = driver.find_elements_by_xpath("//a[@class='pic-title']")
    for tag in film_elem_iqy:
        films_iqy.append(tag.text)
    time.sleep(2)

#driver.close()
```


```python
print(films_iqy)
```

    ['多金社長小資女(Rich Man)', '漢摩拉比小姐', '想暫停的瞬間', '油膩的Melo', '無法律師', '情婦(獵豔情人)', '素描Sketch', '金裝律師', '訓南正音', '經常請吃飯的漂亮姐姐', 'Switch-改變世界', '我的大叔', '致忘了詩的你', '偉大的誘惑者', '那個男人吳秀', '小神的孩子們', '加油吧威基基', '超人媽咪袋鼠爸', '廣播羅曼史(Radio Romance)', 'Cross', 'MOTHER', '黑騎士', '操心', '疑問的一勝', '不是機器人啊', 'Jugglers', 'untouchable', '我的鬼神搭檔(Two Cops)', '理判事判(鋌而走險)', 'Melo Holic愛情中毒', '當你沉睡時(電視播出版)', '卞赫的愛情', '20世紀少男少女', '今生是第一次', '愛情的溫度', 'The Package', '付岩洞復仇者們', '當你沉睡時', '醫療船', '名不虛傳', 'Manhole', '犯罪心理：韓國', '救救我', '再次重逢的世界', '王在相愛', '有品位的她', '七日的王妃', '華政', '學校2017', '河伯的新娘', '我的野蠻公主', '奇怪的搭檔', '君主－假面的主人', '三流之路', '芝加哥打字機', '焦急的羅曼史', '悄悄話', '隧道', '她愛上了我的謊', '自體發光辦公室', '師任堂', '大力女子都奉順', '明天和你', 'Voice', '內向的老闆', 'Missing9', '摩登農夫(搖滾情人)', '鬼怪', '藍色海洋的傳說', '浪漫醫生金師傅', '舉重妖精金福珠', '獄中花', '老婆這週要出牆(我配不上她)', '深夜食堂(韓國版)', '他們的Money', '警察夫人', '藍色海洋的傳說【導演版】', '拖旅行箱的女人', 'THE K2', '嫉妒的化身', '通往機場的路', 'The 88th Avenue', 'Doctors(電視播出版)', '獨酒男女', '灰姑娘與四騎士', '打架吧鬼神', 'Good Wife', '結婚契約', 'Doctors', '38師機動隊', 'Touching U', '母親', '吸血鬼檢察官2', '心心相印', '一起吃飯吧2', '吸血鬼偵探', '親愛的恩東', '我的秘密飯店', '不要戀愛要結婚', '壞傢伙們', '九數少年', '具海拉成功記(七顛八起具海拉)', '一起吃飯吧', '仁顯王后的男人', '太陽的後裔', '奔跑吧薔薇', '龍八夷', 'Kill Me Heal Me', '侍女們', '花美男拉麵店', '拜託小姐']



```python
print(len(films_iqy))
```

    116


## 2. KKTV

### 2.1 觀察網頁

<font size=2>&emsp;&emsp;我一樣從網頁開始觀察，發現KKTV的網頁是動態的，將滑鼠滾輪往下滾，所看到的韓劇會愈來愈多，如下面兩張圖所示。因此，這次爬蟲必須增加滑鼠滾動的模擬動作，將滑鼠滾到網頁最底，再開始進行爬蟲。如此一來可以得到韓劇劇名列表，而KKTV的韓劇約有212部。



![Imgur](https://i.imgur.com/enNG19I.png)



<font size=2>&emsp;&emsp;原本只有20部韓劇(上圖所示)，滑鼠往下滾之後，後面又增加了(下圖所示)。



![Imgur](https://i.imgur.com/Qv3VrFM.png)


### 2.2 Selenium實作


```python
#用Selenium開網頁
driver.get("https://www.kktv.me/collection/country/%E9%9F%93%E5%8A%87")

#執行滑鼠向下滾動
for i in range(1,12):
    driver.execute_script('window.scrollTo(0, document.body.scrollHeight);')
    time.sleep(2)

#driver.close()
```


```python
#捲到頁面最上方
x = 0
y = 0
move = 'window.scrollTo('+str(x)+','+str(y)+')'
driver.execute_script(move)
```


```python
#開始爬蟲
film_elem_kk = driver.find_elements_by_xpath("//span[@class='text title']")
films_kk = [tag.text for tag in film_elem_kk]
```


```python
print(films_kk)
```

    ['你也是人類嗎', '訓南正音', '油膩的愛情', '想停止的瞬間 About Time', 'M COUNTDOWN', '最後一次抱緊我', '偉大的誘惑者', '那個男人吳秀', '推理的女王2', 'SHORT 冰上爭鋒', 'Misty 謎霧', '只是相愛的關係', 'Mask', '109什麼事都有', '愛爾蘭上勾拳之戀', '操心', '黑騎士', '世上最美的離別', 'Two Cops 我的鬼神搭檔', 'Melo Holic 愛情中毒', '愛情的溫度', '名不虛傳', '2017 釜山同一個亞洲音樂慶典', '月桂樹西裝店的紳士們', '20世紀少男少女', '卞赫的愛情', '叢林的法則 斐濟篇', '有品位的她', '醫療船', 'PRODUCE 101 第二季', '白鍾元的行動餐車', '叢林的法則 科莫多島篇', '犯罪心理 ：韓國', '河伯的新娘', '七日的王妃', '叢林的法則 紐西蘭篇', '三流之路', '推理的女王', '師任堂', '我的野蠻公主', '奇怪的搭檔', '舉重妖精金福珠', '焦急的羅曼史', '悄悄話', '被告人', '完美的妻子', '金科長', '男模消防員', '所羅門的偽證', '叢林的法則 蘇門答臘篇', '大力女子都奉順', '明天和你', 'Missing9 荒島疑蹤', '內向的老闆', 'Voice 聲命線', '花郎', '叢林的法則 美娜多島篇', '人氣歌謠', '白鍾元的三大天王', '搜查官愛麗絲2', '紅心! 少年射箭部', '戲子 (青春練習曲)', '我們家蜜罐子', '幻影偵探', '五個孩子', '超完美搭檔', 'PRODUCE 101', '嫉妒的化身', '灰姑娘與四騎士', 'Doctors 醫生們', '華麗的誘惑', '女魔頭征服記 (玉氏南征記)', '花樣排球2', '花樣排球', '評價女王', '高品格單戀', '獄中花', '美女孔心', '2016 SBS歌謠大戰', '家和萬事成', '白熙回來了', '拜託媽媽', 'Fantastic', '步步驚心：麗 (韓國播出版)', '叢林的法則 東帝汶篇', '2016 MAMA', '浪漫醫生金師傅', '住在我家的男人', 'Entourage 我家也有大明星', '2016 釜山同一個亞洲音樂慶典', '安托萬夫人', '拖旅行箱的女人', '通往機場的路', 'THE K2', '我有愛人了', '雲畫的月光', '步步驚心：麗', 'Beautiful Mind', '任意依戀 (中文配音版)', 'W － 兩個世界', '任意依戀 (韓文原音版)', '我親愛的朋友們', '又是吳海英', '生意之神－客主2015', '蔣英實', '鄰家律師趙德浩', 'Baby sitter', 'Memory 記憶', '吹笛子的男人', '吸血鬼偵探', '鄰家英雄', '信號 (Signal)', '六龍飛天', '太陽的後裔', 'Riders 抓住明天', 'Beauty Master', '搜查官愛麗絲', '美味的愛戀', '湛藍的天', '撲通撲通LOVE', '加油黃金福', '錐子', 'D-Day 災難來臨日', 'LAST 首爾站露宿者們的順序', '為純情著迷', '村莊：阿雉阿拉的祕密', '上流社會', '看見味道的少女', '我心閃亮', 'Mrs. Cop', '深夜食堂 (韓國版)', '聽到傳聞', '龍八夷', '離婚律師戀愛中', 'Punch (逆轉人生180天)', '皮諾丘', '美女的誕生', '瑞典洗衣店', '集結號', '蒙面檢察官', '無理的前進', '吸血鬼醫生', '七顛八起具海拉', '心心相印', '豪苟的愛', '愛情逆轉勝', 'The Lover', '一起吃飯吧2', '隱藏身份', '記得你 (Remember 記號)', 'Oh 我的鬼神君', '第二個二十歲', '宥美的房間', '請回答1988', '泡泡糖', '心理神探 – 佛斯特', '不善良的女人們', '憤怒的媽媽', '家人之間', '女王之花', 'healer 治癒者', '甜蜜的秘密', '密會', '戀愛當鋪', '去韓國吃什麼', '合宿24號房', '需要浪漫3', '魔女的戀愛', '不要戀愛要結婚', '我的秘密飯店', 'Reset', '九數少年', '壞傢伙們', '未生', '詐欺遊戲', '一起吃飯吧', '真是好時節', '奇妙一家人', '大姊The Eldest', '電競高手', '親親小爸', '請回答1994', '你鄰居的妻子', '無情都市', '秘密', 'IRIS 反恐任務2', '九轉時光的旅行', '戀愛操作團', '第六感任務', '廣告天才李太白', '失業救助羅曼史', '森林之舞', '甜蜜的負擔', '125億的捉迷藏', '請回答1997', '仁顯王后的男人', '仁粹大妃', '屋塔房王世子', '善良的男人', '蝴蝶海', 'Big Heat', '昂心亭', 'Sign', '女人的香氣', '49日', '方子傳', '女帝', '大地之女', '美麗的聲音', '女人都這樣', '原來是美男', 'IRIS 反恐任務']



```python
print(len(films_kk))
```

    212


## 3. Line TV

### 3.1 觀察網頁

<font size=2>&emsp;&emsp;LineTV的韓劇網頁，跟KKTV一樣，需要深探到最底下一層才能取得完整的韓劇名稱列表，而LineTV下方即有提供「察看更多」的按鈕，那麼就讓Selenium來模仿我按按鈕的動作吧~ 不過這次爬蟲面臨了另外的問題，那就是LineTV的韓劇每部都分拆成很多小集，而且其中還夾雜著預告片與MV，所以這邊需要將爬出來的劇名做「正則表達式」的篩選與過濾，之後再利用set的唯一性，將重複的名稱刪除，最後才能得到符合需求的劇名表單。執行結果，雖然LineTV的劇看起來數量很多，但是最後總結僅有11部。



![Imgur](https://i.imgur.com/89T78un.png)


### 3.2 Selenium實作


```python
driver = webdriver.Chrome(executable_path = driver_path)
#Selenium 內等待元件(隱含等待)
driver.implicitly_wait(3)

driver.get("https://tv.line.me/search?query=%E9%9F%93%E5%8A%87")
for i in range(1,25):
    driver.execute_script('window.scrollTo(0, document.body.scrollHeight);')
    time.sleep(2)
    submit_button = driver.find_element_by_css_selector('.btn_box')
    submit_button.click()
    time.sleep(2)
```


```python
#捲到頁面最上方
x = 0
y = 0
move = 'window.scrollTo('+str(x)+','+str(y)+')'
driver.execute_script(move)
```


```python
#引入正則表達示套件
import re
films_line = []
film_elem_line = driver.find_elements_by_xpath("//tooltip")

#將得到的劇名進行過濾
for tag in film_elem_line:
    if "|" in tag.text:
        if "/" not in tag.text:
            if "！" not in tag.text:
                if "(" not in tag.text:
                    #將正則表達式選到地方替代掉
                    tag_text = re.sub(" \|[ a-zA-Z0-9.|\-\u4E00-\u9FA5]+","",tag.text)                                
                    films_line.append(tag_text)
```


```python
#再利用set將重複的部份刪除
print(list(set(films_line)))
```

    ['火星生活', '訓南正音', '愛情的溫度', '操心', '想停止的瞬間', '黑騎士', '我的大叔', '巨匠線上真人 武法律師', '武法律師', '油膩的Melo', '致忘了詩的你', '你也是人類嗎']


## 4. Choco TV

### 4.1 觀察網頁

<font size=2>&emsp;&emsp;ChocoTV的韓劇網頁，一開始我們必須先解決升級廣到的部份，可以使用Selenium去找到叉叉的按鈕，將它關閉即可開始爬蟲，方法基本上跟LineTV一樣，不需要使用正則表達示去擷取劇名，最後總結有102部。



![Imgur](https://i.imgur.com/N8GcrFX.png)


### 4.2 Selenium實作


```python
driver = webdriver.Chrome(executable_path = driver_path)
#Selenium 內等待元件(隱含等待)
driver.implicitly_wait(3)

driver.get("https://www.chocotv.com.tw/drama?area=kr")
time.sleep(2)
#將廣告關閉 => 按右上角的叉叉
close_ad_button = driver.find_element_by_css_selector('.modal-close-img')
close_ad_button.click()
```


```python
#利用按"看更多"，將所有的韓劇列表展開
for i in range(1,4):
    driver.execute_script('window.scrollTo(0, document.body.scrollHeight);')
    time.sleep(2)
    submit_button = driver.find_element_by_css_selector('.more-drama-btn')
    submit_button.click()
    time.sleep(2)
```


```python
#捲到頁面最上方
x = 0
y = 0
move = 'window.scrollTo('+str(x)+','+str(y)+')'
driver.execute_script(move)
```


```python
import re
films_ch = []
film_elem_ch = driver.find_elements_by_xpath("//div[@class='drama-info-title']")
# film_elem_ch 
for tag in film_elem_ch:
    films_ch.append(tag.text)
```


```python
print(films_ch)
```

    ['說出你的心願', '像妳一樣的女兒', '油膩的Melo', '所謂單戀-全知單戀視角', '上流愛情(國語發音)', '上流愛情(韓語發音)', '我的大叔', '偉大的誘惑者', '那個男人吳秀', '小神的孩子們', 'Voice', '只是相愛的關係', 'Signal', 'Misty 謎霧', 'Cross：神的禮物', 'Mother 兩個媽媽', '20世紀少男少女', 'Two Cops 我的鬼神搭檔', '今生是第一次', '操心', 'Jugglers神級秘書', '黑騎士', '舉重妖精金福珠', '七日的王妃', 'Melo Holic愛情中毒', '醫療船', '卞赫的愛情', 'Go Back夫婦', '搜查官愛麗絲 2', '魔女的法庭', '搜查官愛麗絲 1', 'Beauty Master', 'Mask', '戀愛禁止社 (禁戀術士)', '有品位的她', '內衣少女時代', 'To Be Continued', '想念, 春天', '穿梭戀人', '名不虛傳', '0時的她', '無限動力合宿院', '救救我', 'Love in Memory', 'Remember－兒子的戰爭', '愛爾蘭上勾拳之戀', '離婚律師戀愛中', '回來吧大叔', '紅心! 少年射箭部', '學校2017', '聽見你的聲音', '美味的愛戀', '河伯的新娘', 'About Love', '美女孔心', '龍八夷', 'Click Your Heart (點選你的心)', '獄中花', "What's with money", '不滅的女神', '為純情著迷', '偉大的糟糠之妻', '灰姑娘與四騎士', '學校2015', '奔跑吧薔薇', '焦急的羅曼史', 'Doctors醫生們', '我的野蠻公主', '冰丘(冰戀)', '三流之路', '嫉妒的化身', '奇怪的搭檔', '帥氣管家 KDI-109', '大力女子都奉順', '被告人', '假面', '仕女們', '甜辣之戀', '明天男孩', '噩夢老師', 'Spark-觸電男孩', '奇蹟', '愛上挑戰', '積極的體質', '愛上就會死的女人奉順', '金科長', '巧克力銀行', '千年戀愛中', '任意依戀', '花樣排球2', '花樣排球1', 'W兩個世界', '高品格單戀', '評價女王', '撲通撲通LOVE', '奶酪陷阱', '浪漫醫生金師傅', '雲畫的月光', 'Fantastic', '太陽的後裔', '命中注定我愛你', '看見味道的少女']



```python
len(films_ch)
```




    102



## 5. Li TV

### 5.1 觀察網頁

<font size=2>&emsp;&emsp;LiTV的韓劇網頁與LineTV相似，可使用網址列進行分頁的切換以取得完整的劇名列表，即可開始爬蟲。最後在LiTV取得128部劇名。



![Imgur](https://i.imgur.com/c1Hwufz.png)


### 5.2 Selenium實作


```python
driver = webdriver.Chrome(executable_path = driver_path)
#Selenium 內等待元件(隱含等待)
driver.implicitly_wait(3)
films_LI = []
driver.get("https://www.litv.tv/vod/drama/advanced_filter.do?f=f_countries%2C%E9%9F%93%E5%9C%8B&p=1")
film_elem_LI = driver.find_elements_by_xpath("//div[@class='vod-list-content']/h3")
for tag in film_elem_LI:                                
    films_LI.append(tag.text)
for i in range(1,3):
    driver.get("https://www.litv.tv/vod/drama/advanced_filter.do?f=f_countries%2C%E9%9F%93%E5%9C%8B&p="+str(i+1))
    films_elem_LI = driver.find_elements_by_xpath("//div[@class='vod-list-content']/h3")
    for tag in films_elem_LI:                                
        films_LI.append(tag.text)
    next_button = driver.find_element_by_css_selector('.text_next_prev a')    
    next_button.click()
    time.sleep(2)
```


```python
print(films_LI)
```

    ['親親小爸(國語配音)', '王在相愛', '多金社長小資女', '推理的女王 2', 'short 冰上爭鋒', 'Misty 謎霧', '那個男人吳秀', '最後一次抱緊我', '只是相愛的關係', '我有愛人了', '戲子', '安托萬夫人', '家和萬事成', '加油！黃金福', '只屬於你', '美女孔心', '偉大的糟糠之妻', '咖啡王子1號店', '華麗的誘惑', '操心', '月桂樹西裝店的紳士們(國語配音)', '月桂樹西裝店的紳士們', '回來吧大叔', '奇怪的搭檔', '我的野蠻公主', 'Melo Holic 愛情中毒', '超完美搭檔', '你鄰居的妻子', '七日的王妃', '有品位的她', '變身情人', '甜蜜秋天(國語配音)', '甜蜜秋天', '密會', '奇蹟', '嫉妒的化身', '千年戀愛中', '灰姑娘與四騎士', '愛上就會死的女人奉順', '噩夢老師', 'Doctors醫生們(國語配音)', '舉重妖精金福珠', 'Doctors醫生們', '焦急的羅曼史', '師任堂(國語配音)', '師任堂', '湛藍的天(國語配音)', '湛藍的天', '華政(國語配音)', '華政', '再一次初戀', '再一次初戀(國語配音)', '青鳥之家', '女魔頭征服記', '吹吧微風(國語配音)', '吹吧微風', '所羅門的偽證', '大力女子都奉順', '大明星小媳婦', '深夜食堂(韓版國語配音)', '結婚契約', '張玉貞，為愛而生', '花樣排球1', '花樣排球2', '高品格單戀', '評價女王', '拜託媽媽(國語配音)', '拜託媽媽', '雲畫的月光', '任意依戀', 'Entourage我家也有大明星', '浪漫醫生金師傅', 'W－兩個世界', '秘密', '125億的捉迷藏', 'Goodbye Mr.Black', '酒店之王', '好運羅曼史', '為純情著迷', '五個孩子(國語配音)', '再一次 Happy Ending(國語配音)', '再一次 Happy Ending', '五個孩子', '傷停時間', 'SPY', 'Oh My Venus', '我女婿的女人', '愛你的時間', '女王之花', '我女婿的女人(國語配音)', '天上的約定(國語配音)', '天上的約定', '12年後的重逢', 'Fantastic', '警察夫人', '深夜食堂(韓國版)', '女帝', '只想為你(國語配音)', '製作人的那些事', '只想為你', '甜蜜的秘密', '女王之花(國語配音)', '太陽的後裔', '大姊', '她很漂亮', 'Riders:抓住明天', '治癒者', '失業羅曼史', '心情好又暖', '不善良的女人們', '離婚律師戀愛中', '母親', '廣告天才李太白', '仁粹大妃', '我的女兒琴四月', '雙面夏娃', '真是好時節', '憤怒的媽媽', '龍八夷', '夜行書生', '家人之間', '為愛美麗', '奇妙一家人', '(國)看見味道的少女', '看見味道的少女', 'Who Are You學校2015', '好好養大的女兒', '(國)Who Are You學校2015']



```python
print(len(films_LI))
```

    128


## 6. 資料處理比對

### 6.1 資料處理

<font size=2>&emsp;&emsp;我們的目標是找出共同的韓劇，在蒐集完各個OTT平台的韓劇列表後，要進行簡單的資料處理。首先，要將List放入dict結構，創造每個劇的標籤，利用dict轉換成dataframe的格式，並將標籤left join，方便交叉比對。最後，將所有的表格outer join起來，即可得到完整的交叉比對表。


```python
import numpy as np
import pandas as pd

#將List放入dict結構
dict_iqy = {"劇名":films_iqy}
dict_kk = {"劇名":films_kk}
dict_line = {"劇名":list(set(films_line))}
dict_ch = {"劇名":films_ch}
dict_LI = {"劇名":films_LI}
```


```python
#創造每個劇的標籤
ones_iqy = pd.DataFrame(1, index=np.arange(len(films_iqy)), columns=["IQIYI"])
ones_kk = pd.DataFrame(1, index=np.arange(len(films_kk)), columns=["KKTV"])
ones_line = pd.DataFrame(1, index=np.arange(len(films_line)), columns=["LINE"])
ones_ch = pd.DataFrame(1, index=np.arange(len(films_ch)), columns=["CHOCO"])
ones_LI = pd.DataFrame(1, index=np.arange(len(films_LI)), columns=["LITV"])
```


```python
#利用dict轉換成dataframe的格式，並將標籤left join
df_iqy = pd.DataFrame(dict_iqy) 
df_iqy1 = pd.DataFrame.join(df_iqy,ones_iqy, how='left')
df_kk = pd.DataFrame(dict_kk) 
df_kk1 = pd.DataFrame.join(df_kk, ones_kk, how='left')
df_line = pd.DataFrame(dict_line) 
df_line1 = pd.DataFrame.join(df_line, ones_line, how='left')
df_ch = pd.DataFrame(dict_ch) 
df_ch1 = pd.DataFrame.join(df_ch, ones_ch, how='left')
df_LI = pd.DataFrame(dict_LI) 
df_LI1 = pd.DataFrame.join(df_LI, ones_LI, how='left')
```


```python
#將所有的表格outer join
result1 = pd.merge(df_iqy1, df_kk1, how='outer')
result2 = pd.merge(result1, df_line1, how='outer')
result3 = pd.merge(result2, df_ch1, how='outer')
result_all = pd.merge(result3, df_LI1, how='outer')
```

### 6.2 資料比對

<font size=2>&emsp;&emsp;下表是最終彙整的大表格取其中5行來看，可以看到1就是代表在此平台有這部韓劇，而空值則代表沒有，如此即可知道韓劇的OTT平台分佈。


```python
result_all.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }
    .dataframe tbody tr th {
        vertical-align: top;
    }
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>劇名</th>
      <th>IQIYI</th>
      <th>KKTV</th>
      <th>LINE</th>
      <th>CHOCO</th>
      <th>LITV</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>226</th>
      <td>豪苟的愛</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>343</th>
      <td>推理的女王 2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>疑問的一勝</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>77</th>
      <td>老婆這週要出牆(我配不上她)</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>239</th>
      <td>家人之間</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>



<font size=2>&emsp;&emsp;最後，我們將有空值的列資料刪除，留下這5個OTT平台共有的韓劇。如下表所示，僅有1部，這部韓劇是「操心」。這部份爬蟲分析還有許多地方可以鑽研下去。技術上，可以對每個劇名進行更仔細的篩選，有可能同一部劇會有不同的翻譯名稱；分析上，則是可以分析每個OTT平台的選片策略，與該平台的使用者特性的比對。


```python
result_same = result_all.dropna()
result_same
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }
    .dataframe tbody tr th {
        vertical-align: top;
    }
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>劇名</th>
      <th>IQIYI</th>
      <th>KKTV</th>
      <th>LINE</th>
      <th>CHOCO</th>
      <th>LITV</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>24</th>
      <td>操心</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>


