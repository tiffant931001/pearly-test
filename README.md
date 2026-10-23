# Pearly 同步測試版

這是測試用的 Pearly，資料跟正式版分開，正式版不受影響。
要測的流程：**點手機背面三下 → 捷徑把畫面上的付款資料存到 GitHub → 打開 Pearly 測試版，自動收進來，不用按貼上。**

正式版的「點兩下＋貼上」照常可以用，兩個不衝突。

---

## 一、建一個私人倉庫，用來暫放記錄（約 2 分鐘，在 iPhone 或電腦都可以）

1. 打開 https://github.com/new
2. Repository name 填 `pearly-inbox`
3. 選 **Private**（一定要選私人）
4. 勾選 **Add a README file**
5. 按 **Create repository**

> 為什麼不用 Gist：Gist 刪掉的內容還留在修改紀錄裡，任何拿到網址的人都看得到。私人倉庫只有你看得到。

## 二、建一組金鑰，只能動這個倉庫（約 3 分鐘）

1. 打開 https://github.com/settings/personal-access-tokens/new
2. Token name 填 `Pearly`
3. Expiration 選 **1 year**（過期後照這步重做一組就好）
4. Repository access 選 **Only select repositories**，下拉選 `pearly-inbox`
5. Permissions → Repository permissions → **Contents** 改成 **Read and write**（其他都不用動）
6. 按最下面的 **Generate token**，複製那串 `github_pat_` 開頭的文字

> 這串金鑰等於這個倉庫的鑰匙：不要傳給別人，也不要把含金鑰的捷徑分享出去。外流的話，到 https://github.com/settings/personal-access-tokens 把它刪掉就失效了。

## 三、把測試版放上網（交給小龍蝦）

把這個 `pearly-test` 資料夾放到電腦上，把下面這段複製給小龍蝦：

```
請幫我把一個網頁發佈到 GitHub Pages，用一個新的專案，不要動我原本的 pearly 專案。
資料夾位置：<這裡換成 pearly-test 資料夾的完整路徑>

步驟：
1. 在資料夾裡執行：
   git init
   git add .
   git commit -m "Pearly 同步測試版"
   git branch -M main
   gh repo create pearly-test --public --source . --push
2. 開啟 GitHub Pages：
   gh api -X POST repos/{owner}/pearly-test/pages -f "source[branch]=main" -f "source[path]=/"
   （{owner} 用 gh api user --jq .login 查）
3. 等 1～2 分鐘，用 gh api repos/{owner}/pearly-test/pages --jq .html_url 查網址，告訴我。

注意：只動這個資料夾和 pearly-test 這個專案。
```

網址會像 `https://你的帳號.github.io/pearly-test/`。用 Safari 打開就可以測，不用加到主畫面。

## 四、在測試版填同步設定

1. 打開測試版 → 右上「設定」→ 找到 **GitHub 自動同步**
2. 倉庫：填 `你的帳號/pearly-inbox`
3. 金鑰：貼上第二步的金鑰
4. 按 **儲存並測試連線**，看到「✓ 連線成功」就好了

## 五、建捷徑「Pearly 同步測試」

打開「捷徑」App → 右上「+」→ 名稱取 `Pearly 同步測試`，依序加入：

1. **截取螢幕畫面**
2. **從影像中擷取文字**
3. **格式化日期**：日期選「目前日期」，格式選「自訂」，輸入 `yyyy/MM/dd HH:mm`
4. **文字**：照下面輸入，`[ ]` 的地方插入變數
   ```
   #Pearly
   [擷取的文字]
   記錄時間 [格式化的日期]
   ```
5. **Base64 編碼**：輸入選上一步的「文字」，模式「編碼」，點箭頭展開，**換行選「無」**
6. **格式化日期**：日期選「目前日期」，格式選「自訂」，輸入 `yyyyMMddHHmmss`
7. **取得 URL 的內容**：
   - URL：`https://api.github.com/repos/你的帳號/pearly-inbox/contents/inbox/p-[第 6 步的格式化的日期].txt`
   - 點箭頭展開：
     - 方法：**PUT**
     - 標頭：新增兩個
       - `Authorization`：`Bearer 你的金鑰`（Bearer 後面有一個空格）
       - `Accept`：`application/vnd.github+json`
     - 要求本文：**JSON**，新增兩個「文字」欄位
       - `message`：`add`
       - `content`：選第 5 步的「Base64 編碼的結果」
8. **顯示通知**：內容打 `已送到 Pearly`

設定觸發：設定 → 輔助使用 → 觸控 → 背面輕點 → **點三下** → 選「Pearly 同步測試」。
（點兩下維持原本的 LINE Pay 捷徑。）

## 六、測試（不用真的付款）

1. 打開相簿裡之前的截圖，例如中信 LINE 通知或 LINE 錢包通知那張，讓它顯示在畫面上
2. 點背面三下，等通知「已送到 Pearly」
3. 用 Safari 打開測試版 → 那筆應該自動出現，沒有按貼上

也可以連續送兩三張再打開，看是不是一次全部進來。

**要回報給我的：**
- 有沒有看到「已送到 Pearly」；沒有的話，捷徑跳出的錯誤訊息截圖
- 測試版有沒有自動收到；金額、店名、時間對不對
- 收進來之後，到 GitHub 的 `pearly-inbox` 倉庫看 `inbox` 資料夾是不是空的（收完會自動刪掉）

## 記錄經過哪裡

- 截圖在 iPhone 上辨識文字，圖片不會上傳
- 辨識出的文字（金額、店名、時間）會短暫存在你的私人倉庫，Pearly 收完就刪掉；倉庫的修改紀錄會留著，但只有你看得到
- 簡訊裡的驗證碼，Pearly 收進來時會遮掉
