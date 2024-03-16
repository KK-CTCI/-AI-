# -影片字幕生成 with MemoAI-
利用免費工具MemoAI將無字幕外語影片轉成雙語字幕版

## 主要工具
影片字幕產生與自動翻譯的 AI工具
介紹: https://youtu.be/T1NBZIgjO0U?si=M5R1P-nL4wso-tEr

下載: https://memo.ac/download.html

相關(ChatGPT API Key): https://platform.openai.com/api-keys

下載MemoAI這個工具(檔案有點大200MB)，然後安裝，裝好後會需要一組MemoAI金鑰，到官網註冊就有了，貼上去就可以啟動。

其他操作請看上方介紹影片。

## 問題處理

1. 生成的字幕很難編輯。搜尋取代功能也有bug，建議可以先匯出.srt檔案，校稿後選擇重新轉寫，這次把修改好的srt匯入進來，這樣比較有效率。

2. 如果已經有.srt或.vtt的字幕檔，在選擇影片後設定轉寫的參數畫面時，可以直接把字幕檔案匯入，就不用讓電腦跑聲音轉文字的功能了。

3. 關於VTT的字幕檔案格式，檢查一下MemoAI載入後，最後一句是不是沒有顯示出來。如果是，請在原來VTT檔案裏面的最後一行，再換行新增一行，內容就填一個很遠的時間點，例如
10:00:25.266 --> 10:02:28.666
這樣最後一行再由MemoAI匯入VTT檔案時就會出現了。

4. 遇到大量VTT檔案都有上面的情況，要一個一個打開編輯實在太累了，所以我請Claude (https://claude.ai/) 寫了一個python程式，
   Prompt: 我有很多.vtt的檔案，需要在每一個檔案裏面最後一行，加上一段指定的文字'10:00:25.266 --> 10:02:28.666'，要怎麼處理比較快?

        # -*- coding: utf-8 -*-
        import os
        
        # 指定要處理的目錄 以下是範例，請自行修改
        directory = r'C:\Users\YourName\Documents\eLearning\media\video'
        
        # 指定要添加的文字 以下是範例，請自行決定
        text_to_add = '10:00:25.266 --> 10:02:28.666'
        
        # 遍歷目錄中所有的 .vtt 文件
        for filename in os.listdir(directory):
            if filename.endswith('.vtt'):
                filepath = os.path.join(directory, filename)
        
                # 讀取文件內容到列表
                with open(filepath, 'r', encoding='utf-8') as file:
                    lines = file.readlines()
        
                # 在最後一行添加指定文字
                lines.append(f'\n{text_to_add}')
                
                # 將修改後的內容寫回文件
                with open(filepath, 'w', encoding='utf-8') as file:
                    file.writelines(lines)
                
                print(f'已處理文件: {filename}')

跑完這支python程式之後所有該資料夾裡的VTT檔案就都預先處理好了。

原本的程式我在這裡遇到問題:
directory = 'C:\Users\YourName\Documents\eLearning\'
^
SyntaxError: unterminated string literal (detected at line 5)

跟Claude抱怨一下後: 還是有錯誤

就得到解答如下: ^_^

這個錯誤是由於Python中的字符串使用了反斜線 `\`  作為轉義字符,因此在Windows路徑中的反斜線會被視為轉義符,導致語法錯誤。

有幾種解決方案:

1. 使用原始字符串
在路徑字符串前加 `r` 將其轉換為原始字符串,這樣反斜線就不會被當作轉義字符。

```python
directory = r'C:\Users\YourName\Documents\eLearning\'
```

2. 使用雙反斜線
在需要顯示反斜線的地方使用連續兩個反斜線。

```python
directory = 'C:\\Users\\YourName\\Documents\\eLearning\\'
```

3. 使用正斜線
在某些情況下,Python也可以接受正斜線作為路徑分隔符。

```python 
directory = 'C:/Users/YourName/Documents/eLearning/'
```

我建議使用第一種方式,在路徑字符串前加 `r` 會更加簡潔和可讀性更好。修改後的代碼如下:

```python
import os

# 指定要處理的目錄
directory = r'C:\Users\YourName\Documents\eLearning'

# 指定要添加的文字 
text_to_add = '10:00:25.266 --> 10:02:28.666'

...
```

用上述任一種方式修改路徑字符串後,腳本應該就可以正常運行了。請嘗試一下,如果仍有其他問題,歡迎繼續提出。
