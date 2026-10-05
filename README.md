# Báo cáo Bài 3: Xử lý xung đột phức tạp trong quá trình Rebase

## 1. Mục tiêu
Rebase nhánh feature-api lên trên main khi cả hai cùng sửa file config.json, giải quyết xung đột thủ công từng chặng để có lịch sử thẳng hàng, không tạo merge commit.

## 2. Bối cảnh dữ liệu
- main có 2 commit: "update port on main" (port 8081) và "add env config" (thêm env production).
- feature-api có 2 commit: "feat: change port" (port 9000) và "feat: enable debug" (debug true).
- Cả hai nhánh cùng sửa file config.json nên khi rebase sẽ phát sinh xung đột.

## 3. Thực hiện rebase
Đứng ở nhánh feature-api, chạy:
  git rebase main

Git áp từng commit của feature-api lên trên main và dừng lại ở mỗi chặng có xung đột.

## 4. Giải quyết xung đột từng chặng

### Chặng 1 — commit "feat: change port"
- Xung đột ở dòng "port": main để 8081, feature-api để 9000.
- Giải quyết: giữ phần env của main, áp port 9000 của feature-api.
- Thao tác: mở config.json xóa các ký hiệu <<<<<<<, =======, >>>>>>>, sửa lại nội dung đúng, lưu file.
- Đánh dấu đã giải quyết và tiếp tục:
  git add config.json
  git rebase --continue

### Chặng 2 — commit "feat: enable debug"
- Xung đột tiếp theo ở file config.json.
- Giải quyết: giữ env của main, áp debug true của feature-api.
- Thao tác tương tự: sửa file, git add config.json, git rebase --continue.

## 5. So sánh giải quyết xung đột khi Merge và khi Rebase
- Merge: gộp hai nhánh và tạo thêm một merge commit; xung đột được giải quyết một lần duy nhất tại thời điểm merge; lịch sử bị rẽ nhánh.
- Rebase: áp lần lượt từng commit của nhánh tính năng lên đầu nhánh chính; xung đột có thể phải giải quyết nhiều lần, mỗi lần cho một commit; lịch sử cuối cùng thẳng hàng, không có merge commit.

## 6. Kết quả

File config.json sau khi hoàn tất:
  {
    "port": 9000,
    "debug": true,
    "env": "production"
  }

Lịch sử sau khi rebase (git log --graph --oneline) thẳng tắp:
  * d321ee7 (HEAD -> feature-api) feat: enable debug
  * a90bad1 feat: change port
  * d8a04ef (main) add env config
  * a7b0233 update port on main
  * 617c80f init config

## 7. Kết luận
Nhánh feature-api đã được đưa lên nối tiếp ngay sau các commit mới nhất của main. Lịch sử thẳng hàng, không có merge commit phụ, đồng thời vẫn giữ nguyên thay đổi env của nhánh chính (không phá hỏng code của main).


