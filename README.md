# Manga Reader (漫畫閱讀與離線下載 Android App)

本專案為使用 **Kotlin + Jetpack Compose + Room + WorkManager** 開發的 Android 漫畫閱讀器，專門支援漫畫人（manhuaren.com）漫畫解析、章節離線快取下載與流暢閱讀。

---

## 核心功能特色

1. **漫畫人線上解析與搜尋**：
   - 支援直接輸入或貼上漫畫人網址（例如 `https://www.manhuaren.com/manhua-wangzhetianxia/`）解析加入書架。
   - 內建搜尋功能與內建 WebView 瀏覽器。
2. **完整章節目錄與自動同步**：
   - 自動相容單行本（卷）與單話連載目錄結構。
   - 進入漫畫詳情頁時自動背景檢查並同步最新話數。
3. **整部漫畫離線下載**：
   - 支援背景整部批次下載所有章節漫畫圖片。
   - 離線快取無網路狀態下隨時流暢閱讀。
4. **沉浸式漫畫閱讀器**：
   - **上下滑動翻頁模式**（Vertical Pager）：原生流暢垂直翻頁。
   - **螢幕點擊翻頁**：點擊上半部上一頁、點擊下半部下一頁、點擊中央呼叫/隱藏控制列。
   - **連續條漫模式**（Vertical Scroll）與**左右翻頁模式**（Horizontal Pager）。
   - 沉浸全螢幕閱讀，自動隱藏系統上下黑條。
   - 記住閱讀進度與章節位置。

---

## 專案架構

- `app/src/main/java/com/mangareader/app/data/local/`: Room Database、DAO、實體資料庫模型（`MangaEntity`, `MangaChapterEntity`）。
- `app/src/main/java/com/mangareader/app/parser/`: `ManhuarenParser`（Jsoup 漫畫人網頁與圖片資料解析）。
- `app/src/main/java/com/mangareader/app/data/repository/`: `MangaRepository` 資料管理與下載排程。
- `app/src/main/java/com/mangareader/app/ui/`:
  - `MangaShelfScreen`: 漫畫書架首頁、最新話顯示、整部下載進度條。
  - `MangaDetailScreen`: 漫畫詳情、章節目錄、自動同步最新話數。
  - `MangaReaderScreen`: 漫畫閱讀器（上下滑動翻頁、點擊區域翻頁、雙指縮放）。
  - `MangaWebBrowserScreen`: 內建瀏覽器。

---

## 如何建置與執行

1. 使用 **Android Studio** 開啟目錄：
   ```
   D:\git\manga-reader
   ```
2. 等待 Gradle Sync 完成。
3. 點擊 **Run** 或執行 `./gradlew assembleDebug` 建置測試版；未提供簽章設定時，`assembleRelease` 會產生未簽章 APK，不會使用 debug key 偽裝正式版。

---

## GitHub Actions 自動簽章與最新 Release

本專案使用 `.github/workflows/android-release.yml`：

- Pull Request：以 JDK 17、Android SDK 34 編譯 Debug APK 並執行 `testDebugUnitTest`，**不讀取正式簽章 Secrets，也不發佈 Release**。
- 推送到 `main` 或在 `main` 手動執行 Actions：測試成功後，以固定簽章金鑰產生 Release APK，驗證 APK 的簽章憑證與版本，發佈至 GitHub Releases。
- 新版 Release 的 APK 與 `SHA256SUMS.txt` 上傳並確認存在後，才刪除所有舊的 Releases 及其 Tags。發佈或驗證失敗時保留上一版。
- 版本由工作流程自動產生：`versionName=1.0.<run_number>`、`versionCode=<run_number>+1`。安裝更新時須沿用同一份簽章金鑰。

### 必要的 GitHub Actions Secrets

到 **Settings → Secrets and variables → Actions → New repository secret** 設定：

| Secret | 說明 |
| --- | --- |
| `KEYSTORE_BASE64` | 固定 Android 簽章檔（JKS / PKCS12）的 Base64 全文 |
| `KEYSTORE_PASSWORD` | 簽章檔密碼 |
| `KEY_ALIAS` | 金鑰別名 |
| `KEY_PASSWORD` | 金鑰密碼 |

這四個 Secret 名稱與其他 Android 專案 Actions 的簽章設定相同，但 **Secrets 必須能被本儲存庫的 Actions 讀取**。若缺少任何一項，Release 會明確失敗且不會刪除先前的版本。不要將金鑰或密碼寫入 Git。
