# My Work – App quản lý công việc cá nhân

Toàn bộ app nằm trong một file `index.html`, không cần cài đặt hay build.

## Tính năng
- **Kanban** 3 cột (Cần làm / Đang làm / Xong), kéo thả trên máy tính, nút "→" trên điện thoại.
- Task có ưu tiên, hạn chót, dự án/nhãn, checklist việc con, ghi chú, ★ "3 việc quan trọng hôm nay", lịch sử đổi trạng thái.
- **Trợ lý AI (Gemini)**: kể việc bằng lời → AI tách thành task để bạn bấm "Áp dụng"; hỏi tiến độ, việc quá hạn, nên làm gì trước. Lịch sử chat được lưu.
- **Báo cáo**: số việc xong 7 ngày, tỉ lệ đúng hạn, thời gian xử lý trung bình, biểu đồ 14 ngày, theo dự án, danh sách quá hạn.
- **Nhật ký cuối ngày**: đã xong / đang chờ / mai làm gì.
- Chế độ tối, sao lưu Xuất/Nhập JSON, nhắc sao lưu sau 7 ngày.
- **Đồng bộ laptop ↔ điện thoại** qua Firebase (tuỳ chọn).

## Lưu dữ liệu
- Mặc định lưu trong trình duyệt (localStorage) — tự lưu sau mỗi thao tác.
- Có Firebase + đăng nhập Google: tự đồng bộ giữa các máy.
- Gemini API key chỉ lưu trên từng máy, **không** đồng bộ lên Firebase.

## Cài đặt (dùng Gmail cá nhân)

### 1. Gemini API key
1. Vào https://aistudio.google.com → **Get API key** → **Create API key**.
2. Mở app → **Cài đặt** → dán key → Lưu. Model mặc định `gemini-3.8-flash` (nếu Google đổi tên model, sửa ở ô Model).

> Gói miễn phí: Google có thể dùng nội dung để cải thiện dịch vụ — không nhập dữ liệu nhạy cảm (thông tin khách hàng, số tài khoản...).

### 2. Đưa app lên mạng (GitHub Pages) – để mở được trên điện thoại
1. Tạo repo mới trên GitHub (vd `my-work-app`), upload `index.html` và `README.md`.
2. Repo → **Settings → Pages** → Source: `Deploy from a branch`, Branch: `main` / `(root)` → Save.
3. Sau 1–2 phút có link `https://<username>.github.io/my-work-app/`.
4. Trên điện thoại mở link → menu trình duyệt → **Thêm vào màn hình chính**.

### 3. Firebase (đồng bộ) – khoảng 15 phút
1. Vào https://console.firebase.google.com → **Add project** (tắt Google Analytics cũng được).
2. **Build → Authentication → Get started → Sign-in method → Google → Enable**.
3. Authentication → **Settings → Authorized domains → Add domain**: `<username>.github.io`.
4. **Build → Firestore Database → Create database** → chọn vùng gần (vd `asia-southeast1`) → Production mode.
5. Firestore → tab **Rules**, dán và **Publish**:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
6. **Project settings (bánh răng) → Your apps → Web (`</>`)** → đặt tên → Register → copy đoạn `firebaseConfig = {...}`.
7. Mở app (qua link GitHub Pages) → **Cài đặt** → dán config → **Lưu & kết nối** → **Đăng nhập Google**.
8. Làm bước 7 trên điện thoại (dán cùng config) là hai máy dùng chung dữ liệu.

> Đăng nhập Google không chạy khi mở file trực tiếp (`file://`) — phải mở qua link GitHub Pages.
> firebaseConfig không phải mật khẩu; dữ liệu được bảo vệ bởi Rules ở bước 5.

## Phát triển tiếp với Gemini
Dán toàn bộ `index.html` vào Gemini (Canvas) kèm câu:
"Đây là app quản lý công việc một file HTML. Giữ nguyên chức năng và cấu trúc dữ liệu (`mywork.v2` trong localStorage, Firestore `users/{uid}`), chỉ thêm: ..."
Mỗi lần xin 1–2 tính năng, và **Xuất** sao lưu trước khi thay file mới.

Ý tưởng tiếp: lịch tháng, việc lặp lại, Pomodoro, thông báo nhắc hạn, PWA offline.
