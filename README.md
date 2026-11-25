# Câu Cá LIVE — Bản Ably (source of truth cho GitHub Pages)

Bản sao trang game nhận event qua **Ably** thay cho WebSocket nội bộ.
Đây là bản sẽ up lên repo `cau-ca-live` (GitHub Pages).

## File

- `index.html`, `style.css`, `app.js` — copy từ `~/workspace/tiktok-live-cau-ca/`,
  chỉ khác: client Ably (thay WebSocket) + tự chạy khi `?obs=1`.
- `ably-config.js` — CHỈ key **subscribe-only** (public theo thiết kế).
  KHÔNG BAO GIỜ để key publish vào đây. Hỗ trợ `?ablyKey=` / `?ablyChannel=`.
- Server + `.env` (key publish) nằm ở `~/workspace/tiktok-live-cau-ca/`
  (port 3001, WS nội bộ 8080 chỉ để test local).

## Thay đổi so với bản gốc (chỉ đường nhận event, logic game giữ nguyên)

1. `app.js` §15: `connectWebSocket` / `handleWsMessage` → `connectAbly` /
   `handleAblyMessage` qua `@ably/pubsub-device` 3.2.0
   (`AblyPubSub.createClient({ key, clientId: 'obs-'+random })`).
   Subscribe `comment` / `like` / `gift` / `status` trên kênh `cau-ca-live`;
   lọc event cũ hơn lúc tải trang − 10s; dedupe theo `msg.id` (giữ 500).
2. `app.js` `parseUrlParams`: thêm `?obs=1` → `config.obsMode`; `init()`
   tự gọi `startGame()` sau 300ms khi `?obs=1` (không cần bấm ▶ Bắt đầu).
   KHÔNG hiện nút 'Bấm để bật tiếng' ở chế độ OBS.
3. `index.html`: thêm script SDK Ably (jsDelivr) + `ably-config.js` trước `app.js`.

## Link OBS (sau khi up GitHub)

```
https://covipga-hash.github.io/cau-ca-live/?obs=1&live=1percent.moingay&clean=1
```

Lưu ý: tải lại trang giữa live reset ván về đầu, điểm 0 (state client-side).
Giọng MC có thể bị Chrome chặn tới khi bấm Interact 1 lần vào Browser Source
(không có thao tác chạm khi tự chạy).
