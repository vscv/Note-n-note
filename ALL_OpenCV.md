

* * *
# Note_OpenCV
A collection of sample code of OpenCV


## Text

```Python
#cv2.putText
font = cv2.FONT_HERSHEY_SIMPLEX
#org
org = (50, 50)
#fontScale
fontScale = 3
#Blue color in BGR
color = (255, 0, 0)
#Blue color in BGR
color_blue = (255, 0, 0)
# Line thickness of 2 px
thickness = 2

cv2.putText(img=img, text=txt, org=(int(x)+20, int(y)-20), fontFace=font, fontScale=2.0, color=color, thickness=thickness, lineType=cv2.LINE_AA)
```
* * *
## BGR2RGB
- BGR是OpenCV惱人的預設色彩讀取順序，原因是發展OpenCV當下時代盛行的色彩是BGR格式[1]，只是現在主流是RGB了。
- cv2讀取時預設為BGR，如果處理過程跟色彩有關會需要先轉回RGB(或是把你的處理過程用BGR格式)。
- cv2儲存時預設為RGB，即如果沒有做任何色彩處理的話，可直接存檔不需再轉換。

Example:

```Python
#Util function
def get_date():
    date= datetime.datetime.today()
    return date.strftime("%Y_%m_%d_%H%M")

#Show anns on the sample image
path="/PATH_TO/train/"
img =  cv2.imread(path + img_name) # If only for showing, BGR -> RGB , cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

#Check ann by cv2, comment if use cocoapi
for a in ann:
    #print(f'[A box]: {a}')
    category_id = a['category_id']
    txt = 'id:' + str(category_id)
    x,y,w,h = a['bbox']
    cv2.rectangle(img, (int(x), int(y)), (int(x+w), int(y+h)), (255, 255, 0), 5)
    cv2.putText(img=img, text=txt, org=(int(x)+20, int(y)-20), fontFace=font, fontScale=2.0, color=color, thickness=thickness, lineType=cv2.LINE_AA)

fig = plt.figure()
plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB)); plt.axis('off') #must after the every cv2 task

#Show ann by cocoapi, comment if use cv2
coco.showAnns(ann, draw_bbox=True)


#cannot save the ann on top of image, only save the original img
cv2.imwrite(f'test_patchs/show_ann_{img_name}_cv2_{timestamp}.jpg', img) #imwrite do BGR2RGB for you
#bcs it plot object, we should apply plt.save instead
timestamp = get_date()
# plt.savefig(f'test_patchs/show_ann_with_image_{img_name}_{timestamp}.jpg', bbox_inches='tight', pad_inches=0)
plt.savefig(f'test_patchs/show_ann_{img_name}_plt_{timestamp}.jpg', bbox_inches='tight', pad_inches=0)
```

[1] 來源請求

* * *
## 依機率選擇使用某個函數
* 這在深度學習的數據集增強中常會用到，增強套件裡一定有幫你寫好這個功能，但總有例外不能直上的時候。
之前有寫過，但懶得找了。因此重新寫在這裡...

* oneof

多個函數中選擇一個，有時候也會配上p。如果需求是每次必選一個則p=1，最簡單就是用random.choices(list)來選

```Python
def oneof():
    list = [f1, f2, f3, f4, ]
    return random.choices(list)

aug_f = oneof()
output = aug_f(image) 
```
    
TypeError: 'list' object is not callable： 如果list裡是一堆函數f1() f2()，返回使用時記得加上[0]。

```Python
def oneof():
    list = [f1, f2, f3, f4, ]
    return random.choices(list)[0]

aug_f = oneof()
output = aug_f(image) 
```
    或是

```Python
def oneof():
    list = [f1, f2, f3, f4, ]
    return random.choices(list)

aug_f = oneof()
output = aug_f[0](image) 
```    
    
ps.random.choices的weights參數是可以設定個別元素被選到的機率，但總和為100%。若要給一個是否會執行oneof的機率，就在預先產生一個p值在判斷要不要執行。

##### 假設80%機率才執行oneof時
```Python
p = random.uniform(0, 1)
if p >= 0.8:
    do oneof()
```

* p `執行該函數的機率`

* * * 
### 舊版Tensorflow範例 讀取影像耗時問題
`當初沒考量reshape對於大圖會非常耗時，改用numpy處理即可解決。`

```Python
def load_image_into_numpy_array(pil_image):
    (im_width, im_height) = pil_image.size
    data = pil_image.getdata()

    data_array = np.array(data)

    return data_array.reshape((im_height, im_width, 3)).astype(np.uint8)
```
`改成不要reshape了`
```Python
def load_image_into_numpy_array(path):
    """Load an image from file into a numpy array.

    Puts image into numpy array to feed into tensorflow graph.
    Note that by convention we put it into a numpy array with shape
    (height, width, channels), where channels=3 for RGB.

    Args:
    path: a file path (this can be local or on colossus)

    Returns:
    uint8 numpy array with shape (img_height, img_width, 3)
    """
    img_data = tf.io.gfile.GFile(path, 'rb').read()
    image = Image.open(BytesIO(img_data))

    return np.array(image)
```

`改成不要reshape了2`
```Python
def pil_image_to_numpy_array(pil_image):
    return np.asarray(pil_image)  
```



* * *
## OpenCV 舊版範例問題

`舊版cv2.findContours例如cv2 2.x回傳順序為hierarchy,contours，但4.x 5.x已經改成contours, hierarchy！`

```Python
def find_biggest_contour(image):

# Copy to prevent modification
image = image.copy()

_,contours  = cv2.findContours(image, cv2.RETR_LIST, cv2.CHAIN_APPROX_SIMPLE)

#get contour sizes and return the biggest contour
max_area = -1
for i in range(len(contours)):
    area = cv2.contourArea(contours[i])
    if area>max_area:
        biggest_contour = contours[i]
        max_area = area

#create an empty mask
mask = np.zeros(image.shape, np.uint8)

#draw the biggest contour on it
cv2.drawContours(mask, [biggest_contour], -1, 255, -1)

return mask
```
for cv2 4.x 5.x

`contours, hierarchy = cv2.findContours()`

* * *
## Cat-Dog 貓狗資料集EXIF錯誤
首先trace錯誤訊息 找到可能的原因 大部分應該找最源頭而不是最後的Error!例如看到這個會以為TF又出包了
        
        UnknownError: Graph execution error:

其實再往回看源頭，這才是原因。因為其EXIF損壞(可交換圖檔格式Exchangeable image file format是專門為數位相機的相片設定的檔案格式，可以記錄數位相片的屬性資訊和拍攝資料。)

        UserWarning: Possibly corrupt EXIF data.


三種方法：

1. PIL : 請先去除訓練集中損壞的圖片，例如PetImages/Cat/666.jpg, PetImages/Dog/11702.jpg。另請參閱     https://www.kaggle.com/code/sushovansaha9/cat-dog-classification-transferlearning-ipynb。

2. PIL ：刪除 EXIF 數據但保留圖像。

```Python
import piexif  
piexif.remove(filename)
```

3. tf.io：僅讀取圖像數據，因此不用刪除任何圖像或EXIF：
```Python
file = tf.io.read_file(filename)
image = tf.image.decode_jpeg(file)
```
* * *

* * *

* * *
## 繪出標註框修改字型與支援繁體中文
#### deploy/python/infer_LSW.py ---> visualize -> visualize_box_mask -> draw_box
* edukai-4.0.ttf 教育部標準楷書字形檔(Version 4.00)  https://language.moe.gov.tw/001/Upload/Files/site_content/M0001/edukai-4.0.zip
* 然後拷貝到系統預設字型路徑下
```Shell
sudo cp -rf deploy/python/font_local /usr/share/fonts/truetype/
```

### 在visualize.py中，增加字體並控制大小
* 中文標籤
```Python
#JCK 中文標籤顯示
labels_cht = [
                "寶特瓶",
                "塑膠瓶蓋",
                "其他飲料瓶與食物容器",
                "非食物的瓶罐與容器",
                "塑膠提袋",
                "食品包裝袋",
                "吸管",
                "外帶飲料杯",
                "免洗餐具",
                "鐵鋁罐",
                "鋁箔包或利樂包",
                "玻璃瓶",
                "釣魚用具",
                "漁業浮球/浮筒/漁船防碰墊",
                "漁網與繩子",
                "菸蒂",
                "牙刷",
                "針筒/針頭",
                "打火機",
                "其他",
            ]
```

* 設定楷體字型與大小

把中文標籤labels_cht[clsid]加入，再把font給予textsize, draw.text函數就好了。

```Python
from PIL import ImageFont
font = ImageFont.turepytpe("edukai-4.0.ttf", 80, encoding="utf-8")
```
```Python
#JCK ADD Font control
def imagedraw_textsize_c(draw, text, font):
    if int(PIL.__version__.split('.')[0]) < 10:
        #JCK yep, we use PIL '9.5.0' version
        tw, th = draw.textsize(text, font)
    else:
        left, top, right, bottom = draw.textbbox((0, 0), text)
        tw, th = right - left, bottom - top

    return tw, th
```
```Python
def draw_box(im, np_boxes, labels, threshold=0.5):
.
.
.
        # draw label
        text = "{} {:.4f} {}".format(labels[clsid], score, labels_cht[clsid])
        tw, th = imagedraw_textsize_c(draw, text, font)
        draw.rectangle(
            [(xmin + 1, ymin - th), (xmin + tw + 1, ymin)], fill=color)
        draw.text((xmin + 1, ymin - th), text, fill=(255, 255, 255), font=font)
```
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/913eac07-d849-4217-a238-2bbe3d92e15d)


* 強制設定seaborn繪圖顯示特定中文字型
```Python
from matplotlib.font_manager import fontManager, FontProperties
fontManager.addfont('/usr/share/fonts/truetype/font_local/edukai-4.0.ttf')
prop = FontProperties(fname='/usr/share/fonts/truetype/font_local/edukai-4.0.ttf')
sns.set(font=prop.get_name())
```

```Python
sns.histplot(dataset_df['單價'], );
```
![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/e63e0785-1743-4f2b-adf6-06f43a55cf78)


* 簡體
```python
import matplotlib.pyplot as plt
import seaborn as sns
plt.rc("font",family="SimHei",size="15")
```


* 設定matplotlib預設使用本地中文字型
https://github.com/ultralytics/ultralytics/issues/5803
https://dev.to/codemee/matplotlib-xian-shi-zhong-wen-4998

測試沒有正確中文字型時
```
import matplotlib.pyplot as plt
plt.pie(
 [800, 300, 400],
 labels=['交通', '娛樂', '教育'])
#字會變成框框
```


強制加入字型
```Shell
from matplotlib.font_manager import fontManager
fontManager.addfont('/home/u3/font_local/edukai-4.0.ttf')
```
檢查字型名稱
```
for i in sorted(fontManager.get_font_names()):
    print(i)
DejaVu Sans
DejaVu Sans Display
DejaVu Sans Mono
DejaVu Serif
DejaVu Serif Display
Liberation Mono
Liberation Sans
Liberation Sans Narrow
Liberation Serif
STIXGeneral
STIXNonUnicode
STIXSizeFiveSym
STIXSizeFourSym
STIXSizeOneSym
STIXSizeThreeSym
STIXSizeTwoSym
TW-MOE-Std-Kai
```

設定使用該字型
```
import matplotlib
matplotlib.rc('font', family='TW-MOE-Std-Kai', size=8)
```



* * *
## OpenCV與Numpy座標轉換

```Shell
    0/0---column--->
     |
     |
    row
     |
     |
     ˅
     (Numpy)
     
     
     0/0---X--->
     |
     |
     Y
     |
     |
     ˅
     (OpenCV)
```

```Python
#draw bounding box
# 左上、右下點座標
image = cv2.rectangle(image, (xmin,ymin), (xmax, ymax), color, border_thickness)

#crop box from image
# [str_row:end_row, star_col:end_col]
box_img = image[ymin:ymax,  xmin:xmax]

```


### 交換box_xy與np Row_Col位置

box: (3,2), (4,3)
arr: [1:3, 2:4] 

* [1:2] = 1,
* [2:4] = 2,3

```python
>>> arr = np.zeros((4,5))
>>> arr
array([[0., 0., 0., 0., 0.],
       [0., 0., 0., 0., 0.],
       [0., 0., 0., 0., 0.],
       [0., 0., 0., 0., 0.]])
>>> arr[1:3, 2:4] = 1
>>> arr
array([[0., 0., 0., 0., 0.],
       [0., 0., 1., 1., 0.],
       [0., 0., 1., 1., 0.],
       [0., 0., 0., 0., 0.]])
```

* * *
## Some bugs

```bash
    import cv2
  File "/usr/local/lib/python3.10/dist-packages/cv2/__init__.py", line 181, in <module>
    bootstrap()
  File "/usr/local/lib/python3.10/dist-packages/cv2/__init__.py", line 175, in bootstrap
    if __load_extra_py_code_for_module("cv2", submodule, DEBUG):
  File "/usr/local/lib/python3.10/dist-packages/cv2/__init__.py", line 28, in __load_extra_py_code_for_module
    py_module = importlib.import_module(module_name)
  File "/usr/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "/usr/local/lib/python3.10/dist-packages/cv2/typing/__init__.py", line 169, in <module>
    LayerId = cv2.dnn.DictValue
AttributeError: module 'cv2.dnn' has no attribute 'DictValue'
```

`do the same modification in the "/usr/local/lib/python3.10/dist-packages/cv2/typing/__init__.py" `

```python
#https://github.com/facebookresearch/nougat/issues/40
@Scikud I was able to fix it by removing line 169 from "/Users/apple/anaconda3/lib/python3.11/site-packages/cv2/typing/init.py" file, it seems to be the problem

comment out line 169 like so
#  LayerId = cv2.dnn.DictValue
```




* * *
## 影像的標準化 [Ｘ,Ｘ]-> [0, 1]
https://stackoverflow.com/questions/70783357/how-do-i-normalize-the-pixel-value-of-an-image-to-01
Hit:
* 對比標準化，僅使用單影像中像素值上下限計算，可以有較好的視覺呈現。但有拉伸或壓縮副作用。
* 資料標準化，使用資料型態的上下限計算，保留原值scale分佈。
* 
* ps.例如來自MR,CT, 醫學、科學的影像使用資料標準化時在視覺上通常會太暗或太亮，以腎臟HiP-CT為例，像素值落在22000~28500(int64)之間。需要可視化影像時可以使用對比標準化來凸顯腎臟與血管結構，但用於訓練模型時需要使用資料標準化以統一所有訓練、測試集，或是對資料集統計取得mean, std基準來做標準化會較有助於模型學習。


* * *
## CV2無法讀取mp4檢查print("cap:", cap.isOpened())都是False?
* 重複確認檔案絕對路徑
* 重新安裝ffmpeg
* 重新安裝opencv
  $sudo pip uninstall opencv-python
  $sudo pip uninstall python

  sudo pip uninstall openecv opencv-python
* $sudo pip install opencv-contrib-python

`如果是在TWCC PyTorch 24.02容器下發生的，可以改用Tensorflow 24.02版本的容器是可以的。
* * *

# 
# render the segmentation results every few frames for save time and space
vis_frame_stride = 5 #50 #1 # %05d 沒連號的ffmpeg不能轉 ffmpeg -f image2 -i %*.png out.avi WILL NOT WORK! 
讀檔還是用out_frame_idx（讀入固定的檔名），但是就把寫檔時的out_frame_idx（讀入固定的檔名）改成count填充，因為檔名也是`%05d`.jpg格式，可以直接換掉作用一樣。


```
def nas_draw_and_save_masklets_cv2(video_dir, frame_names, vis_frame_stride, out_mask_path, video_segments):
    
    count=0
    for out_frame_idx in tqdm(range(0, len(frame_names), vis_frame_stride)):
        """
        直接在 cv2 image 上畫出 mask + 輪廓 + obj_id 標籤文字。
        """

        image = cv2.imread(os.path.join(video_dir, frame_names[out_frame_idx]), cv2.IMREAD_UNCHANGED)
        image = cv2.cvtColor(image, cv2.COLOR_BGR2BGRA)  # 轉換成 BGRA 色彩模式

        # add frame number
        # font
        font = cv2.FONT_HERSHEY_SIMPLEX
        # org
        #org = (round(W/2)-100, round(H/8))
        org = (25, 25)

        # fontScale
        fontScale = 1.
        # Blue color in BGR
        color = (255, 255, 255)
        # Line thickness of 2 px
        thickness = 2
        # Using cv2.putText() method
        image = cv2.putText(image, f'frame {out_frame_idx}', org, font,
                           fontScale, color, thickness, cv2.LINE_AA)

        try :
            for out_obj_id, out_mask in video_segments[out_frame_idx].items():
                image = nas_draw_masklets_cv2(out_mask, image, obj_id=out_obj_id)
            cv2.imwrite(f"./{out_mask_path}/{count:05}.jpg", image)
        except Exception as e:
            cv2.imwrite(f"./{out_mask_path}/{count:05}.jpg", image)
            print(f'missing mask: {e}')
            
        count+=1

```


# FFmpeg 轉檔時只顯示進度 不顯示header、video等資訊

`frame=  270 fps=164 q=2.0 Lsize=N/A time=00:00:54.00 bitrate=N/A dup=0 drop=1340 speed=32.9x`
```
-loglevel quiet -stats

!ffmpeg -i {video_path} -q:v 2 -r {framerate} -start_number 0 {video_dir}/%05d.jpg -loglevel quiet -stats
```


# FFMPEG 影片解析度尺寸裁切

✅ 基本概念
使用的是 ffmpeg 的 crop 濾鏡：

`crop=寬度:高度:X座標:Y座標`

|參數 |說明|
|--|--|
|寬度| 要保留的畫面寬| 
|高度| 要保留的畫面高|
|X| 從左邊算起的像素位置|
|Y| 從上邊算起的像素位置|

✅ 最基本範例：裁剪固定尺寸
🔹 從左上角裁 640x480
```bash
ffmpeg -i input.mp4 -vf "crop=640:480:0:0" output.mp4
```

🔹 從指定位置裁剪（例：中間偏右下）
```bash
ffmpeg -i input.mp4 -vf "crop=640:480:100:50" output.mp4
```

✅ 裁剪「正中央」畫面（自動計算）
假設你要裁成 640x480，無論原影片多大：
```bash
ffmpeg -i input.mp4 -vf "crop=640:480:(in_w-640)/2:(in_h-480)/2" output.mp4
```
📌 in_w、in_h 是 ffmpeg 內建的原始影片寬高變數。


✅ 移除左右黑邊（例如 1920×1080 → 裁掉左右）
```
ffmpeg -i input.mp4 -vf "crop=1600:1080:160:0" output.mp4
```

✅ 只裁上半部（常用於監控或 AI 畫面）
```
ffmpeg -i input.mp4 -vf "crop=in_w:in_h/2:0:0" output.mp4
```

✅ 只裁下半部
```
ffmpeg -i input.mp4 -vf "crop=in_w:in_h/2:0:in_h/2" output.mp4
```

✅ 保留畫質（避免重編碼）
如果你裁剪後仍能相容編碼，可指定高品質或接近無損：
```
ffmpeg -i input.mp4 -vf "crop=640:480:0:0" -c:v libx264 -crf 18 -preset veryfast output.mp4
```

|參數 |建議值 |
|--|--|
|-crf |18（高品質）~ 23（一般）|
|-presetvery | fast / fast|




* * *
* * *
# Gradio
python -m pip install gradio

* gradio-4.33.0

AttributeError: module 'gradio' has no attribute 'inputs'   
`舊版的inputs沒支援了pip install gradio==3.50 ==3.43.1`

gradio deprecated gradio.inputs and gradio.ouputs. Use gradio.components for newer versions. See example:

```Python
import gradio as gr
# Component eg. Image, Label ...etc
outputs=gr.components.<Component>(...) 
inputs=gr.components.<Component>(...)
```

```Python
    PencilScribbles(
    ^^^^^^^^^^^^^^^
NameError: name 'PencilScribbles' is not defined
note  : PencilScribbles --> Scribbles, for augraphy="8.2.3" zytk-vm version.



size_range=(100, 800),  ->> scribbles_size_range=(100, 500), # must smaller than text_image


  File "/xxxx/2024_06_06__Gradio_DEMO_page_edge2MacOS/2-DCC_app.py", line 134
    model_path_20K-CAE = Path("xx/best_model/ir_512_20K-CAE/CAE-64-128_512x512_32_0050.xml")
    ^^^^^^^^^^^^^^^^^^
SyntaxError: cannot assign to expression here. Maybe you meant '==' instead of '='?

"這是因為20K'-'CAE對python來說是一個'減'號而不是符號。"

```

### Gradio_ImagSlider_
#### 兩張影像之滑動比對

1. https://github.com/pngwn/gradio-imageslider
   
```Python
# ImageSlider
from gradio_imageslider import ImageSlider
from PIL import ImageFilter

# a pass fun
def make_output_for_ImgSli(img1, img2):
    return (img1, img2)


with gr.Blocks(theme='freddyaboulton/dracula_revamped', title=" Document Enhancement") as demo:
    gr.Markdown(head_line)
    gr.Markdown(head_description)
    
    with gr.Row():
        inp = gr.components.Image(type="filepath", label="Input degraded doc.")
        out = gr.components.Image(type="pil", label="Output clean doc.")
    
    with gr.Row():
        btn_Reset = gr.ClearButton([inp,out,]) #gr.Button("Reset Input")
        btn_Clean = gr.Button("Clean Doc.")


    # Image Slider Compare #
    #with gr.Group():
    with gr.Column():
        # for dynaminc input "AFTER" processed the input and output images
        ImgSli = ImageSlider(
        label="Image slider comparing", type="pil", slider_color="pink",
        width=800, height=800,
        )
        
        # for static two image input
        #ImgSli = ImageSlider(value=[inp,out],
        #label="Blur image", type="pil", slider_color="pink",
        #width=512, height=512,
        #)
        
        #ImgSli.upload(fn, inputs=img1, outputs=img1)
        #ImgSli.input(inputs=img1, outputs=img1)
        
        with gr.Row():
            btn_Reset2 = gr.ClearButton([ImgSli,])
            btn_ImgSli = gr.Button("ImgSli")

    btn_Clean.click(fn=inference, inputs=[inp,ver], outputs=out)
    btn_ImgSli.click(fn=make_output_for_ImgSli, inputs=[inp,out], outputs=ImgSli)

if __name__ == "__main__":
    demo.launch(server_name="127.0.0.1",server_port=6666,)
```


![image](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/9b1b78d6-0e1e-420f-af80-4bb98ee16a07)

![螢幕錄影 2024-06-07 10 14 55 (1)](https://github.com/vscv/AllYouNeedToKnowAboutX/assets/18000764/ffbbbc77-b4d8-4f84-b9ef-b2bf4a3a2892)
   
2. https://imgsli.com/


* * *

# mptplotlib的中文字型
這裡有完整解法 注意 要照裡面全部 全部步驟

## 下載字型

edukai-4.0.ttf 教育部標準楷書字形檔(Version 4.00) https://language.moe.gov.tw/001/Upload/Files/site_content/M0001/edukai-4.0.zip 已失效

edukai-5.0.ttf 教育部標準楷書字形檔(Version 5.00) 13,084字 https://language.moe.gov.tw/001/Upload/Files/site_content/M0001/edukai-5.0.zip


教育部標準宋體 UN for Unicode版 24,034字  https://language.moe.gov.tw/001/Upload/Files/site_content/M0001/eduSong_Unicode.zip

## 方法一：拷貝字型到系統中
```
# 找到mptplotlib的字型位置
import matplotlib
print(matplotlib.matplotlib_fname())
```


```
#將你的中文字體拷貝進去
!cp font_local/edukai-4.0.ttf /home/your_DIR/.local/lib/python3.xx/site-packages/matplotlib/mpl-data/fonts/ttf
```

## 方法二：或是強制設定字型路徑
#### 設定MPL使用中文字型 (# MPL每次都要重新執行一次自行設定)
```
from matplotlib.font_manager import fontManager

fontManager.addfont('/home/your_dir/your_font_local/edukai-4.0.ttf')
```

```
# 檢查一下字型有沒有進去
for i in sorted(fontManager.get_font_names()):
    print(i)
```

```
DejaVu Sans
DejaVu Sans Display
DejaVu Sans Mono
DejaVu Serif
DejaVu Serif Display
STIXGeneral
STIXNonUnicode
STIXSizeFiveSym
STIXSizeFourSym
STIXSizeOneSym
STIXSizeThreeSym
STIXSizeTwoSym
TW-MOE-Std-Kai # 這個是教育部字型別名
cmb10
cmex10
cmmi10
cmr10
cmss10
cmsy10
cmtt10
```

```
# 測試

import matplotlib
matplotlib.rc('font', family='TW-MOE-Std-Kai', size=28)


import matplotlib.pyplot as plt
plt.pie(
 [800, 300, 400],
 labels=['交通', '娛樂', '教育'])
plt.title("中文測試")
plt.show()
```
![image](https://github.com/user-attachments/assets/b57dde90-6f10-4f6d-b3bd-f58a196de64b)




* * *
# mptplotlib的子圖填充

🔍 為什麼有白邊？
在 matplotlib 中，fig（figure）和 ax（axes）之間預設有留白（padding），方便放置標題、刻度、標籤等。imshow 雖然只顯示圖片，但這些預留空間仍然存在。

✅ 解釋 subplots_adjust(...) 參數：
這個函式會設定子圖 (ax) 在整個圖形 (fig) 中的位置和大小。

```python
fig.subplots_adjust(left=0, right=1, top=1, bottom=0)
參數	意義
left=0	子圖的左邊界貼齊整個 figure 的最左邊（0%）
right=1	子圖的右邊界貼齊整個 figure 的最右邊（100%）
top=1	子圖的上邊界貼齊整個 figure 的最上邊（100%）
bottom=0	子圖的下邊界貼齊整個 figure 的最下邊（0%）

這樣設定就會讓 ax 的區域擴展到整個 fig，圖片也就能完整填滿而無白邊。
```


🎯 效果圖示（概念）
```
+---------------------------+
|                           |  ← 整個 figure 區域
|   +-------------------+   |
|   |     image (ax)    |   |  ← 完全填滿，無邊界
|   +-------------------+   |
|                           |
+---------------------------+
```

🔧 小結：
這種方式不改變圖片內容與比例，只是在視覺上去除了周圍不必要的空白。

搭配 ax.axis('off') 一起使用，效果最佳。

📝 補充：可選的加強設定
如果圖片顯示時還是略有邊界，請確保：

figsize 和圖片比例一致（例如圖片是 16:9 → figsize=(8,4.5)）。

dpi 沒有限制視覺大小（通常不用改）。

請根據你的 frame 大小，微調 figsize，例如圖片是 1920x1080，則建議使用：fig, ax = plt.subplots(figsize=(9.6, 5.4))


* * *
# ipywidgets的[GridspecLayout](https://ipywidgets.readthedocs.io/en/7.x/examples/Layout%20Templates.html#Grid-layout)編排

#### AppLayout

```
from ipywidgets import AppLayout, Button, Layout

header_button = create_expanded_button('Header', 'success')
left_button = create_expanded_button('Left', 'info')
center_button = create_expanded_button('Center', 'warning')
right_button = create_expanded_button('Right', 'info')
footer_button = create_expanded_button('Footer', 'success')

AppLayout(header=header_button,
          left_sidebar=left_button,
          center=center_button,
          right_sidebar=right_button,
          footer=footer_button)
```
![AppLayout](https://github.com/user-attachments/assets/a1cbd7e7-4d92-40db-acb9-9c2575d9fe82)



```
from ipywidgets import GridspecLayout

grid = GridspecLayout(4, 3)

for i in range(4):
    for j in range(3):
        grid[i, j] = create_expanded_button('Button {} - {}'.format(i, j), 'warning')
grid
```
![image](https://github.com/user-attachments/assets/f0cf1380-1708-4f07-9796-b863d8963d75)



```
grid = GridspecLayout(4, 3, height='300px')
grid[:3, 1:] = create_expanded_button('One', 'success')
grid[:, 0] = create_expanded_button('Two', 'info')
grid[3, 1] = create_expanded_button('Three', 'warning')
grid[3, 2] = create_expanded_button('Four', 'danger')
grid
```
![image](https://github.com/user-attachments/assets/a2c80c20-22ef-4e74-a04c-d3851a1fe903)


# GridLayout 內容置中、靠右

    self.grid[2:10, 0] = ipyw.HBox([self.class_list], layout=ipyw.Layout(justify_content='flex-end', width='100%'))

    

    import ipywidgets as ipyw
    from IPython.display import display
    
    #  class_list 定義
    class_list = ipyw.RadioButtons(
        options=["0 robot", "1 clamp", "2 camera", "3 scanner", "4 control_panel", "5 ROS_panel", "6 object"],
        description='Labels:',
        layout=ipyw.Layout(height='auto', width='auto'), # 建議設置為 auto
        disabled=False
    )
    
    # --- GridspecLayout 中置中對齊 ---
    grid_center = ipyw.GridspecLayout(1, 3, width='500px', height='100px', border='2px solid red')
    # 將 class_list 放入一個 HBox 並置中，然後將 HBox 放入 grid
    grid_center[0, 1] = ipyw.HBox([class_list], layout=ipyw.Layout(justify_content='center', width='100%'))
    display(grid_center)
    
    
    # --- GridspecLayout 中靠右對齊 ---
    grid_right = ipyw.GridspecLayout(1, 3, width='500px', height='100px', border='2px solid blue')
    # 將 class_list 放入一個 HBox 並靠右，然後將 HBox 放入 grid
    grid_right[0, 2] = ipyw.HBox([class_list], layout=ipyw.Layout(justify_content='flex-end', width='100%'))
    display(grid_right)


# Button icon
![Jupyter_Widgets_button_icon](https://github.com/user-attachments/assets/191de4e8-2d94-4c9c-9941-ab499c043223)

If your Jupyter Notebook icon does not show, you may try to using the 'OLD' Font Awesome 4.7 (https://fontawesome.com/v4.7.0). 

```
pts_save_button = ipyw.Button(description=" Save pts by frame", button_style='primary', icon='floppy-o',
                             layout={'border': '2px solid gray', 'width': 'auto'})
pts_save_button_output = ipyw.Output()

pts_clear_button = ipyw.Button(description=" Clear pts", button_style='warning', icon='eraser',
                               layout={'border': '2px solid gray', 'width': '60%'})
pts_clear_button_output = ipyw.Output()
```


* * *


* * *
* * *
* * *

