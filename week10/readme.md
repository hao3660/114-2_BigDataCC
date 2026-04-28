# 第 10 週補充教學：Conda 指令教學

> 本講義整理 Conda 的常用指令，幫助你快速掌握 Python 環境與套件管理。

![Conda 教學速查圖](images/conda-overview.png)

---

## 學習目標

1. 認識 Conda 在 Python 開發中扮演的角色
2. 學會使用 Conda 建立、切換、刪除虛擬環境
3. 學會在環境中安裝、更新、移除套件
4. 學會查看 Conda 系統資訊與清理暫存
5. 學會匯出與重建環境設定檔，方便跨機器移植

---

## 重點摘要

| 功能類別 | 用途 | 代表指令 |
|---------|------|---------|
| 環境管理 | 建立獨立的 Python 空間，避免不同專案的套件衝突 | `create`、`activate`、`env list` |
| 套件管理 | 在特定環境中安裝或移除工具包 | `install`、`list`、`remove` |
| 系統維護 | 查看系統資訊與清理多餘的暫存檔 | `info`、`clean` |

---

## 1. 環境管理指令（Environment Management）

環境管理是 Conda 最核心的功能，讓你能夠為每個專案量身打造不同的 Python 版本。

| 指令 | 說明 |
|------|------|
| `conda create -n [env_name]` | 建立一個名為 `env_name` 的新環境 |
| `conda create -n [env_name] python=3.9` | 建立環境並指定特定的 Python 版本 |
| `conda activate [env_name]` | 切換（啟動）到指定環境 |
| `conda deactivate` | 退出當前環境，回到 base 環境 |
| `conda env list`（或 `conda info --envs`） | 列出目前電腦中所有的環境 |
| `conda remove -n [env_name] --all` | 刪除指定的環境及其所有套件 |
| `conda create --name [new_env] --clone [old_env]` | 複製一個現有的環境 |

### 操作示範

```bash
# 為本學期課程建立一個獨立環境
conda create -n bigdatacc python=3.11

# 進入該環境
conda activate bigdatacc
# 提示符會變成： (bigdatacc) user@host:~$

# 確認電腦中所有環境
conda env list

# 不再使用時，退出環境
conda deactivate

# 完全刪除某個環境（請先確認，不可還原）
conda remove -n bigdatacc --all
```

---

## 2. 套件管理指令（Package Management）

在進入特定環境後，可以使用以下指令來管理該環境內的工具。

| 指令 | 說明 |
|------|------|
| `conda list` | 列出目前環境中已安裝的所有套件 |
| `conda search [package_name]` | 在網路上搜尋可用的套件版本 |
| `conda install [package_name]` | 在當前環境安裝套件 |
| `conda install [package_name]=[version]` | 安裝指定版本的套件（例如 `pandas=1.3.0`） |
| `conda update [package_name]` | 更新指定的套件 |
| `conda remove [package_name]` | 從當前環境中移除特定套件 |

### 操作示範

```bash
# 進入環境後再裝套件，套件只會裝在這個環境
conda activate bigdatacc

# 安裝資料分析常用三件組
conda install pandas numpy matplotlib

# 確認套件是否裝好
conda list

# 想用特定版本（例如和教科書同版本）
conda install pandas=2.1.0

# 升級到最新版本
conda update pandas

# 移除不再需要的套件
conda remove matplotlib
```

### Conda 與 pip 的差異

| 項目 | conda install | pip install |
|------|---------------|-------------|
| 來源 | Conda 官方／社群 channel | PyPI |
| 相依性處理 | 自動解決 | 需自行處理 |
| 是否含 C 函式庫 | 多數有（如 NumPy、SciPy） | 部分需自行編譯 |
| 速度 | 較慢（需求解相依） | 較快 |

> **建議**：能用 `conda install` 就用 conda；若 conda 找不到，再用 pip。同一環境中兩者混用容易產生衝突，請斟酌使用。

---

## 3. 系統資訊與清理（System & Maintenance）

長期使用 Conda 會產生不少暫存檔，定期清理可以節省硬碟空間。

### 查看系統資訊

| 指令 | 說明 |
|------|------|
| `conda info` | 顯示 Conda 的版本、路徑與當前配置 |
| `conda --version` | 快速查看 Conda 版本 |

### 清理指令

| 指令 | 說明 |
|------|------|
| `conda clean --packages` | 刪除不再使用的套件包 |
| `conda clean --tarballs` | 刪除下載時留下的壓縮檔 |
| `conda clean --index-cache` | 刪除過期的索引快取 |
| `conda clean --all` | **最推薦使用**，一次清理所有快取、索引與暫存檔 |

### 操作示範

```bash
# 顯示 Conda 系統資訊
conda info

# 一鍵清理所有暫存（會列出可刪除項目，輸入 y 確認）
conda clean --all
```

---

## 4. 進階技巧：環境匯出與備份

當你需要把專案交給同事、或是換電腦繼續開發時，這兩個指令非常實用。

### 匯出環境設定檔（environment.yml）

```bash
# 在要備份的環境中執行
conda activate bigdatacc
conda env export > environment.yml
```

`environment.yml` 內容範例：

```yaml
name: bigdatacc
channels:
  - defaults
dependencies:
  - python=3.11.5
  - pandas=2.1.0
  - numpy=1.26.0
  - matplotlib=3.8.0
  - pip:
    - some-pip-only-package==1.0.0
```

### 從設定檔重建環境

```bash
conda env create -f environment.yml
```

> **小提醒**：如果某個套件在 Conda 找不到，可以嘗試先啟動環境後再使用 `pip install [package_name]`。雖然 Conda 會盡力追蹤 pip 安裝的內容，但建議優先使用 `conda install` 以維持環境的穩定性。

---

## 速查卡

| 用途 | 指令 |
|------|------|
| 建立新環境 | `conda create -n NAME python=3.X` |
| 啟動環境 | `conda activate NAME` |
| 退出環境 | `conda deactivate` |
| 列出所有環境 | `conda env list` |
| 刪除環境 | `conda remove -n NAME --all` |
| 列出已裝套件 | `conda list` |
| 安裝套件 | `conda install PKG` |
| 安裝指定版本 | `conda install PKG=VER` |
| 更新套件 | `conda update PKG` |
| 移除套件 | `conda remove PKG` |
| 查看系統資訊 | `conda info` |
| 清理所有暫存 | `conda clean --all` |
| 匯出環境設定 | `conda env export > environment.yml` |
| 從設定檔重建 | `conda env create -f environment.yml` |

---

## 常見地雷

| 問題 | 原因 | 解法 |
|------|------|------|
| 在 base 裝一堆套件 | 母環境被汙染，影響所有專案 | 永遠先 `conda create -n` 建立新環境 |
| `conda install` 跑超久 | 相依性求解慢 | 改用 `mamba`（Conda 加速版） |
| pip 與 conda 混裝出錯 | 兩套套件管理彼此干擾 | 同環境內優先 conda；pip 留到最後 |
| 忘了 activate 就 install | 套件被裝到 base 環境 | 每次開新終端機先 `conda activate [env]` |
| 硬碟越來越滿 | 暫存檔累積 | 每月跑一次 `conda clean --all` |

---

## 練習任務（選做，不計分）

1. 建立一個名為 `w10-conda` 的環境，Python 版本指定為 3.11
2. 進入該環境後安裝 `pandas` 和 `jupyter`
3. 用 `conda list` 截圖證明套件已裝好
4. 用 `conda env export > environment.yml` 匯出設定檔
5. 用 `conda clean --all` 清理暫存，記錄釋放的空間大小

完成後可在 `week10/conda_practice.txt` 記錄每步指令與輸出。
