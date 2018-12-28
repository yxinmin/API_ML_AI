## api使用
### 百度翻译api，需要申请开发者权限
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

### 语音识别与语音合成
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
# 识别本地文件
client.asr(get_file_content('auido.amr'), 'amr', 8000, {
    'dev_pid': 1536,
})

```
```
result  = client.synthesis('方言翻译器挺不错的呢', 'zh', 1, {
    'vol': 5,
})

# 识别正确返回语音二进制 错误则返回dict 参照下面错误码
if not isinstance(result, dict):
    with open('auido.mp3', 'wb') as f:
        f.write(result)
```
### 讯飞语音听写的使用
参考文章:
- [科大讯飞开放平台————语音听写接口的使用](https://www.cnblogs.com/jiayongji/p/5300563.html)
- [python实现调用科大讯飞语音听写（将音频识别成文字输出）](https://blog.csdn.net/Smile_coderrr/article/details/81636015)
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
#import urllib2
import time
import urllib
import json
import hashlib
import base64
import urllib.request
import urllib.parse
 
 
def main():
    f = open("C:\Users\candy\Documents\WeChat Files\wxid_uyujwzccqksk22\Files\1.mp3", 'rb')  #rb表示二进制格式只读打开文件
    file_content = f.read()
    # file_content 是二进制内容，bytes类型
    # 由于Python的字符串类型是str，在内存中以Unicode表示，一个字符对应若干个字节。
    # 如果要在网络上传输，或者保存到磁盘上，就需要把str变为以字节为单位的bytes
    # 以Unicode表示的str通过encode()方法可以编码为指定的bytes
    base64_audio = base64.b64encode(file_content)   #base64.b64encode()参数是bytes类型，返回也是bytes类型
    body = urllib.parse.urlencode({'audio': base64_audio})
 
    url = 'http://api.xfyun.cn/v1/service/v1/iat'
    api_key = 'a6b2d0d289e3f930a22cff17aa661a26'
    param = {"engine_type": "sms16k", "aue": "raw"}
 
    x_appid = '5c0d0c34d'
    x_param = base64.b64encode(json.dumps(param).replace(' ', '').encode('utf-8')) #改('''')
    # 这是3.x的用法，因为3.x中字符都为unicode编码，而b64encode函数的参数为byte类型，
    # 所以必须先转码为utf-8的bytes
    x_param = str(x_param, 'utf-8')
 
    x_time = int(int(round(time.time() * 1000)) / 1000)
    x_checksum = hashlib.md5((api_key + str(x_time) + x_param).encode('utf-8')).hexdigest() #改
    x_header = {'X-Appid': x_appid,
                'X-CurTime': x_time,
                'X-Param': x_param,
                'X-CheckSum': x_checksum}
    # 不要忘记url = ??, data = ??, headers = ??, method = ?? 中的“ = ”，这是python3
    req = urllib.request.Request(url = url, data = body.encode('utf-8'), headers = x_header, method = 'POST')
    result = urllib.request.urlopen(req)
    result = result.read().decode('utf-8')
    print(result)
    return
 
if __name__ == '__main__':
    main()

 ```
 
- [科大讯飞开放平台————语音听写接口的使用](https://www.cnblogs.com/jiayongji/p/5300563.html)
 1、XML代码：
界面中有一个按钮，一个TextView和一个EditText，EditText用于显示语音识别的结果
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <Button
        android:id="@+id/listen_btn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="开始说话" />

    <TextView
        android:id="@+id/task_tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="20dp"
        android:text="日程安排：" />

    <EditText
        android:id="@+id/content_et"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_dropdown_light_frame"
        android:cursorVisible="true"
        android:enabled="true"
        android:gravity="top"
        android:visibility="visible" />

</LinearLayout>
```
 2、MainActivity
```
import java.lang.reflect.Type;
import java.util.List;

import com.example.speechrecognition.DictationResult;
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;
import com.iflytek.cloud.RecognizerListener;
import com.iflytek.cloud.RecognizerResult;
import com.iflytek.cloud.SpeechConstant;
import com.iflytek.cloud.SpeechError;
import com.iflytek.cloud.SpeechRecognizer;
import com.iflytek.cloud.SpeechUtility;
import com.iflytek.cloud.ui.RecognizerDialog;
import com.iflytek.cloud.ui.RecognizerDialogListener;

import android.app.Activity;
import android.content.Context;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.inputmethod.InputMethodManager;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

public class MainActivity extends Activity implements OnClickListener {
    private static String APPID = "569e39a1";

    private Button listenBtn;
    private EditText contentEt;

    // 听写结果字符串（多个Json的列表字符串）
    private String dictationResultStr = "[";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        listenBtn = (Button) findViewById(R.id.listen_btn);
        contentEt = (EditText) findViewById(R.id.content_et);

        listenBtn.setOnClickListener(this);

    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
        case R.id.listen_btn:

            dictationResultStr = "[";
            // 语音配置对象初始化
            SpeechUtility.createUtility(MainActivity.this, SpeechConstant.APPID
                    + "=" + APPID);

            // 1.创建SpeechRecognizer对象，第2个参数：本地听写时传InitListener
            SpeechRecognizer mIat = SpeechRecognizer.createRecognizer(
                    MainActivity.this, null);
            // 交互动画
            RecognizerDialog iatDialog = new RecognizerDialog(
                    MainActivity.this, null);
            // 2.设置听写参数，详见《科大讯飞MSC API手册(Android)》SpeechConstant类
            mIat.setParameter(SpeechConstant.DOMAIN, "iat"); // domain:域名
            mIat.setParameter(SpeechConstant.LANGUAGE, "zh_cn");
            mIat.setParameter(SpeechConstant.ACCENT, "mandarin"); // mandarin:普通话
            
            //3.开始听写
            iatDialog.setListener(new RecognizerDialogListener() {

                @Override
                public void onResult(RecognizerResult results, boolean isLast) {
                    // TODO 自动生成的方法存根
                    // Log.d("Result", results.getResultString());
                    // contentTv.setText(results.getResultString());
                    if (!isLast) {
                        dictationResultStr += results.getResultString() + ",";
                    } else {
                        dictationResultStr += results.getResultString() + "]";
                    }
                    if (isLast) {
                        // 解析Json列表字符串
                        Gson gson = new Gson();
                        List<DictationResult> dictationResultList = gson
                                .fromJson(dictationResultStr,
                                        new TypeToken<List<DictationResult>>() {
                                        }.getType());
                        String finalResult = "";
                        for (int i = 0; i < dictationResultList.size() - 1; i++) {
                            finalResult += dictationResultList.get(i)
                                    .toString();
                        }
                        contentEt.setText(finalResult);
                        
                        //获取焦点
                        contentEt.requestFocus();
                        
                        //将光标定位到文字最后，以便修改
                        contentEt.setSelection(finalResult.length());
                        
                        Log.d("From reall phone", finalResult);
                    }
                }

                @Override
                public void onError(SpeechError error) {
                    // TODO 自动生成的方法存根
                    error.getPlainDescription(true);
                }
            });

            // 开始听写
            iatDialog.show();

            break;
        default:
            break;
        }
    }
}
```
 3、自定义的com.example.speechrecognition.DictationResult类的代码：
```
import java.util.List;

/**
 * 解析语音听写返回结果Json格式字符串的模板类（多重嵌套Json）
 * 
 * 语音识别结果Json数据格式（单条数据）：
 * {"sn":1,"ls":true,"bg":0,"ed":0,"ws":[{"bg":0,"cw":[{"w":"今天","sc":0}]},
 * {"bg":0,"cw":{"w":"的","sc":0}]},{"bg":0,"cw":[{"w":"天气","sc":0}]},
 * {"bg":0,"cw":[{"w":"怎么样","sc":0}]},{"bg":0,"cw":[{"w":"。","sc":0}]}]}
 */
public class DictationResult {
    private String sn;
    private String ls;
    private String bg;
    private String ed;

    private List<Words> ws;

    public static class Words {
        private String bg;
        private List<Cw> cw;

        public static class Cw {
            private String w;
            private String sc;

            public String getW() {
                return w;
            }

            public void setW(String w) {
                this.w = w;
            }

            public String getSc() {
                return sc;
            }

            public void setSc(String sc) {
                this.sc = sc;
            }

            @Override
            public String toString() {
                return w;
            }
        }

        public String getBg() {
            return bg;
        }

        public void setBg(String bg) {
            this.bg = bg;
        }

        public List<Cw> getCw() {
            return cw;
        }

        public void setCw(List<Cw> cw) {
            this.cw = cw;
        }

        @Override
        public String toString() {
            String result = "";
            for (Cw cwTmp : cw) {
                result += cwTmp.toString();
            }
            return result;
        }
    }

    public String getSn() {
        return sn;
    }

    public void setSn(String sn) {
        this.sn = sn;
    }

    public String getLs() {
        return ls;
    }

    public void setLs(String ls) {
        this.ls = ls;
    }

    public String getBg() {
        return bg;
    }

    public void setBg(String bg) {
        this.bg = bg;
    }

    public String getEd() {
        return ed;
    }

    public void setEd(String ed) {
        this.ed = ed;
    }

    public List<Words> getWs() {
        return ws;
    }

    public void setWs(List<Words> ws) {
        this.ws = ws;
    }

    @Override
    public String toString() {
        String result = "";
        for (Words wsTmp : ws) {
            result += wsTmp.toString();
        }
        return result;
    }
}
```
 4、AndroidManifest.xml中申请权限：
```
<!-- 连接网络权限，用于执行云端语音能力 -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <!-- 获取手机录音机使用权限，听写、识别、语义理解需要用到此权限  -->
    <uses-permission android:name="android.permission.RECORD_AUDIO"/> 
    <!--读取网络信息状态 -->  
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>  
    <!--获取当前wifi状态 -->  
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>  
    <!--允许程序改变网络连接状态 -->
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
    <!--读取手机信息权限 -->  
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>  
    <!--读取联系人权限，上传联系人需要用到此权限 -->  
    <uses-permission android:name="android.permission.READ_CONTACTS"/>
```
 5、测试：模拟器无法打开录音机，是不能在上面测试的，要用真机测试。
   ![测试结果](/img/语音听写.png)

