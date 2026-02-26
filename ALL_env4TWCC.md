---

# Slurm 排程主機 🛫🛫🛫

這個新主機只能用排程跑GPU

<img width="400" height="346" alt="image" src="https://github.com/user-attachments/assets/95ad45e3-1506-482e-a85f-3d9ca01e2738" />
<img width="400" height="404" alt="image" src="https://github.com/user-attachments/assets/3a82749a-22b3-44fd-8305-8d07c14dfaf5" />

## 排程
slurm + miniconda + jupyter



## 登入方式 
#### (⚠️⚠️ 這是登入節點，雖然配了兩卡但不是用做長計算！且卡不是獨佔使用！同時可能有其他使用者佔用！！⚠️⚠️)
1. SSH登入
     - 選3. Email OTP
     - 輸入主機密碼
     - 收OTP
     - 輸入OTP後登入

2. 登入ThinLinc 互動式繪圖節點和操作範例說明 
- 安裝 [ThinLinc](https://www.cendio.com/thinlinc/download/)
- 使用ThinLinc 登入 n5
  - 注意！Password： 3 (輸入1~3。選擇2FA的其中一種方式登入) 用選擇 3. Email OTP
  - 另外跳出一個password視窗，輸入主機密碼
  - 等待Email發送OTP密碼後填入
  - 進入互動式節點桌面
    - 2 * NVIDIA H100 PCIe, 80GB 🔋 
 

## 檔案傳輸
  1. 傳輸節點FileZila可以，選交談式登入，一樣選3 mail OTP。`賽博鴨不行會鬼打牆`
  2. sftp [指令範本](https://man.twcc.ai/@AI-Pilot/SkDyJN4Gkl) 比較方便直接從另一台主機傳到新主機上，且主機間是內網比中轉快非常多。



---
# SFTP
SFTP (Secure File Transfer Protocol) 是一種基於 SSH 安全加密的檔案傳輸工具。常用指令包括連線 sftp user@host、下載 get、上傳 put、顯示遠端/本地目錄 ls/lls、切換目錄 cd/lcd 以及退出 quit。SFTP 具有互動性，能安全地在本地與遠端伺服器間管理檔案。 

以下是常用的 SFTP 指令整理：

1. 建立連線 
```基礎連線： sftp username@hostname
指定連接埠 (Port)： sftp -P port username@hostname (注意大寫 -P) 
```
3. 檔案與目錄操作 (遠端伺服器)
```
列出檔案： ls
顯示當前目錄： pwd
切換目錄： cd <directory>
建立目錄： mkdir <directory>
刪除檔案： rm <file>
刪除空目錄： rmdir <directory>
重新命名： rename <oldname> <newname> 
```
5. 上傳與下載 (檔案傳輸)
```下載檔案： get <remote_file> (下載至本機目前目錄)
下載資料夾： get -r <remote_folder>
上傳檔案： put <local_file> (上傳至遠端目前目錄)
上傳資料夾： put -r <local_folder>
```

6. 本地操作 (Local)
```在指令前加上 l (local) 可對本地主機進行操作：
列出本地檔案： lls
切換本地目錄： lcd <directory>
顯示本地當前目錄： lpwd
在本地執行指令： ! <command> (例如 !ls 顯示本地文件列表)
```

7. 結束連線
```離開： quit 或 exit 
操作提示：
在 sftp> 提示符下輸入 ? 或 help 可列出所有可用指令。
若要保存修改時間和檔案權限，上傳時可使用 put -p。
```

---
# ssh timeout 時間

如果是因為 shell 自動登出（非 SSH），可以在使用者的 shell 設定檔中調整 TMOUT：
編輯 ~/.bashrc 或 ~/.bash_profile：

export TMOUT=1800  # 單位是秒，這裡是 30 分鐘

`某些系統可能在/etc/profile檔案中設定了readonly TMOUT，這會阻止使用者修改TMOUT的值。如果遇到-bash: TMOUT: readonly variable的錯誤，您需要將readonly TMOUT這一行註解掉。`


---
在 VS Code 中整合 `sbatch` 啟動的 Jupyter Notebook 非常方便，因為 VS Code 內建了強大的**自動埠號轉發（Port Forwarding）**功能。

以下是具體的實作步驟，讓您不需要手動輸入複雜的 SSH 指令：

---

## 步驟 1：使用 VS Code 連線至 Nano 5

1. 在本地電腦安裝 **Remote - SSH** 擴充功能。
2. 點擊左下角的藍色圖示 `><`，選擇 **Connect to Host...**。
3. 輸入 `your_id@nano5.nchc.org.tw` 並完成登入。

## 步驟 2：提交 sbatch 腳本

在 VS Code 內建的終端機（Terminal）中，提交您的 Jupyter 啟動腳本：

```bash
sbatch submit_jupyter.sh

```

提交後，請使用 `tail -f jupyter_作業編號.log` 觀察日誌，直到看到 `http://127.0.0.1:8888/tree?token=...` 的訊息出現。

## 步驟 3：手動設定埠號轉發 (Port Forwarding)

由於 `sbatch` 是在計算節點（如 `nano5-compute01`）執行，VS Code 有時無法自動偵測到跨節點的埠號，這時請手動設定：

1. 在 VS Code 下方狀態列找到 **「埠號」(Ports)** 面板（如果沒看到，請對狀態列按右鍵勾選「Ports」）。
2. 點擊 **「新增埠號」(Forward a Port)**。
3. **關鍵點：** 在輸入框中直接輸入 `計算節點名稱:8888`（例如 `nano5-compute01:8888`）。
4. VS Code 會自動建立隧道。此時「轉發位址」會顯示為 `http://localhost:8888`。

## 步驟 4：開啟瀏覽器或直接在 VS Code 使用

* **瀏覽器法：** 直接點擊「轉發位址」旁的地球圖示，瀏覽器會開啟 Jupyter 頁面，再貼上 Log 裡的 **Token** 即可。
* **VS Code 原生介面：** 您也可以在 VS Code 中開啟一個 `.ipynb` 檔案，點擊右上角的 **Select Kernel** -> **Existing Jupyter Server**，貼上完整的 URL（含 Token），這樣就能直接在 VS Code 漂亮的介面裡寫程式，還能享有 IntelliSense 代碼補全。

---

### 💡 為什麼要這樣做？

* **免記指令：** 您不需要在本地端開好幾個終端機視窗下 `ssh -L` 指令。
* **斷線重連：** 即使您的本地網路不穩斷線，只要重新連上 VS Code，它會自動幫您重新掛載這些隧道，實驗不會中斷。

**需要我幫您檢查您的 `sbatch` 腳本中，啟動 Jupyter 的參數是否與 VS Code 完美相容嗎？**


---
## install_env.sh
* 2021


      sudo apt update;

      # for jupyter output pdf need >100GB dpkg.
      #sudo apt install -y pandoc;
      #sudo apt install -y xelatex;
      #sudo apt install -y texlive-xetex texlive-fonts-recommended texlive-generic-recommended;

      # for TWCC env.
      sudo apt-get install -y libsm6 libxrender1 libxext-dev tree unrar imagemagick graphviz;
      sudo pip3 install visual-logging ipyplot tf-explain tensorflow-addons tensorboard_plugin_profile seaborn scikit-learn scikit-image;
      sudo pip3 install -q "tqdm>=4.36.1";
      sudo pip3 install jupyternotify;
      sudo pip3 install pydot;
      sudo pip3 install albumentations augraphy;
      sudo pip3 install paddlepaddle paddleocr;


* 2023


      # 2020-11-30 created
      # 2021-02-24 updated
      # 2021-12-27 updated
      # 2023-10-12 added psmisc # for 'fuser' the GPU clear!
      # 2023-11-15 added tensorflow-io for decode tiff.
      
      # tricks for TWCC
      # mute the AMP woring
      echo "+ + + export var"
      export TF_ENABLE_AUTO_MIXED_PRECISION=0
      # V100 oom with efnetB7 if bs>4, nor sure its help or not.
      export TF_FORCE_GPU_ALLOW_GROWTH=1
      # cache tf hub models
      export TFHUB_CACHE_DIR=~/tfhub_modules_cache #to cache the hub model. /tmp/tfhub_modules
      
      #timer
      date +"%Y-%m-%d %H:%M:%S"
      
      
      #APT
      #echo "+ + + apt update no -qq"
      #sudo apt update;
      echo "+ + + apt update -qq"
      sudo apt update -qq;
      
      # for 'fuser' the GPU clear!
      sudo apt install psmisc -y -qq;
      
      # for jupyter output pdf need >100GB dpkg.
      # sudo apt install -y pandoc;
      # sudo apt install -y xelatex;
      # sudo apt install -y texlive-xetex texlive-fonts-recommended texlive-generic-recommended;
      
      
      # for TWCC env.
      #echo "+ + + apt install no -qq"
      #sudo apt install -y libgl1-mesa-glx;
      #sudo apt install -y libsm6 libxrender1 libxext-dev tree unrar imagemagick graphviz;
      echo "+ + + apt install -qq"
      sudo apt install -y libgl1-mesa-glx -qq;
      sudo apt install -y libsm6 libxrender1 libxext-dev tree unrar imagemagick graphviz -qq;
      
      
      # for maskrcnn
      sudo apt-get -y install protobuf-compiler -qq; #for maskrcnn/tf od api
      sudo pip3 install -q git+https://github.com/NVIDIA/dllogger.git
      
      
      # PIP
      echo "+ + + pip install -q 1"
      sudo pip3 install -q visual-logging ipyplot tf-explain tensorflow-addons tensorboard_plugin_profile seaborn scikit-learn scikit-image;
      echo "+ + + pip install -q 2"
      sudo pip3 install -q "tqdm>=4.36.1";
      sudo pip3 install -q jupyternotify;
      sudo pip3 install -q pydot;
      echo "+ + + pip install -q 3"
      sudo pip3 install -q albumentations;
      sudo pip3 install -q pytictoc;
      sudo pip3 install -q pycocotools;
      sudo pip3 install -q pytesseract;
      sudo pip install -q tensorflow-io;
      
      
      # for turn off warning of "TF_ENABLE_AUTO_MIXED_PRECISION has no effect."
      # export TF_ENABLE_AUTO_MIXED_PRECISION=0
      
      
      date +"%Y-%m-%d %H:%M:%S"


##### dpkg: debian package. 


***
## FLIR Science File SDK 

用來開啟紅外線熱影像SEQ錄影與R.JPG影像檔案。

```bash
FLIR Science File SDK 
https://github.com/gcathelain/thermalcognition/tree/master/FLIR%20Science%20File%20SDK

FLIR Science File SDK for Python
https://flir.custhelp.com/app/answers/detail/a_id/3504/~/getting-started-with-flir-science-file-sdk-for-python

Verifying archive integrity...  100%   All good.
Uncompressing FLIR Science File SDK  100%  
must be run as root

(1) 直接開pytorch-23.11-py3:latest容器
$cd 2023_08_14_FLIR_SDK/
$ sudo sh FLIRScienceFileSDK-4.1.0+26-linux-x86_64.run (自下載的ZIP檔解開)

Verifying archive integrity...  100%   All good.
Uncompressing FLIR Science File SDK  100%  
Installing FLIR Science File SDK to /opt/flir/sdks/file
Add Desktop Entry [Yn]?Y

Creating /usr/share/applications/flir-filesdk.desktop
Creating /etc/profile.d/flir_filesdk.sh for FLIRFILESDKDIR environment variable.
You may need to reboot to apply this change.

(2)
編譯給Python使用
#安裝套件
$sudo pip install setuptools cython wheel
#後面會缺ffprobe
$sudo pip install opencv-python ffprobe-python sk-video scikit-image spacy pytesseract ffmpeg tree libimage-exiftool-perl

$ sudo apt install ffmpeg #後面若要存新影片才要
$ sudo apt install libimage-exiftool-perl #後面要開FLIR JPG才要


(3)
#把SDK安裝成py套件
$cd /opt/flir/sdks/file/python
python setup.py install --shadow-dir /path/to/temp/folder/filesdk
注意：/path/to/temp/folder/filesdk是你本機有寫入權限的地方
注意：setup.py位置在 /opt/flir/sdks/file/python
$ sudo python setup.py install --shadow-dir /tmp/filesdk

“error: shadow-dir: /tmp/filesdk/ must not exist.”不要先去mkdir資料夾，他自己會產生！
有sudo才能安裝 
Installed /usr/local/lib/python3.10/dist-packages/FileSDK-4.1.0-py3.10-linux-x86_64.egg
Processing dependencies for FileSDK==4.1.0
Finished processing dependencies for FileSDK==4.1.0


(4) 測試fnv工具包
$cd to somewhere not the opt/flir/sdks/file/python
$python3
Python 3.10.6 (main, May 29 2023, 11:10:38) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import fnv
>>> OK! If no any error!

(5) 重啟jupyter-notebook載入新安裝的套件

(6) 安裝thermalcognition包含API使用範例
# demo jupyter
git clone https://github.com/gcathelain/thermalcognition

(7) 修bug
AttributeError: module 'cv2.dnn' has no attribute 'DictValue'

comment out line 169 like so
#  LayerId = cv2.dnn.DictValue

$sudo vim /usr/local/lib/python3.10/dist-packages/cv2/typing/__init__.py
#  LayerId = cv2.dnn.DictValue (OK!)

```

***

# 手動可以遠端使用的方法

# TensorBoard in notebooks
https://www.tensorflow.org/tensorboard/tensorboard_in_notebooks

只能用在local機器上，CCS因為port有重新導向，在CCS用cmd下tensorboard --host 0.0.0.0 --port 5000 再用網頁開:CCS對外IP:對外PORT即可！

CCS
$tensorboard --port 5000 --logdir xxxxx/fit/20241025-131659

your browser


***
### 基本linux檔案空間指令

ref : https://www.dreamjtech.com/content/31

磁碟空間： df -h (容器主機裡顯示的是整體共享磁區，所以你/home/xxxxx顯示的不是喔！)

du: 計算目錄所使用的空間

指令 du -h –max-depth=1

–max-depth是表示查詢子目錄的層級

sort: 將輸入的資料排序
head: 將輸入資料的最開頭幾行資料輸出

找出最大的檔案：

du -a /path_to_u | sort -n -r | head -n 5




 利用 find指令如

 find / –type f –size +5G

 我們可以利用此種方式找出大於5G 的檔案

 



***


***
http://203.145.216.xxx:58425/ 

