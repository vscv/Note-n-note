## Finder
### Finder 更改顯示字型

`設了半天重開Finder視窗後，為何預設的自型大小就消失了？`

參考：https://support.apple.com/zh-tw/guide/mac-help/mchldaafb302/13.0/mac/13.0

多層資料集設定：

父資料夾：選擇「顯示方式」->『打開顯示方式選項』
<p align="center"> <img width=200 src="https://user-images.githubusercontent.com/18000764/216751152-716752c4-f1ca-4db8-ae5d-3b71852718e9.png"> </p>

注意：只有父資料夾的第二個設定要勾選。

父資料夾：選擇「顯示方式」->『打開顯示方式選項』
<p align="center"> <img width=200 src="https://user-images.githubusercontent.com/18000764/216751174-d0c95fc0-3738-4b94-8c09-36947645dece.png"> </p>

注意：所有子資料集的第二個設定不要勾選。

* * *

## iTune 音樂

拷貝一份放本機中離線播放

MP3位置：/Users/NAME/Music/iTunes/2023_02_02_MP3

若是要整個資料夾MP3加入 播放列表

`直接拖曳 資料夾 到iTune的播放列表下變成列表，然後可以建立列表資料夾，再把多個列表移動到列表資料夾中。`


* * *

## MacOS 郵件預覽/網頁瀏覽器，某些頁面的中文顯示問號框框

* 若是在mail2000郵件，選擇純文字模式，就正常顯示中文。
* 此外，用右鍵選取時prompt可以看到文字，或是右鍵拷貝貼上任何編輯器也可正常顯示中文。

![Pasted Graphic 3](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/673abcc2-8b9f-4165-a05c-c304e5bfa09c)

* 應該是字型問題：開啟『字體簿』查看字型，有一個字體就是問號框框。把他停用之後，郵件、chrome網頁的問號框框就變成文字了。

![Pasted Graphic 1](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/e9a1aff1-7bb7-4fb0-87d2-83cea7237394)

* 移除字體：進入『所有字體』，有移除選項，移除後若還存在，請重開字體簿重整。
* 重置字體：再去設定，進階，『重置字體』。發現.last resort並不是預設字體，所以不會重新下載。
* 
![Pasted Graphic 4](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/cfd7f8de-ba34-4f19-a28c-89cbab2b244d)


* * *

## Finder自製新增檔案工具與icns製作

![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/f03f7fc4-e31b-43ea-a5a3-8b162495387b)
### 開啟工序編輯
新增檔案，AppleScript處填入`tell application "Finder" to make new file at (the target of the front window) as alias`
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/bfb0e753-b902-4cf8-a1b3-93929806c1d4)

### 存檔選應用程式
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/e3b87508-fd4d-472b-ab51-d31a63b97729)

### 預設的icon是scirpt圖示
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/67fd1bb5-d3fd-4a75-b32a-64a6f9f1ce6f)
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/98472d37-343d-473c-8ed1-972b9d824507)

### 加入Finder工具列
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/068f53c7-e670-41a5-a4ab-f71d16b742c8)
這時候把`Finder新增檔案.app`拖曳到Finder即可。
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/2b12e1f4-c402-4037-8814-ab68d9986ad1)


### icns製作

用Inkscape製作一個1024x1024的png圖片，或是下載現成的SVG或png來改都可以。這裡是用https://upload.wikimedia.org/wikipedia/commons/3/31/U%2B1F02A_MJbaida.svg來放大。
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/45dae621-50cc-4860-bdfe-d1bf94989e58)

#### 開啟終端機指令： 資料夾名稱不能改要同範例一致

```shell
mkdir icons.iconset

sips -z 16 16 icon.png -o icons.iconset/icon_16x16.png
sips -z 32 32 icon.png -o icons.iconset/icon_16x16@2x.png
sips -z 32 32 icon.png -o icons.iconset/icon_32x32.png
sips -z 64 64 icon.png -o icons.iconset/icon_32x32@2x.png
sips -z 128 128 icon.png -o icons.iconseticon_128x128.png
sips -z 256 256 icon.png -o icons.iconset/icon_128x128@2x.png
sips -z 256 256 icon.png -o icons.iconset/icon_256x256.png
sips -z 512 512 icon.png -o icons.iconset/icon_256x256@2x.png
sips -z 512 512 icon.png -o icons.iconset/icon_512x512.png
sips -z 1024 1024 icon.png -o icons.iconseticon_512x512@2x.png

iconutil -c icns icons.iconset -o icon.icns
```

#### 取代原本app/Contents/Resources/applet.icns： 

```shell
▶ cp -rf icon.icns ../Finder新增檔案.app/Contents/Resources/applet.icns 
```
不要急，icns不會馬上更新要等一會。
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/e9ec97bd-9d13-46c0-b205-8f993ef2d636)
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/0b0cb56e-c901-4c32-9a28-1da25e7eca30)


若要換成新icns，一樣自訂工具列，把舊的拉下，新的拖曳上去就好了。
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/073ed15a-32c6-4862-ac70-659375bc3122)

### 範例檔案

[icns](temp_imgs/icon_BaiDa_百搭.icns)

[Finder新增檔案.app](temp_imgs/Finder新增檔案.app)

* * *

#### A script runs in a subshell. Changing a directory in a subshell doesn't propagate to the parent shell.
#### https://askubuntu.com/questions/481715/why-doesnt-cd-work-in-a-shell-script

`直接在shell script中放cd路徑的問題`

```bash
#!/bin/sh
cd 2023_11_13_Segment_kidney_blood_vessel/notebooks/;
```


* * *
# Jupyter Notebook 註冊\更換新的kernal核心
注意！這個範例改成jupyter notebook版本了，用於用matplotlib繪圖。

步驟三：在 Jupyter 中註冊虛擬環境 Kernel (核心)

```
▶ pip install ipykernel  (補裝)
python -m ipykernel install --user --name=my_project_venv --display-name="My Project Venv (Python 3.x)"
•  --user: 將 Kernel 安裝到當前用戶的配置中。
•  --name: 這是給 Kernel 檔案取的名稱（例如：my_project_venv）。
•  --display-name: 這是您在 Jupyter Notebook 介面中看到的名稱（例如：My Project Venv (Python 3.x)）。

▶ python -m ipykernel install --user --name=vQuantum312 --display-name="vQuantum312-venv (Python 3.12)"
Installed kernelspec vQuantum312 in /Users/PATH/Library/Jupyter/kernels/vquantum312  
💡 快速檢查
您可以使用這個指令來查看您的電腦上目前註冊了哪些 Jupyter Kernels：
▶ jupyter kernelspec list
Available kernels:
  python3        /usr/local/lib/python3.9/site-packages/ipykernel/resources
  vquantum312    /Users/PATH/Library/Jupyter/kernels/vquantum312
  vtf25          /Users/PATH/Library/Jupyter/kernels/vtf25
  vvino          /Users/PATH/Library/Jupyter/kernels/vvino
  vtf220         /usr/local/share/jupyter/kernels/vtf220
  vtf230         /usr/local/share/jupyter/kernels/vtf230      
vquantum312    使用環境ＯＫ！
```



* * *

## 從PDF複製文字貼上word後的字型不一致，且無法修改的問題

<img width="3790" height="1132" alt="image" src="https://github.com/user-attachments/assets/84c69f55-3bef-4c91-8ba8-97e135d61ae8" />

`這也是vibe coding的好例子，經歷多次回饋，探索不同方向，引導LLM趨向你認知的解決方案，落入大量廢碼的黑洞。`


#### 「重新打字」式 PDF 文字清理器（macOS）
目標：輸出「100% 標準 Unicode 中文」文字，確保貼到 Word 可正常套用標楷體（DFKai-SB）


方法：
1. 僅保留「安全字元」：標準中文字 (U+4E00–U+9FFF) + 常用標點/數字/英文字母
2. 移除所有相容漢字（U+FAxx）、私用區（U+E000+）、控制字元
3. 重建純文字，效果等同「人工重新輸入」


使用流程：`直接在剪貼簿中處理`
1. 從 PDF 複製有問題的文字（⌘+C）
2. 執行： py retype_pdf_text.py
3. 腳本會：
  顯示原始文字片段（用 repr 可見隱藏字元）
  顯示重建後文字
  自動複製乾淨文字到剪貼簿
4. 到 Word 貼上（⌘+V）

🔍 為什麼這個方法有效？
完全避開相容漢字（U+FAxx）：因它們不在 0x4E00–0x9FFF 範圍內
移除私用區（PUA）字元：常見於掃描 PDF 的 OCR 錯誤
只保留「系統一定能用標楷體顯示」的字元
效果 ≈ 你手動重新打字，但 100% 自動化

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
終極 PDF 文字清理器（macOS）— 繁體中文優化版
1. 使用 NFKC 將相容漢字轉為標準字
2. 保留/還原全形中文標點（，。；：「」等）
3. 移除有害控制字元
"""

import sys
import subprocess
import unicodedata
import re

# 半形 → 全形 標點映射（常見於繁體中文）
HALFWIDTH_TO_FULLWIDTH_PUNCT = {
    ',': '，',   # U+002C → U+FF0C
    '.': '。',   # U+002E → U+3002
    ';': '；',   # U+003B → U+FF1B
    ':': '：',   # U+003A → U+FF1A
    '?': '？',   # U+003F → U+FF1F
    '!': '！',   # U+0021 → U+FF01
#    '"': '「',   # 可選：但需注意開閉引號
#    "'": '’',    # 或保留原樣（引號較複雜）
#    '(': '（',   # U+0028 → U+FF08
#    ')': '）',   # U+0029 → U+FF09
}

def clean_text(text: str) -> str:
    # 1. NFKC 正規化（解決相容漢字問題）
    text = unicodedata.normalize('NFKC', text)
    
    # 2. 還原全形標點（僅在「中文語境」下安全替換）
    # 注意：只替換「前後是中文字或空白」的標點，避免誤改英文句子
    # 對每個標點分別處理
    for half, full in HALFWIDTH_TO_FULLWIDTH_PUNCT.items():
        # 使用 lookahead/lookbehind 保留上下文
        pattern = f'(?<!\\w){re.escape(half)}(?!\\w)'
        text = re.sub(pattern, full, text)

    # 3. 移除有害控制字元
    hidden_chars = [
        '\u200B', '\u200C', '\u200D', '\u200E', '\u200F',
        '\uFEFF', '\uFFFD', '\u2028', '\u2029', '\u00AD',
        '\u0000', '\u0001', '\u0002', '\u0003', '\u0004'
    ]
    pattern = '[' + re.escape(''.join(hidden_chars)) + ']'
    text = re.sub(pattern, '', text)
    
    # 4. 清理多餘空白（但保留段落）
    lines = [line.strip() for line in text.splitlines()]
    result = []
    prev_empty = False
    for line in lines:
        if line:
            # 壓縮行內多個空格為單一空格
            line = re.sub(r' +', ' ', line)
            result.append(line)
            prev_empty = False
        else:
            if not prev_empty:
                result.append('')
                prev_empty = True
    return '\n'.join(result)

def main():
    try:
        # 讀取剪貼簿
        raw = subprocess.run(['pbpaste'], capture_output=True, text=True, encoding='utf-8').stdout
        cleaned = clean_text(raw)
        # 寫回剪貼簿
        proc = subprocess.Popen(['pbcopy'], stdin=subprocess.PIPE, text=True, encoding='utf-8')
        proc.communicate(input=cleaned)
        print("✅ 已清理文字並保留全形中文標點！")
        print("→ 請貼到 Word 並使用「DFKai-SB」字型")
        print(repr(cleaned))
    except Exception as e:
        print(f"❌ 錯誤：{e}", file=sys.stderr)
        sys.exit(1)

if __name__ == '__main__':
    main()


```


* * *
# MacOS掃描報告 (使用 ClamAV 開源掃毒工具)



```bash
#安裝
brew install clamav

📌 安裝後會得到什麼

主要工具：

clamscan → 用來掃描檔案/目錄（就是你要的掃毒功能）。

clamd / clamdscan → 常駐掃描服務（需要另外設定）。

freshclam → 更新病毒碼（signature database）。


安裝完成後，可以確認版本：

clamscan --version
```


```
 👍
Homebrew 安裝的 ClamAV 在 macOS 上，預設並不會自動生成設定檔，所以你第一次執行 freshclam 會遇到：

ERROR: Can't open/parse the config file /usr/local/etc/clamav/freshclam.conf

🔧 解決方式
Step 1. 建立設定檔

Homebrew 安裝完 ClamAV 之後，通常會提供範例設定檔在：

/usr/local/etc/clamav/freshclam.conf.sample
/usr/local/etc/clamav/clamd.conf.sample


你需要把它複製成正式設定檔：

sudo cp /usr/local/etc/clamav/freshclam.conf.sample /usr/local/etc/clamav/freshclam.conf
sudo cp /usr/local/etc/clamav/clamd.conf.sample /usr/local/etc/clamav/clamd.conf


Step 2. 編輯 freshclam.conf

用編輯器打開：

sudo nano /usr/local/etc/clamav/freshclam.conf


找到這一行：

Example


👉 刪掉或註解掉（在前面加 #），否則 freshclam 會拒絕執行。

可選修改（推薦）：

設定更新伺服器（通常預設就夠）：

DatabaseMirror database.clamav.net


設定日誌檔位置：

UpdateLogFile /usr/local/var/log/clamav/freshclam.log

Step 3. 建立日誌目錄（如果需要）
sudo mkdir -p /usr/local/var/log/clamav
sudo touch /usr/local/var/log/clamav/freshclam.log
sudo chmod 644 /usr/local/var/log/clamav/freshclam.log

🔧 解法
方法 1. 把 log 檔權限調整給 clamav

先確認有沒有 clamav 使用者：

id clamav


如果有的話，直接把 log 檔給它用：

sudo chown clamav:clamav /usr/local/var/log/clamav/freshclam.log
sudo chmod 644 /usr/local/var/log/clamav/freshclam.log


目錄的權限也是給clamav使用者可以用
sudo chown -R clamav:clamav /usr/local/var/lib/clamav




📌 第一次使用注意事項

🥈 Step 2. 更新病毒碼資料庫

ClamAV 必須更新病毒特徵庫才有意義。第一次更新可能會花幾分鐘。

方法 A（推薦）
sudo freshclam



執行測試掃描（先不要全機，測試一下）：
先在一個小範圍（例如下載資料夾）測試：
clamscan /Users/$(whoami)/Downloads


300GB的硬碟用了兩天才掃完！
```


```
sh run_cav.sh
```

#### run_cav.sh
```bash
#!/bin/bash

# ================================
# macOS ClamAV Full System Scanner
# ================================

# 取得目前使用者名稱
USER_NAME=$(whoami)

# 建立桌面報告檔案名稱（含日期時間）
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")
REPORT_FILE="/Users/$USER_NAME/Desktop/clamav_scan_report_$TIMESTAMP.log"

echo "======================================="
echo "   🚀 ClamAV Full System Scan Started   "
echo "======================================="
echo "Report will be saved to: $REPORT_FILE"
echo ""

# 執行 ClamAV 掃描
sudo clamscan -r / \
  --exclude-dir="^/Volumes" \
  --exclude-dir="^/System" \
  --exclude-dir="^/dev" \
  --exclude-dir="^/private/tmp" \
  --exclude-dir="^/private/var/folders" \
  --exclude-dir="^/cores" \
  --exclude-dir="/Users/lsw/Library/CloudStorage/" \
  --bell \
  -v \
  --log="$REPORT_FILE" \
  --infected \
  --recursive
  

echo ""
echo "======================================="
echo "   ✅ Scan Completed!"
echo "   📄 Report saved at: $REPORT_FILE"
echo "======================================="

```



* * *
* * *
