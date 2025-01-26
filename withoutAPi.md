如果不想使用 API，可以通過 **網頁爬蟲** 或其他開源工具直接下載 YouTube 播放清單的影片。以下是方法和相關程式碼：

---

### **步驟 1：使用 `yt_dlp` 下載 YouTube 播放清單**
`yt_dlp` 是一個強大的 YouTube 下載工具，支援直接下載播放清單中的影片，並可選擇畫質、音訊或字幕。

1. 安裝 `yt_dlp`：
   ```bash
   pip install yt-dlp
   ```

2. **下載播放清單的程式碼：**
   ```python
   from yt_dlp import YoutubeDL

   def download_playlist(playlist_url, save_path):
       options = {
           'outtmpl': f'{save_path}/%(playlist_title)s/%(title)s.%(ext)s',
           'format': 'bestvideo+bestaudio/best',  # 最高畫質
           'noplaylist': False,  # 明確表示下載播放清單
           'writesubtitles': True,  # 下載字幕
           'subtitleslangs': ['en', 'zh'],  # 選擇語言
           'postprocessors': [{
               'key': 'FFmpegVideoConvertor',
               'preferedformat': 'mp4',  # 轉為 MP4 格式
           }]
       }

       with YoutubeDL(options) as ydl:
           ydl.download([playlist_url])

   # 測試功能
   playlist_url = "https://www.youtube.com/playlist?list=你的播放清單ID"
   save_path = "./downloads"
   download_playlist(playlist_url, save_path)
   print("播放清單下載完成！")
   ```

---

### **步驟 2：生成字幕與校準時間軸**
1. **`yt_dlp` 自帶字幕下載功能**：
   上面的程式碼已經設置下載字幕（`writesubtitles: True`），可以直接獲取自動生成的 YouTube 字幕，並保存為 `.vtt` 文件。

2. 如果需要進一步調整字幕，可以使用 **`whisper`**（參考上一段中的範例）。

---

### **步驟 3：不需額外庫的簡單爬蟲方式**
如果希望僅爬取播放清單的影片 URL 並手動處理下載，可以使用 `BeautifulSoup` 分析網頁：

1. 安裝必要套件：
   ```bash
   pip install requests beautifulsoup4
   ```

2. **爬取播放清單影片 URL 的程式碼：**
   ```python
   import requests
   from bs4 import BeautifulSoup

   def get_playlist_urls(playlist_url):
       response = requests.get(playlist_url)
       soup = BeautifulSoup(response.text, 'html.parser')
       video_links = []
       for link in soup.find_all('a', href=True):
           href = link['href']
           if "/watch?v=" in href and "list=" in href:
               video_url = f"https://www.youtube.com{href.split('&')[0]}"
               if video_url not in video_links:
                   video_links.append(video_url)
       return video_links

   # 測試功能
   playlist_url = "https://www.youtube.com/playlist?list=你的播放清單ID"
   video_urls = get_playlist_urls(playlist_url)
   print("影片 URL：")
   for url in video_urls:
       print(url)
   ```

3. **用 `yt_dlp` 單獨下載影片：**
   ```python
   for video_url in video_urls:
       with YoutubeDL({'outtmpl': './downloads/%(title)s.%(ext)s'}) as ydl:
           ydl.download([video_url])
   ```

---

### **工具比較**
- **`yt_dlp`**：直接處理播放清單和字幕，自動化程度高，推薦！
- **`BeautifulSoup`**：適合單純獲取 URL，操作較為靈活。
- **`whisper`**：對音訊文件生成字幕更強大。

如果需要進一步優化或調整，隨時可以詢問我！
