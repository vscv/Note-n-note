# YOLO and others
---
---
# 物件偵測資料集資料夾架構對照 (COCO, VOC, YOLO)

在進行機器學習任務時，正確的資料夾結構是程式能夠順利讀取資料的關鍵。以下是三種最主流格式的架構說明。

1. MS COCO 格式

COCO 格式的特點是**「圖檔分離，標籤集中」**。所有的標記資訊都存在一個單一的 JSON 檔案中。
資料夾架構：

```
coco_dataset/
├── annotations/
│   ├── instances_train2017.json  # 訓練集所有標籤
│   └── instances_val2017.json    # 驗證集所有標籤
├── train2017/                    # 存放訓練圖片
│   ├── 000000000001.jpg
│   └── 000000000002.jpg
└── val2017/                      # 存放驗證圖片
    ├── 000000000031.jpg
    └── 000000000032.jpg
```

2. Pascal VOC 格式

VOC 格式採用 XML 檔案，特點是**「一對一」**，即每一張圖片都對應一個同名的 XML 標籤檔。
資料夾架構：
```
VOCdevkit/
└── VOC2012/
    ├── Annotations/              # 存放所有 XML 標籤檔
    │   ├── 2007_000032.xml
    │   └── 2007_000033.xml
    ├── JPEGImages/               # 存放所有 JPG 圖片檔
    │   ├── 2007_000032.jpg
    │   └── 2007_000033.jpg
    └── ImageSets/
        └── Main/
            ├── train.txt         # 條列訓練集的檔案名稱
            └── val.txt           # 條列驗證集的檔案名稱
```

3. YOLO 格式 (Ultralytics 標準)

YOLO 格式是目前最適合訓練的架構，它要求圖片與標籤路徑必須**「平行且對應」**。標籤為 .txt 檔，內容為歸一化後的座標。
資料夾架構：
```
yolo_dataset/
├── data.yaml                     # 設定檔：定義路徑與類別名稱
├── images/
│   ├── train/                    # 訓練圖片
│   │   ├── img1.jpg
│   │   └── img2.jpg
│   └── val/                      # 驗證圖片
│       ├── img3.jpg
│       └── img4.jpg
└── labels/
    ├── train/                    # 訓練標籤 (必須與圖片同名)
    │   ├── img1.txt
    │   └── img2.txt
    └── val/                      # 驗證標籤 (必須與圖片同名)
        ├── img3.txt
        └── img4.txt
```

💡 關鍵差異總結

|特性|	MS COCO|	Pascal VOC|	YOLO (Ultralytics)|
|---|---|---|---|
|標籤格式|	JSON (一個檔案包全部)|	XML (一個檔案對應一張圖)|	TXT (一個檔案對應一張圖)|
|座標系統|	\[x, y, width, height] |	\[x1, y1, x2, y2]	| \[class_id, x_center, y_center, width, height] |
|簡易記法|	左上角,寬高 |	左上＋右下	| 中心點,寬高 |
|單位	|絕對像素值|	絕對像素值|	歸一化 (0.0 到 1.0)|
|資料分割|	由 JSON 檔名區分|	由 ImageSets/Main/*.txt 定義	|由資料夾路徑直接區分|


⚠️ 特別注意 (YOLO 訓練)

在訓練 YOLO 模型時，程式會自動將路徑中的 /images/ 字串替換為 /labels/ 來尋找對應的標籤。因此：
1.	圖片與標籤的資料夾名稱必須固定為 images 與 labels。
2.	兩者內部的子資料夾結構（如 train/ 或 val/）必須完全一致。


---
# 主流偵測模型表

|特性	|RF-DETR (2025)	|YOLO26 (2026)	|RT-DETR v2 (2024)|
|---|---|---|---|
|核心架構	|Transformer (DINOv2 Backbone)	|高度優化 CNN (MuSGD 優化)	|Transformer (Hybrid Encoder)|
|訓練格式	|COCO / YOLO 自動切換	|嚴格 YOLO (.txt)	|主要是 COCO (.json)|
|NMS 依賴	|完全無需 NMS (End-to-End)	|無需 NMS (ProgLoss 優化)	|無需 NMS|
|收斂速度	|極快 (受惠於基礎模型預訓練)	|中等 (需較多 Epoch 訓練卷積)	|較慢 (Transformer 訓練較吃力)|
|顯存消耗	|高 (Attention 機制較佔記憶體)	|極低 (適合邊緣端訓練)	|高|
|領域泛化	|最強 (換場景不需大量資料)	|普通 (依賴資料增強)	|強|

---
--- 

## YOLO-segmentation performance

YOLO26-性能表
`Coming soon`

YOLO12-性能表

<p align="center">
  <img width=70% height=40% src="/temp_imgs/YOLO12-mAP.jpg">
</p>




YOLO11-性能表
- det
<p align="center">
  <img width=70% height=40% src="/temp_imgs/yolo11_mAPjpg.jpg">
</p>


- seg
<p align="center">
  <img width=70% height=40% src="/temp_imgs/YOLO11-mAP.jpg">
</p>



YOLOv9-性能表


<p align="center">
  <img width=70% height=40% src="/temp_imgs/yolov9_det性能表.jpg">
</p>


---

# YOLO 多GPU推論
# YOLO prediction with Multi-GPUs
- 由於 ultralytics/YOLO 模型的 model.predict() 本身不直接支援多 GPU 分佈式推論 (Distributed Inference)，我們需要使用 Python 的多進程 (Multiprocessing) 或 多線程 (Threading) 搭配 GPU 索引 來模擬並行化。 在這裡，使用 多進程 (multiprocessing) 是最安全和推薦的做法，因為它可以避免 Python 的 GIL（全局解釋器鎖）問題，並且能讓每個進程獨立運行在指定的 GPU 上。

由於檔案列表太大的話會造成GPU OOM（一次讀取所有照片檔造成）。我們在 single_gpu_predict 函式內部新增一個分批器 (Batcher) 邏輯，將分配給該 GPU 的大檔案列表 (file_list)，進一步切割成每次只包含 10 個檔案的小批次 (Sub-batches)。在16000張512x512影像，單卡6分鐘，8卡41秒完成推論。

```Bash
Active GPUs: [0, 1, 2, 3, 4, 5, 6, 7], N_GPUS: 8, Sub-Batch Size: 10
總檔案數: 16000, 分割成 8 份。
多 GPU 進程完成數: 100%|██████████| 8/8 [00:41<00:00,  5.14s/進程]                 

開始合併 8 個臨時結果檔案...
已收集並排序 43185 條檢測結果。
✅ 最終結果已儲存到 ./yolo-predict/epoch38.pt_final.txt (並已排序)
總計有 box 的影像張數: 12917, 總計 box 數量: 43185
所有臨時檔案已清除。
```


```Python

import os
import glob
from ultralytics import YOLO
from multiprocessing import Pool
from tqdm import tqdm

# --- 1. 配置參數 ---
Fold = "model-##"
imgsz = 512
PT = 'epoch38.pt'
run_ta = False
TTA = "-ta" if run_ta else ""
PT_PATH = f'runs/detect/{Fold}/weights/{PT}'
SOURCE_DIR = "./datasets/test_all/"

AVAILABLE_DEVICES = [0, 1, 2, 3, 4, 5, 6, 7]
N_GPUS = 8
ACTIVE_DEVICES = AVAILABLE_DEVICES[:N_GPUS]

OUTPUT_DIR = f'./predict_txt/{Fold}{TTA}_{PT}_{imgsz}_parallel/'
FINAL_OUTPUT_FILE = f'./predict_txt/{Fold}{TTA}_{PT}_{imgsz}_final.txt'

# --- 內部批次大小 ---
SUB_BATCH_SIZE = 10
print(f'Active GPUs: {ACTIVE_DEVICES}, N_GPUS: {N_GPUS}, Sub-Batch Size: {SUB_BATCH_SIZE}')

# 確保輸出目錄存在
os.makedirs(OUTPUT_DIR, exist_ok=True)


# --- 切分檔案列表 ---
def split_data(source_dir, n_splits):
    # split_data 函式
    all_files = glob.glob(os.path.join(source_dir, '*.png'))
    
    if not all_files:
        print(f"錯誤: 在 {source_dir} 中找不到任何 .png 檔案。")
        return []
    
    total_files = len(all_files)
    chunk_size = total_files // n_splits
    remainder = total_files % n_splits
    
    splits = []
    current = 0
    for i in range(n_splits):
        end = current + chunk_size + (1 if i < remainder else 0)
        splits.append(all_files[current:end])
        current = end
        
    print(f"總檔案數: {total_files}, 分割成 {n_splits} 份。")
    return splits


# --- 4. 單一 GPU 推論函式 (+內部批次處理) ---
def single_gpu_predict(args):
    """
    在單個進程中，使用指定的 GPU 對一組檔案進行推論，並進行內部批次切割。
    """
    gpu_id, total_file_list = args
    model = YOLO(PT_PATH)
    
    temp_output_path = os.path.join(OUTPUT_DIR, f'temp_gpu_{gpu_id}.txt')
    
    box_count_1 = 0
    box_count_2 = 0
    
    # --- 【關鍵：內部批次切割與循環】 ---
    
    # 設置 tqdm 追蹤該進程的工作進度
    total_files_in_split = len(total_file_list)
    
    # 為了避免 Too many open files 錯誤，我們使用列表收集後一次性寫入
    all_output_lines = []

    # 進行內部批次循環
    for i in tqdm(range(0, total_files_in_split, SUB_BATCH_SIZE),
                  desc=f"GPU {gpu_id} 推論進度 ({total_files_in_split} 張)",
                  leave=False):
        
        # 提取當前子批次的檔案列表
        sub_batch_list = total_file_list[i:i + SUB_BATCH_SIZE]
        
        # 執行推論 (此時 source 列表長度僅為 SUB_BATCH_SIZE)
        results = model.predict(source=sub_batch_list,
                                save=False,
                                imgsz=imgsz,
                                iou=0.75,
                                device=gpu_id,
                                augment=run_ta,
                                verbose=False
                                )
        
        # 處理結果
        for r in results: # results 現在是一個 list of Results
            # 取得圖片檔名（不含副檔名）
            filename = os.path.basename(r.path).split('.png')[0]

            boxes = r.boxes
            box_num = len(boxes.cls.tolist())

            if box_num > 0:
                box_count_1 += 1
                for j in range(box_num):
                    # 提取資訊
                    label = int(boxes.cls[j].item())
                    conf = boxes.conf[j].item()
                    x1, y1, x2, y2 = r.boxes.xyxy[j].tolist()

                    # 建立一行資料
                    line = f"{filename} {label} {conf:.4f} {int(x1)} {int(y1)} {int(x2)} {int(y2)}\n"
                    all_output_lines.append(line)
                    
                    box_count_2 += 1
    
    # 一次性寫入結果並關閉檔案 (解決 Too many open files)
    with open(temp_output_path, 'w') as output_file:
        output_file.writelines(all_output_lines)
    
    return temp_output_path, box_count_1, box_count_2, gpu_id

# --- 5. 合併結果函式 (+排序) ---
def combine_results(temp_files):
    """將所有臨時檔案合併成一個最終輸出檔案，並依檔名排序。"""
    combined_box_count_1 = 0
    combined_box_count_2 = 0
    all_lines = [] # 用於收集所有結果行
    temp_paths_to_delete = []

    print(f"\n開始合併 {len(temp_files)} 個臨時結果檔案...")
    
    # 步驟 1: 讀取所有臨時檔案並收集所有行
    for temp_path, box_c1, box_c2, _ in temp_files:
        combined_box_count_1 += box_c1
        combined_box_count_2 += box_c2
        
        if os.path.exists(temp_path):
            with open(temp_path, 'r') as infile:
                all_lines.extend(infile.readlines())
            temp_paths_to_delete.append(temp_path) # 收集路徑以便稍後刪除

    # 步驟 2: 排序所有行
    # 排序鍵：取每行的第一個元素 (即檔名: patient00##_####)
    def sort_key(line):
        return line.split(' ')[0]

    all_lines.sort(key=sort_key)
    print(f"已收集並排序 {len(all_lines)} 條檢測結果。")
    
    # 步驟 3: 寫入最終檔案
    with open(FINAL_OUTPUT_FILE, 'w') as outfile:
        outfile.writelines(all_lines)
            
    # 步驟 4: 清理臨時檔案
    for temp_path in temp_paths_to_delete:
        os.remove(temp_path)
            
    print(f'✅ 最終結果已儲存到 {FINAL_OUTPUT_FILE} (並已排序)')
    print(f'總計有 box 的影像張數: {combined_box_count_1}, 總計 box 數量: {combined_box_count_2}')
    print(f'所有臨時檔案已清除。')


# --- 6. 執行主程序 ---
if __name__ == '__main__':
    
    # 1. 數據分割
    file_splits = split_data(SOURCE_DIR, N_GPUS)
    
    if not file_splits:
        print("未找到任何檔案，程序結束。")
        exit()

    # 2. 準備多進程參數
    tasks = []
    for i in range(N_GPUS):
        gpu_id = ACTIVE_DEVICES[i]
        tasks.append((gpu_id, file_splits[i]))

    # 3. 啟動進程池
    with Pool(processes=N_GPUS) as pool:
        # 這裡使用 map 並且在外部使用 tqdm 追蹤進程完成數
        # 內部 tqdm (在 single_gpu_predict 內) 追蹤每個進程的批次進度
        results_from_pool = list(tqdm(pool.imap(single_gpu_predict, tasks),
                                      total=len(tasks),
                                      desc="多 GPU 進程完成數",
                                      unit="進程"))

    # 4. 合併結果
    combine_results(results_from_pool)
```


--- 
---
# RF-DETR
Roboflow 在 2025 年推出的 RF-DETR (Roboflow Detection Transformer) 是物件偵測領域的一個重要里程碑。

- RF-DETR 的訓練格式
   與傳統 DETR 僅支援 COCO 不同，RF-DETR 為了貼近工業界需求，設計了高度靈活的資料讀取機制：

   原生格式：COCO (JSON)

   這是 RF-DETR 最推薦的格式。它會尋找 train/_annotations.coco.json 檔案。對於精細的「海洋廢棄物」標籤（包含多種屬性或遮蔽標記），COCO 格式能提供最完整的元數據。

   兼容格式：YOLO (TXT)

   RF-DETR 的 SDK (rfdetr package) 具備自動偵測功能。如果你提供包含 data.yaml 與 train/images/ 的目錄，它能直接讀取 YOLO 格式進行訓練。

   預期結構：
```  
dataset/
├── train/ (images & labels/json)
├── valid/
└── test/
```


- 訓練階段的關鍵注意點

1. 梯度累積 (Gradient Accumulation)：

   RF-DETR 是 Transformer 架構，對 Batch Size 非常敏感。官方建議 Total Batch Size 應維持在 16。
```
小顯存 (如 T4 GPU)：batch_size=4, grad_accum_steps=4 (4x4=16)。

大顯存 (如 A100 GPU)：batch_size=16, grad_accum_steps=1。
```
2. DINOv2 權重初始化：
   
   RF-DETR 的強大源於其使用 DINOv2 作為 Backbone。在訓練時應確保載入預訓練權重，這對於辨識「沙灘上與自然物混雜的垃圾」非常有幫助，因為 DINOv2 的特徵提取具備極強的背景魯棒性。

2. 早停機制 (Early Stopping)：

   RF-DETR 在單一類別或簡單場景下收斂極快（有時不到 10 個 Epoch 就達到平台期）。建議開啟 early_stopping 以避免在海洋廢棄物這種樣本不均的資料集上產生過擬合。

3. 學習率 (Learning Rate)：

   通常設定在 1e-4。由於其 Transformer 特性，過大的學習率會導致損失函數（Loss）劇烈震盪甚至爆炸。

RF-DETR Segmentation Benchmarks and Training Guide  
https://www.youtube.com/watch?v=boVmetUahes  
這段影片詳細展示了 RF-DETR 在 2026 年初的最新基準測試以及如何針對自定義資料集進行微調訓練。  
---
# InternImage-H
###  書生圖像 - 大規模視覺基礎模型
   https://github.com/OpenGVLab/InternImage/blob/master/README_CN.md
   InternImage是一個由上海人工智慧實驗室、清華大學等機構的研究人員提出的基於變形神經（CNN）的視覺基礎模型。與基於Transformer的網路不同，InternImage以可變形算DCNv3（2024支援DCNv4）作為核心子，使模型不僅具有檢測和分割等下游任務所需的動態有效感受野，而且能夠進行自適應的聚合空間。

- 2022年11月10日：🚀 InternImage-H在COCO目標偵測任務上以65.4 mAP斬獲冠軍，是唯一突破65.0 mAP的超強目標偵測模型！

|骨幹	|方法	|box mAP（驗證/測試）	| #參數	|下載|
|---|---|---|---|---|
|CB-InternImage-H	|DINO（TTA）	|65.0 / 65.4	|2.18億	| ckpt有  cfg有|
|CB-InternImage-G	|DINO（TTA）	|65.3 / 65.5	|6B	|未公布|


   https://github.com/OpenGVLab/InternImage/blob/master/detection/README.md
   
   Our detection code is developed on top of MMDetection v2.28.1. 偵測是基於MM家的架構！

   MMDetection provides hundreds of existing and existing detection models in Model Zoo), and supports multiple standard datasets, including Pascal VOC, COCO, CityScapes, LVIS, etc.

#### COCO資料集的表現

<img width="500" alt="image" src="https://github.com/user-attachments/assets/49ad7132-b6d0-42b8-a43d-e7121656a8b6" />


## Training

`本身有支援SLURM跨節點訓練` `只要是基於troch訓練的都可以用srun來跨節點`

To train an `InternImage` on COCO, run:

```bash
sh dist_train.sh <config-file> <gpu-num>
```

For example, to train `InternImage-T` with 8 GPU on 1 node, run:

```bash
sh dist_train.sh configs/coco/mask_rcnn_internimage_t_fpn_1x_coco.py 8
```

## Manage Jobs with Slurm

For example, to train `InternImage-L` with 32 GPU on 4 node, run:

```bash
GPUS=32 sh slurm_train.sh <partition> <job-name> configs/coco/cascade_internimage_xl_fpn_3x_coco.py work_dirs/cascade_internimage_xl_fpn_3x_coco
```


---
# MMDetection provides hundreds of existing and existing detection models in Model Zoo), and supports multiple standard datasets, including Pascal VOC, COCO, CityScapes, LVIS, etc.

---

---
