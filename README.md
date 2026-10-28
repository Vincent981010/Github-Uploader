# 📦 GitHub 資料夾上傳工具 (GitHub Folder Uploader)

這是一個純前端的開源工具，讓您可以直接在瀏覽器中選擇整個本機資料夾，並以「單一 Commit」的形式將所有檔案上傳到 GitHub 儲存庫。

## 💡 運作原理

本工具捨棄了一般網頁版或普通 API「每次上傳一個檔案就產生一個 Commit」的作法，改為呼叫底層的 **GitHub Git Database API**。運作流程如下：

1. **取得現有狀態 (Commit / Tree)**：先向 GitHub 獲取目標分支目前的最新目錄樹（`base_tree`）的 SHA 雜湊值。
2. **上傳新檔案 (Blobs)**：將您選取的資料夾內的檔案，逐一轉為 Base64 並上傳至 GitHub，取得各檔案的 Blob SHA。
3. **建立新目錄樹 (Tree)**：將步驟 2 的新檔案清單與步驟 1 的 `base_tree` 結合。
   * *無痛覆蓋與保留*：GitHub 會自動比對，用新檔案覆蓋同名檔案，並完全保留原本就在 `base_tree` 中但這次未上傳的舊有檔案。
4. **建立提交 (Commit)**：將新的目錄樹打包成一個新的 Commit。
5. **更新指標 (Ref)**：將分支的指標推移到這個最新的 Commit，完成上傳。

## 📝 使用教學

在開始使用前，您需要先取得一組擁有儲存庫寫入權限的 **Personal Access Token (PAT)**。

### 步驟 1：產生 GitHub Token
1. 前往 GitHub 的 [Developer settings > Personal access tokens > Tokens (classic)](https://github.com/settings/tokens)。
2. 點擊 **"Generate new token (classic)"**。
3. 填寫 Note（例如：Folder Uploader Tool）。
4. 在 **Select scopes** 中，勾選 `repo` (Full control of private repositories) 權限。
5. 點擊頁面底部的 **"Generate token"**。
6. **立刻複製**並妥善保存 Token（格式如 `ghp_...`）。

### 步驟 2：在網頁中使用
1. 開啟本工具網頁。
2. 貼上剛剛取得的 **Token**，並輸入目標的 **Owner** (帳號名稱) 與 **Repo** (儲存庫名稱)。
3. **設定檔管理 (選填)**：
   * 勾選「記住我的設定」將資料存在瀏覽器本機。
   * 點擊「匯出設定」可將當前填寫的資料存成 `github-uploader-settings.json`。
   * 點擊「匯入設定」可快速載入之前存檔的設定檔。
4. 點擊虛線框，選擇本機的一個**資料夾**。
5. 點擊「開始上傳」，即可在下方「執行日誌」觀察進度。

## ⚠️ 注意事項

- **隱私與安全**：本工具只會在您的本機瀏覽器內運作，不會將 Token 傳送到任何第三方伺服器。若勾選儲存設定，資料將存放在瀏覽器的 LocalStorage 中；匯出的設定檔以明碼 JSON 儲存，請妥善保管。
- **檔案限制**：由於檔案在前端轉換為 Base64 上傳，建議用於程式碼、圖片或一般文件檔。
- **空資料夾**：瀏覽器的 API 預設會過濾空資料夾，上傳時只會處理內部帶有檔案的目錄結構。
