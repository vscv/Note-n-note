---

# Slurm 排程主機 🛫🛫🛫
這個新主機只能用排程跑，對非HPC科學計算的開發不方便。
嘗試兩個方法
1. slurm + miniconda + jupyter

<img width="400" height="346" alt="image" src="https://github.com/user-attachments/assets/95ad45e3-1506-482e-a85f-3d9ca01e2738" />


2. 登入ThinLinc 互動式繪圖節點和操作範例說明

<img width="400" height="404" alt="image" src="https://github.com/user-attachments/assets/3a82749a-22b3-44fd-8305-8d07c14dfaf5" />

- 安裝 [ThinLinc](https://www.cendio.com/thinlinc/download/)
- 使用ThinLinc 登入 n5
  - 注意！Password： 3 (輸入1~3。選擇2FA的其中一種方式登入) 用選擇 3. Email OTP
  - 另外跳出一個password視窗，輸入主機密碼
  - 等待Email發送OTP密碼後填入
    - 進入互動式節點桌面
    - 2 * NVIDIA H100 PCIe, 80GB 🔋 ⚠️⚠️不是獨佔使用！同時可能有其他使用者佔用！！⚠️⚠️
    - `只能固定使用兩張H100`，不能選H200或更多張卡
- 檔案傳輸
  1. 傳輸節點FileZila可以，選交談式登入，一樣選3 mail OTP。`賽博鴨不行會鬼打牆`
  2. sftp [指令範本](https://man.twcc.ai/@AI-Pilot/SkDyJN4Gkl) 比較方便直接從另一台主機傳到新主機上，且主機間是內網比中轉快非常多。



---
# ssh timeout 時間

如果是因為 shell 自動登出（非 SSH），可以在使用者的 shell 設定檔中調整 TMOUT：
編輯 ~/.bashrc 或 ~/.bash_profile：

export TMOUT=1800  # 單位是秒，這裡是 30 分鐘

`某些系統可能在/etc/profile檔案中設定了readonly TMOUT，這會阻止使用者修改TMOUT的值。如果遇到-bash: TMOUT: readonly variable的錯誤，您需要將readonly TMOUT這一行註解掉。`

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

