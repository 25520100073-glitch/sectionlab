---
description: Chạy tiếp đội agent theo trạng thái trong .team/STATUS.md cho đến khi gặp cổng cần người duyệt
---

1. Đọc skill `team-pipeline` và file `.team/STATUS.md`. Nếu chưa có thư mục `.team/`, tạo mới theo mẫu trong skill (phase: SPEC, next_role: Architect).
2. Đóng vai `next_role` hiện tại và làm đúng phần việc của vai đó theo skill. Chỉ sửa file thuộc quyền của vai.
3. Cập nhật `.team/STATUS.md` và thêm một dòng vào `.team/LOG.md`.
4. Lặp lại bước 1 đến 3 với vai kế tiếp, cho đến khi gặp một trong các điểm dừng:
   - `next_role: Human` (SPEC chờ duyệt, task lỗi quá 3 lần, hoặc DONE)
   - `phase: BLOCKED`
   - Cần quyết định mà SPEC chưa trả lời
5. Khi dừng, tóm tắt cho người dùng: đã làm gì, đang ở đâu, cần người dùng quyết định gì.
