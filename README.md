# 勝手に使わないでね(事前に許可した人はok)
## To woolisbest youtube.mdをみてね。
***
## Stream Api's Repo
### yuzu
- https://github.com/yuzubb/yudlp
### min
- https://github.com/Minotaur-ZAOU/min-tube-api4
### xerox
- https://github.com/Xerox-Pro/XeroxYT-NT-APIv1
### しあ
- ???(https://github.com/siawaseok-sub/siatube-backend-render)
### wista
- 非公開
***
## Api How to use
### MIN-Tube2 API リファレンス
| メソッド | ルート | 説明 | レスポンス例 |
|----------|-------|------|-------------|
| GET | /status | サーバーの稼働状況、応答時間、メモリ・CPU使用量、APIロード状態、健康状態を取得 | ```json { "status": "OK", "serverTime": "2026-03-16T12:34:56.789Z", "uptime": 1234.56, "responseTime": 5.2, "memoryUsage": {"heapUsed":12345678}, "cpuUsage": {"user":5000,"system":1000}, "apis":"loaded","health":"Excellent (100%)" } ``` |
| GET | /api/video/:id | 指定した動画IDの情報を取得。標準・高解像度動画URLや音声URLを含む | ```json { "stream_url": "...", "highstreamUrl": "...", "audioUrl": "...", "videoId": "abc123", "channelId": "channel123", "channelName": "Example Channel", "channelImage": "...", "videoTitle": "Example Video", "videoDes": "Video description here", "videoViews": 12345, "likeCount": 678 } ``` |
| GET | /api/comments/:id | 指定した動画IDのコメント情報を取得 | ```json [ { "author": "User1", "comment": "Great video!", "likeCount": 5, "publishedAt": "2026-03-16T12:00:00Z" }, ... ] ``` |
| GET | /api/channels/:id | 指定したチャンネルIDの情報を取得 | ```json { "channelId": "channel123", "channelName": "Example Channel", "channelImage": "...", "description": "This is a sample channel" } ``` |
| GET | /api | 取得済みの外部API一覧を返す | ```json [ "https://api1.example.com", "https://api2.example.com" ] ``` |
| GET | /return | 外部API一覧を再読み込み | ```text APIs refreshed. ``` |

---

💡 **補足**
- `:id` は動画IDまたはチャンネルIDを指定
- `/api/video/:id` では標準・高解像度・音声ストリームを取得可能
### MIN-Tube2 API Routes

MIN-Tube2 サーバーで利用できる主な API ルート一覧です。

| Method | Route | Description |
|------|------|-------------|
| GET | `/status` | サーバーの状態（稼働時間、応答速度、CPU・メモリ使用量など）を取得 |
| GET | `/api/video/:id` | 指定した動画IDの動画情報を取得 |
| GET | `/api/comments/:id` | 指定した動画IDのコメント情報を取得 |
| GET | `/api/channels/:id` | 指定したチャンネルIDのチャンネル情報を取得 |
| GET | `/api` | 現在読み込まれている外部APIの一覧を取得 |
| GET | `/return` | 外部API一覧を再読み込み（リフレッシュ） |
- `/status` はサーバーの健康状態チェックに利用
- `/api` と `/return` は外部APIの管理用
***
***
# yuzu API ドキュメント

`yuzu` は FastAPI と yt-dlp を使用した YouTube データ取得APIです。  
動画情報、プレイリスト、チャンネル、ライブ配信、ショート動画、字幕などを取得できます。

## ルート一覧

| メソッド | ルート | 説明 |
|----------|-------|------|
| GET | `/status` | サーバー稼働状況、キャッシュ統計、処理中ID数を返す |
| GET | `/cache` | 現在キャッシュにある動画・プレイリスト・チャンネルID一覧を返す |
| DELETE | `/cache` | すべてのキャッシュをクリアする |
| GET | `/stream/{video_id}` | 指定動画IDの利用可能なストリーム情報を返す |
| GET | `/playlist/{playlist_id}` | 指定プレイリストIDの動画リストとタイトル、動画数を返す |
| GET | `/channel/{channel_id}` | 指定チャンネルIDの動画一覧とチャンネル情報を返す |
| GET | `/channel/feature/{channel_id}` | チャンネルのfeaturedページ情報＋最大12件動画を返す |
| GET | `/channel/stream/{channel_id}` | チャンネルのライブ配信中・予定の動画一覧を返す |
| GET | `/short/{channel_id}` | チャンネルのショート動画一覧を返す |
| GET | `/subtitles/{video_id}` | 動画の利用可能字幕言語一覧を返す |
| GET | `/subtitles/{video_id}/content` | 動画の字幕内容を取得（`lang` パラメータで言語指定可） |

## 注意点

- チャンネル・動画・プレイリスト情報は `yt-dlp` を使用して取得  
- キャッシュ管理により同一リクエストの負荷を軽減  
- 非同期処理 (`ThreadPoolExecutor` + `asyncio`) で複数リクエストに対応  
- 字幕は VTT を解析して JSON 形式で返却
# xerox - API ルート一覧

## 1. `/api/shortstdata`
- GET `/api/shortstdata?id=<video_id>`
- Shorts 動画の全メタデータ取得
- 返却：YouTube 動画情報オブジェクト

## 2. `/api/suggest`
- GET `/api/suggest?q=<query>`
- YouTube サジェスト取得
- 返却：`["候補1", "候補2", ...]`

## 3. `/api/video-proxy`
- GET `/api/video-proxy?url=<video_url>`
- 動画 URL プロキシ（Range ヘッダー対応）
- ストリーミング可能

## 4. `/api/video`
- GET `/api/video?id=<video_id>`
- 動画詳細取得、関連動画最大 40 件
- 継続トークン対応

## 5. `/api/search`
- GET `/api/search?q=<query>&page=<1>&sort_by=<type>`
- 動画 / ショート / チャンネル / プレイリスト検索
- ページネーション・ソート対応

## 6. `/api/comments`
- GET `/api/comments?id=<video_id>&sort_by=<newest|top>&continuation=<token>`
- 動画コメント取得
- 返却：`{ comments: [...], continuation: token }`

## 7. `/stream`
- GET `/stream?id=<video_id>`
- yt-dlp でストリーミング URL 取得
- 映像+音声（mp4）と音声のみ（m4a）
- 返却：動画タイトル、duration、streamingUrl、audioUrl、フォーマット一覧

## 8. `/api/channel`
- GET `/api/channel?id=<channel_id>&page=<1>&sort=<latest|popular|oldest>`
- チャンネル情報 + 動画リスト
- ページネーション対応

## 9. `/api/channel-shorts`
- GET `/api/channel-shorts?id=<channel_id>`
- チャンネルショート動画取得
- 配列で返却

## 10. `/api/channel-home-proxy`
- GET `/api/channel-home-proxy?id=<channel_id>`
- 外部 API 経由でチャンネル情報取得（プロキシ）

## 11. `/api/channel-live`
- GET `/api/channel-live?id=<channel_id>`
- ライブ配信一覧取得
- エラー時は空配列

## 12. `/api/channel-community`
- GET `/api/channel-community?id=<channel_id>`
- コミュニティ投稿取得
- 投稿内容・公開時間・いいね数・添付画像/動画/選択肢対応

## 13. `/api/channel-playlists`
- GET `/api/channel-playlists?id=<channel_id>`
- プレイリスト一覧取得

## 14. `/api/playlist`
- GET `/api/playlist?id=<playlist_id>`
- プレイリスト情報取得

## 15. `/api/fvideo`
- GET `/api/fvideo`
- ホームフィード動画取得
