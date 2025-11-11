# YOLO note

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
    # ... (split_data 函式內容保持不變)
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
    
    # --- 【關鍵修正：內部批次切割與循環】 ---
    
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
