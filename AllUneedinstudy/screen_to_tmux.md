***
| 功能描述 | Screen 指令 | Tmux 指令 |
| 開啟新工作階段 | screen | tmux |
| 開啟並命名工作階段 | screen -S <name> | tmux new -s <name> |
| 列出所有工作階段 | screen -ls | tmux ls |
| 重新連回工作階段 | screen -r <name> | tmux attach -t <name> |
| 刪除指定工作階段 | screen -X -S <name> quit | tmux kill-session -t <name> |
| 刪除所有工作階段 | - | tmux kill-server |


***
screen 為什麼會出現亂碼？
UTF-8 支援未開啟： screen 預設可能運作在 8-bit 模式，而 macOS 終端機（iTerm2 或 Terminal.app）預設使用 UTF-8。當 screen 不知道要處理 UTF-8 多位元組字元時，就會將中文或特殊符號解析成一堆亂碼。

語系設定 (Locale) 遺失： 進入 screen 會開啟一個新的 shell 層級，有時它不會正確繼承父層的環境變數（如 LANG 或 LC_ALL）。

Shell Prompt 衝突： 如果您的 shell 提示字元（Prompt）包含特殊符號或中文，且 screen 沒設定好，畫面會直接崩潰。

如何修復 (Fix)
1. 強制以 UTF-8 模式啟動 screen (最快的方法)
不要只輸入 screen，改用 -U 參數，這會強制 screen 進入 UTF-8 模式：

Bash
screen -U
2. 修改 .screenrc 配置檔 (永久修復)
在您的家目錄建立或編輯 .screenrc 檔案，確保它開機就支援 UTF-8：

輸入 nano ~/.screenrc

加入以下內容：

Plaintext
defutf8 on
utf8 on on
儲存並離開。


***
💡 更好的替代方案：tmux
如果您在 macOS 上經常遇到 screen 的編碼問題，強烈建議改用 tmux。

優點： 對 UTF-8 支援極佳，且畫面管理功能更強。

安裝： brew install tmux

啟動： 直接輸入 tmux 即可，幾乎不會遇到 screen 這種老舊的編碼問題。


1. 指令對照表 (CLI)
當您還沒進入環境，在一般的 Shell 底下執行時：

|功能	|Screen 指令	|Tmux 指令|
|開啟新連線	|screen	|tmux|
|開啟並命名	|screen -S name	|tmux new -s name|
|離開 (保留後台)	|Ctrl+A, D	|Ctrl+B, D|
|列出所有工作階段	|screen -ls	|tmux ls|
|重新連回 (Attach)	|screen -r name	|tmux attach -t name|
|刪除工作階段	|screen -X -S name quit	|tmux kill-session -t name|


2. 快捷鍵邏輯：Prefix 的改變
這是最容易不習慣的地方。所有的快捷鍵都需要先按一個 Prefix (前綴鍵)。

Screen: 預設是 Ctrl + A。

Tmux: 預設是 Ctrl + B。


常用快捷鍵對應：
功能Screen (Ctrl+A) Tmux (Ctrl+B)
脫離 (Detach)dd
列出所有視窗ww (這會開啟一個可選單)
建立新視窗cc
切換到下一個視窗nn
重新命名視窗A, (逗號)
顯示時間tt



3. Tmux 的殺手鐧：窗格 (Panes) 分割
tmux 最強大的地方在於可以在同一個螢幕內分割多個視窗，這對在 VM 上同時監看 nvidia-smi 和寫程式非常方便：

垂直分割： Ctrl + B 然後按 %

水平分割： Ctrl + B 然後按 " (雙引號)

切換窗格： Ctrl + B 然後按 方向鍵

關閉目前窗格： exit 或 Ctrl + D




4. 解決「滑鼠捲動」的問題
您剛從 screen 轉過來，可能會發現無法用滑鼠滾輪往回看訊息。在 tmux 中，預設需要進入 「複製模式」 才能捲動：

按下 Ctrl + B。

按下 [。

現在可以使用方向鍵或 PageUp/PageDown 捲動。

按下 q 退出捲動模式。

💡 小撇步：讓 Tmux 變好用
如果您不習慣 Ctrl+B，或想要開啟滑鼠支持，可以編輯 ~/.tmux.conf：

Bash
# 開啟滑鼠支持 (可以用滑鼠點選窗格、滾動)
set -g mouse on

# 如果您想改回 Ctrl+A 作為前綴鍵 (選配)
# unbind C-b
# set-option -g prefix C-a
# bind-shell C-a send-prefix





