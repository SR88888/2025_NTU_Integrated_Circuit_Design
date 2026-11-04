# 2025 NTU Integrated Circuit Design

國立臺灣大學 2025 年 **Integrated Circuit Design** 課程實作紀錄，包含 RSA 電路作業與影像卷積期末專題。兩個專案均提供 Verilog RTL、題目規格與壓縮測試環境。

## 專案內容

| 專案 | 主要功能 | RTL | 規格 |
| --- | --- | --- | --- |
| HW2：RSA | 16-bit 輸入／輸出的 RSA 相關運算，含模乘與 Montgomery 運算模組 | [RSA.v](HW2/RSA.v) | [ICD_hw2.pdf](HW2/ICD_hw2.pdf) |
| Final：Image Convolution | 64 × 64 灰階影像卷積、ReLU 與 2 × 2 max-pooling | [CONV.v](Final%20Project/CONV.v) | [ICD_final.pdf](Final%20Project/ICD_final.pdf) |

期末專題支援一般 3 × 3 卷積與 dilated 3 × 3 卷積模式。卷積及 ReLU 產生 64 × 64 的中間結果，再以 stride 2 的 max-pooling 產生 32 × 32 輸出；資料透過題目定義的記憶體介面存取。

## 目錄結構

```text
.
├── HW2/
│   ├── RSA.v            # RSA RTL 實作
│   ├── ICD_hw2.pdf      # 作業規格
│   └── ICD_hw2.tar      # 題目 RTL 模板、測試平台與測試資料
└── Final Project/
    ├── CONV.v           # 卷積 RTL 實作
    ├── ICD_final.pdf    # 專題規格
    └── ICD_final.tar    # 測試平台、patterns 與各階段設定
```

壓縮檔內含題目模板；以下範例會將目錄外的實作版本複製到解壓後的測試環境中。

## 環境需求

- Linux／Unix shell、`tar`。
- 支援 Verilog／SystemVerilog 的模擬器；下列範例使用 Synopsys VCS。
- 邏輯合成需要 Synopsys Design Compiler。
- 閘級與佈局後模擬需要對應的標準元件模型；期末專題設定引用課程的 N16 ADFP library 路徑。

合成與 APR 需要課程工作站的工具與製程資料，請依自己的環境調整 `.synopsys_dc.setup`、library 與時序設定。

## 快速開始：卷積專題

```bash
git clone https://github.com/SR88888/2025_NTU_Integrated_Circuit_Design.git
cd 2025_NTU_Integrated_Circuit_Design

mkdir -p work/conv
tar -xf "Final Project/ICD_final.tar" -C work/conv
cp "Final Project/CONV.v" work/conv/ICD_final/01_RTL/CONV.v

cd work/conv/ICD_final/01_RTL
vcs -full64 -R -sverilog -f rtl.f +define+pat0
```

`rtl.f` 會載入 `CONV.v`、`tb_CONV.v` 與記憶體模型。更換 `pat0` 為 `pat1`～`pat9` 可使用壓縮檔附帶的其他影像資料。測試選項區分大小寫，並須從 `01_RTL` 目錄執行，以維持相對路徑正確。

## 快速開始：RSA 作業

下列命令從 repository 根目錄執行：

```bash
mkdir -p work/rsa
tar -xf HW2/ICD_hw2.tar -C work/rsa
cp HW2/RSA.v work/rsa/ICD_hw2/01_RTL/RSA.v

cd work/rsa/ICD_hw2/01_RTL
vcs -full64 -R -sverilog RSA.v ../00_TESTBENCH/tb_RSA.v +define+pat0
```

RSA 測試環境包含 `pat0`～`pat3`。請搭配規格確認握手訊號與輸入／輸出格式。

## 合成與實體設計流程

期末專題壓縮檔提供 `02_SYN`、`03_GATE`、`04_APR` 與 `05_POST` 的部分設定：

- `02_SYN/syn.tcl` 與 `CONV.sdc`：合成及時序限制。
- `03_GATE/gate.f`：閘級模擬檔案清單。
- `04_APR/mmmc.view`：多模式多角落分析設定。
- `05_POST/post.f`：佈局後模擬檔案清單。

這些設定會引用前一階段產生的 netlist、SDF 與外部 library；先完成對應階段並核對路徑，再執行後續流程。
