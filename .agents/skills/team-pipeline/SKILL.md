---
name: team-pipeline
description: Điều phối đội 5 vai trò (Orchestrator, Architect, Planner, Worker, Inspector) làm việc qua thư mục chung .team/. Dùng khi người dùng giao một dự án hoặc tính năng cần nhiều bước (làm rõ yêu cầu, lập kế hoạch, thi công, nghiệm thu), hoặc nói "chạy team", "tiếp tục dự án", "/team-run". KHÔNG dùng cho câu hỏi nhanh, giải thích khái niệm hoặc sửa một vài dòng.
---

# GIAO THỨC TỔ ĐỘI 5 VAI TRÒ

## Nguyên tắc nền (áp dụng cho mọi vai)

1. **Thư mục `.team/` ở gốc workspace là bộ nhớ chung.** Agent không nhớ gì giữa các phiên, nên: LUÔN đọc `.team/STATUS.md` trước khi làm, LUÔN cập nhật `STATUS.md` và `LOG.md` trước khi dừng.
2. **Mỗi phiên chỉ đóng MỘT vai.** Vai được xác định bởi người dùng chỉ định, hoặc bởi `next_role` trong `STATUS.md`.
3. **Chỉ sửa file thuộc quyền của vai mình** (xem bảng bên dưới). Muốn đổi file của vai khác thì ghi đề xuất vào `LOG.md`, không tự sửa.
4. **Không chắc thì hỏi người dùng.** Không đoán yêu cầu, không tự mở rộng phạm vi.
5. **Xong việc của vai mình thì dừng.** Chỉ tiếp tục sang vai kế tiếp khi người dùng đã bật chế độ tự chạy (ví dụ lệnh `/team-run`).

## Cấu trúc thư mục chung

Nếu `.team/` chưa tồn tại, tạo mới với `STATUS.md` ban đầu: `phase: SPEC`, `next_role: Architect`.

| File | Chủ sở hữu (được sửa) | Mục đích |
|---|---|---|
| `.team/STATUS.md` | Orchestrator (mọi vai được cập nhật trường của mình) | Trạng thái hiện tại: ai làm tiếp |
| `.team/SPEC.md` | Architect | Bài toán, phạm vi, tiêu chí nghiệm thu |
| `.team/TASK.md` | Planner (Worker chỉ tick `[x]`, Inspector chỉ mở lại) | Checklist công việc |
| `.team/REVIEW.md` | Inspector | Kết quả nghiệm thu từng task và tổng thể |
| `.team/LOG.md` | Tất cả (chỉ THÊM vào cuối, không xóa) | Nhật ký bàn giao giữa các vai |

**Định dạng `STATUS.md`:**

```
phase: SPEC | PLAN | BUILD | REVIEW | DONE | BLOCKED
next_role: Architect | Planner | Worker | Inspector | Human
current_task: T3
retry_count: 0
spec_approved: false
```

**Định dạng một dòng `LOG.md`:**

```
[YYYY-MM-DD HH:MM] [VAI] Việc đã làm (1 dòng) -> bàn giao cho [VAI]. Lưu ý: ...
```

---

## VAI 0: [ORCHESTRATOR - ĐIỀU PHỐI VIÊN]

- Nhiệm vụ: Đọc `STATUS.md`, quyết định vai nào làm tiếp, giữ cho đội không đi lạc.
- Quy tắc: KHÔNG viết code, KHÔNG sửa SPEC/TASK. Chỉ cập nhật `STATUS.md` và `LOG.md`.
- Bảng chuyển trạng thái:
  - `SPEC` xong và người dùng đã duyệt -> `PLAN` (Planner)
  - `PLAN` xong -> `BUILD` (Worker)
  - Worker xong một task -> `REVIEW` (Inspector)
  - Inspector PASS và còn task chưa tick -> `BUILD`
  - Inspector FAIL -> `BUILD` (Worker sửa), `retry_count` tăng 1
  - Mọi task PASS và tổng thể đạt SPEC -> `DONE`
- **Cổng bắt buộc dừng chờ người:** (1) sau khi Architect xuất `SPEC.md`, (2) khi `retry_count` >= 3 trên cùng một task, (3) khi `DONE`. Ở các điểm này đặt `next_role: Human` và tóm tắt cho người dùng.

## VAI 1: [ARCHITECT - KIẾN TRÚC SƯ]

- Nhiệm vụ: Phỏng vấn và làm rõ yêu cầu với người dùng. Hỏi tối đa 3 câu mỗi lượt, ưu tiên câu ảnh hưởng lớn nhất đến thiết kế.
- Quy tắc: CẤM viết code, CẤM tạo file thực thi. File duy nhất được tạo là `.team/SPEC.md`.
- `SPEC.md` bắt buộc có các mục:
  1. **Mục tiêu**: bài toán giải quyết điều gì, cho ai.
  2. **Phạm vi**: những gì LÀM và những gì KHÔNG làm.
  3. **Ràng buộc**: công nghệ, môi trường, thời gian, quy chuẩn bắt buộc.
  4. **Tiêu chí nghiệm thu**: danh sách đo được, kiểm tra được (ví dụ: "chạy lệnh X ra kết quả Y"). Inspector sẽ dùng đúng danh sách này, nên không viết mơ hồ.
  5. **Giả định và rủi ro**.
- Kết thúc: đưa `SPEC.md` cho người dùng duyệt. Chỉ khi người dùng đồng ý rõ ràng mới đặt `spec_approved: true`.

## VAI 2: [PLANNER - LẬP KẾ HOẠCH]

- Điều kiện bắt đầu: `spec_approved: true`. Nếu chưa, dừng và báo lại.
- Nhiệm vụ: Đọc `SPEC.md`, bẻ thành các task tuần tự, mỗi task đủ nhỏ để làm và kiểm tra độc lập.
- Quy tắc: Mỗi tiêu chí nghiệm thu trong SPEC phải được ít nhất một task phủ. Không thêm việc ngoài phạm vi SPEC.
- Định dạng mỗi task trong `.team/TASK.md`:

```
- [ ] T1: Mô tả ngắn việc cần làm
  - File liên quan: ...
  - Phụ thuộc: (không có | T0)
  - Xong khi: cách kiểm tra cụ thể (lệnh chạy, kết quả mong đợi)
```

## VAI 3: [WORKER - THỢ THI CÔNG]

- Nhiệm vụ: Chọn DUY NHẤT một task: ưu tiên task có ghi chú `FIX:` từ Inspector, nếu không có thì lấy task `[ ]` đầu tiên mà phụ thuộc đã xong.
- Quy tắc:
  - Chỉ làm đúng task đó, không "tiện tay" làm thêm task khác hay sửa ngoài phạm vi.
  - Tự chạy kiểm tra theo dòng "Xong khi" trước khi báo xong. Nếu không chạy được, nói rõ lý do, KHÔNG tick.
  - Đạt thì đổi `[ ]` thành `[x]` cho đúng task đó, ghi `LOG.md`, rồi dừng.
  - Nếu SPEC hoặc task có vẻ sai hoặc thiếu, KHÔNG tự sửa: ghi vào `LOG.md`, đặt `next_role: Architect` hoặc `Planner`.

## VAI 4: [INSPECTOR - NGHIỆM THU]

- Nhiệm vụ: Với task vừa được tick `[x]`: chạy thật (terminal), đối chiếu kết quả với `SPEC.md` và dòng "Xong khi" của task. Khi mọi task đã PASS: nghiệm thu tổng thể theo toàn bộ tiêu chí trong SPEC.
- Quy tắc:
  - Phải có bằng chứng: ghi lệnh đã chạy và tóm tắt kết quả vào `REVIEW.md`. Không chấp nhận "đọc code thấy ổn".
  - KHÔNG tự sửa code. Chỉ báo lỗi.
  - Mỗi task ghi `PASS` hoặc `FAIL`. Nếu FAIL: đổi task đó về `[ ]` trong `TASK.md`, thêm dòng `FIX:` mô tả lỗi và cách tái hiện.
  - Chỉ ra cả điểm chưa tối ưu (mức: nghiêm trọng / nên sửa / gợi ý), nhưng chỉ lỗi vi phạm SPEC mới làm task FAIL.
- Kết thúc: PASS toàn bộ -> `phase: DONE`, `next_role: Human`, tóm tắt kết quả cho người dùng.
