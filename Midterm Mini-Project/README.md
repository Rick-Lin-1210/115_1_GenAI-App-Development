# 社團公告行動清單（期中個人小專題）

> 生成式 AI 應用開發｜第 8 週期中個人小專題（課堂簡化版）

## 1. 使用者與問題

- **使用者**：大學社團的一般社員。
- **情境**：幹部在群組貼出長篇公告，社員常漏看期限、要帶的東西，或沒發現公告根本沒寫時間地點。
- **任務**：貼上一則社團公告 → 整理成「活動名稱／時間地點／我要做的事／公告沒寫的事」四欄，社員看完就知道下一步，以及該回頭問幹部什麼。

## 2. 為什麼採用 JSON Schema 路線

- 欄位只需要 `string` 與 `array of string`，課程 JSON Schema 起點已完整支援，改動最少。
- Schema 直接以 `text.format` + `json_schema` + `strict=True` 送出，是真正的 Structured Outputs，不是只在 prompt 寫「回 JSON」。
- 回應再經 `check_result()` 逐欄檢查 key 與型別才保存。

## 3. 輸入與輸出欄位

輸入：一段社團公告文字（1～6000 字元）。

| 欄位 | 型別 | 畫面標籤 | 用途 |
|---|---|---|---|
| `event_title` | string | 📌 活動名稱 | 確認是哪個活動；沒寫就明說未提供 |
| `time_place` | string | 🕒 時間與地點 | 排行事曆；缺的部分明說未提供 |
| `action_items` | array of string | ✅ 我要做的事 | 報名、繳費、攜帶物品等，保留原文期限 |
| `missing_info` | array of string | ❓ 公告沒寫、需要問幹部的事 | 資訊不足時提醒社員去問，而不是讓模型編造 |

## 4. 安裝與啟動（本機）

在本資料夾（`Midterm Mini-Project`）執行：

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r ../requirements.txt   # 套件清單在 Repo 根目錄，原因見第 7 節
Copy-Item .env.example .env      # 再打開 .env 填入自己的 OPENAI_API_KEY
python -m streamlit run app.py
```

## 5. 設定方式

- 本機：`.env`（參考 `.env.example`）。
- 雲端：Streamlit Community Cloud → App settings → Secrets，格式參考 `.streamlit/secrets.example.toml`。
- `OPENAI_MODEL` 預設 `gpt-5.4-mini`。
- `.env`、`.streamlit/secrets.toml`、`.venv/`、`__pycache__/` 已被 `.gitignore` 排除，金鑰不進 GitHub。

## 6. 操作範例

1. 開啟 App，「啟用真實 API」預設**關閉**。
2. 貼上 [sample_data/normal.txt](sample_data/normal.txt)（虛構）→ 按「整理成行動清單」→ 只做輸入與設計檢查，0 次請求。
3. 勾選「啟用真實 API」→ 再送出一次 → 顯示四個欄位、原文與原始 JSON（1 次請求）。
4. 改貼 [sample_data/insufficient.txt](sample_data/insufficient.txt)（虛構）→ 檢查模型有沒有把缺少的時間、地點、費用列進「公告沒寫的事」，而不是自己編。

## 7. Streamlit Community Cloud 部署與驗收

| 項目 | 內容 |
|---|---|
| App 網址 | 待填（部署後貼上） |
| Repo／分支 | 待填／`main` |
| Main file path | `Midterm Mini-Project/app.py` |
| Cloud Python 版本 | 待填（Advanced settings 實際選擇） |
| 老師觀看權限 | 待填（公開或已加入老師） |
| 套件清單 | Repo 根目錄 `requirements.txt`（streamlit 1.63.0／openai 2.49.0／python-dotenv 1.2.3） |
| 雲端驗收日期／結果 | 待填 |

**為什麼 requirements.txt 放在 Repo 根目錄：** 2026-11-11 第一次部署時，requirements.txt 放在 `Midterm Mini-Project/` 內，Cloud 安裝失敗，log 顯示 `Invalid requirement: 'Mini-Project/requirements.txt'`，原因是平台沒有替含空格的路徑加引號。依說明書 3-1「入口同層或 Repo 根目錄」的規定，改用根目錄已有、內容相同的共用清單（比對後未覆蓋）。

詳細測試紀錄見 [第08週_課堂簡化版_專題設計與驗收紀錄.md](第08週_課堂簡化版_專題設計與驗收紀錄.md)。

## 8. 實測／未測項目

- 已測（離線）：空白、超過 6000 字元會提示且 0 次請求（AppTest 程式模擬）。
- 待測：雲端真實 API 正常資料、資訊不足各 1 筆；部署網址可開啟。結果請填入驗收紀錄。

## 9. 成本與限制

- API 預設關閉；每次有效送出最多 1 次請求，`max_retries=0`、timeout 60 秒。
- 預計請求數：正常 1 次 + 資訊不足 1 次 = 2 次（直接在雲端完成），額外重測另計。
- 6000 字元是成本護欄，不是 token 上限；超過會提示，不會默默截斷。
- 格式合格不代表內容正確，結果仍需對照原文人工核對。
- 只支援貼上文字，不支援檔案上傳；不要貼個資或機密內容。

## 10. AI 協助說明

- AI（Claude Code）協助：依課程 JSON Schema 起點填入題目、規則、Schema、標籤，撰寫 README 與虛構測試資料。
- 自己的修改與驗證：待填（例如：調整 prompt 用詞、實際操作雲端並逐欄核對）。
