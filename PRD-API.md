## api使用
### 百度翻译翻译api，百度语音识别，语音合成api
```
import requests
import string
import time
import hashlib
import json
 
#init
api_url = "http://api.fanyi.baidu.com/api/trans/vip/translate"
my_appid = 20181213000247793
cyber = '57lCiFG1VhMp2UXxalBJ'
lower_case = list(string.ascii_lowercase)
 
def requests_for_dst(word):
    #init salt and final_sign
    salt = str(time.time())[:10]
    final_sign = str(my_appid)+word+salt+cyber
    final_sign = hashlib.md5(final_sign.encode("utf-8")).hexdigest()
    #区别en,zh构造请求参数
    if list(word)[0] in lower_case:
        paramas = {
            'q':word,
            'from':'en',
            'to':'zh',
            'appid':'%s'%my_appid,
            'salt':'%s'%salt,
            'sign':'%s'%final_sign
            }
        my_url = api_url+'?appid='+str(my_appid)+'&q='+word+'&from='+'en'+'&to='+'zh'+'&salt='+salt+'&sign='+final_sign
    else:
        paramas = {
            'q':word,
            'from':'zh',
            'to':'en',
            'appid':'%s'%my_appid,
            'salt':'%s'%salt,
            'sign':'%s'%final_sign
            }
        my_url = api_url+'?appid='+str(my_appid)+'&q='+word+'&from='+'zh'+'&to='+'en'+'&salt='+salt+'&sign='+final_sign
    response = requests.get(api_url,params = paramas).content
    content = str(response,encoding = "utf-8")
    json_reads = json.loads(content)
    print(json_reads['trans_result'][0]['dst'])
 
while True:
    word = input("输入你想翻译的内容: ")
    requests_for_dst(word)
```

## 语音识别与语音合成
```
from aip import AipSpeech

""" 你的 APPID AK SK """
APP_ID = '15092311'
API_KEY = '1LB5Dl5zAGPfWCYUIBxQDXPT'
SECRET_KEY = 'leOKhkN7RxMComv4ZBFIVYxibc8bZQ2i'

client = AipSpeech(APP_ID, API_KEY, SECRET_KEY)
```
```
# 读取文件
def get_file_content(filePath):
    with open(filePath, 'rb') as fp:
        return fp.read()
```
```
# 识别本地文件
client.asr(get_file_content('auido.amr'), 'amr', 8000, {
    'dev_pid': 1536,
})
result  = client.synthesis('方言翻译器挺不错的呢', 'zh', 1, {
    'vol': 5,
})

# 识别正确返回语音二进制 错误则返回dict 参照下面错误码
if not isinstance(result, dict):
    with open('auido.mp3', 'wb') as f:
        f.write(result)
```

[2016-2017翻译机市场规模走势](/img/市场.png)（文：[中国翻译机行业市场市场监测及前景未来预测](https://www.chyxx.com/research/201811/691794.html) 
