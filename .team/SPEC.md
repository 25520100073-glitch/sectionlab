# SPEC: SectionLab (tên tạm, chờ chốt)

Trạng thái: **ĐÃ DUYỆT** (`spec_approved: true` trong STATUS.md)

## 1. Mục tiêu

Xây dựng một thư viện Python mã nguồn mở, có kiểm chứng, cho tính toán tiết diện và cơ học vật liệu, gồm 3 lớp:

1. **Lớp lõi SBVL**: đặc trưng tiết diện, ứng suất, vòng Mohr, nội lực và độ võng dầm. Dùng để luyện và kiểm tra bài Olympic SBVL.
2. **Lớp BTCT**: phân tích tiết diện bê tông cốt thép bằng tương thích biến dạng: khả năng chịu uốn, biểu đồ tương tác P-M, quan hệ moment-curvature.
3. **Lớp báo cáo**: mỗi phép tính trả về cả vết tính toán (công thức, đầu vào, điều khoản tham chiếu) để xuất báo cáo tính tay có thể kiểm tra.

Người dùng: sinh viên kỹ thuật xây dựng và kỹ sư cần kiểm tra chéo kết quả phần mềm thương mại.
Mục đích của chủ dự án (theo thứ tự ưu tiên): CV xin vào công ty tư vấn kết cấu nước ngoài, ôn Olympic SBVL, nghiên cứu khoa học, có thể thương mại hóa.

## 2. Phạm vi

**LÀM:**
- **Tiết diện (Kiến trúc 2 cấp):**
  + Các hình học cơ bản có nghiệm giải tích chính xác 100%: `Rectangle`, `Circle`, `HollowCircle`, `ISection`, `TSection`, `ChannelSection`.
  + Lớp `CompositeSection`: tổ hợp các hình cơ bản bằng Định lý dời trục song song Steiner (hỗ trợ cả phần cộng và phần trừ/khoét lỗ).
  + Lớp `PolygonSection`: tính $A, x_c, y_c, I_x, I_y$ bằng Định lý Green cho đa giác đơn thuần túy (không tự cắt).
- **Đặc trưng tiết diện:** diện tích, trọng tâm, mô men quán tính ($I_x, I_y, I_{xy}$), mô men chống uốn ($W_x, W_y$), bán kính quán tính, góc xoay trục chính quán tính và mô men quán tính chính ($I_u, I_v$).
- **Ứng suất:** uốn, cắt theo Zhuravsky (CHỈ tính toán trên `CompositeSection` và các hình tiêu chuẩn; không áp dụng tính $\tau$ cho `PolygonSection` tự do), xoắn thuần túy, ứng suất tổ hợp, vòng Mohr (ứng suất chính, ứng suất tiếp cực đại).
- **Dầm tĩnh định:** dầm đơn giản 2 gối tựa (có/không console), dầm côngxôn ngàm 1 đầu. Tải trọng hỗ trợ: lực tập trung $P$, mô men tập trung $M$, tải phân bố đều $q$, tải phân bố bậc nhất (tam giác).
- **Nội lực và độ võng dầm:**
  + Thiết lập hàm giải tích đường đàn hồi liên tục $y(x)$ và góc xoay $\theta(x)$ bằng **Phương pháp thông số ban đầu Clebsch**.
  + Module kiểm chứng bằng **Phương pháp nhân biểu đồ Vereshchagin / tích phân Simpson** tại các điểm đặc trưng để xuất lời giải từng bước chuẩn format thi Olympic SBVL.
- BTCT: cốt thép rời rạc, mô hình vật liệu bê tông và thép, giải tương thích biến dạng, mô men giới hạn, biểu đồ tương tác P-M, moment-curvature
- Gói quy chuẩn tách rời (xem mục 4): TCVN 5574:2018 và AS 3600 cho v1.0, Eurocode 2 cho v1.1
- Giao diện: dòng lệnh (CLI) và ứng dụng web Streamlit để làm bản demo
- Xuất báo cáo: Markdown và PDF
- Tài liệu và README bằng **tiếng Anh** (hướng tới nhà tuyển dụng nước ngoài)

**KHÔNG LÀM (v1.0):**
- Giải khung hoặc hệ thanh phức tạp bằng phần tử hữu hạn; dầm siêu tĩnh (chỉ làm dầm tĩnh định)
- Tính ứng suất tiếp Zhuravsky cho đa giác tự do `PolygonSection` (chỉ làm cho hình cơ bản và `CompositeSection`)
- Thiết kế cắt, xoắn, vết nứt, độ võng dài hạn của BTCT
- Thiết kế móng, thép, gỗ
- Thay thế phần mềm thương mại hoặc tuyên bố đủ điều kiện dùng cho thiết kế thực tế

## 3. Ràng buộc

- Python 3.11 trở lên. Thư viện: numpy, scipy, matplotlib, pytest, ruff. Báo cáo: jinja2 (PDF qua công cụ do Planner chọn và nêu lý do).
- Quy ước đơn vị bắt buộc cho toàn bộ code: **N, mm, MPa; góc tính bằng radian**. Đổi đơn vị chỉ ở lớp CLI và giao diện.
- Mã nguồn các quy chuẩn bản quyền: **không chép nguyên văn nội dung hoặc bảng từ tiêu chuẩn**. Chỉ cài đặt công thức, đặt tên tham số của mình và ghi số hiệu điều khoản để tham chiếu.
- Mọi số liệu trong gói quy chuẩn phải do chủ dự án đối chiếu với bản tiêu chuẩn họ có quyền truy cập. Agent không tự điền hệ số từ trí nhớ.
- Làm việc theo PR: mỗi task một nhánh, một PR, nhánh chính được bảo vệ. Thư mục `venv/` không được theo dõi bởi git.
- Người dùng có thể làm 5 đến 10 giờ mỗi tuần, trình độ Python cơ bản. Code phải đơn giản, có chú thích, dễ đọc.

## 4. Kiến trúc

Kiến trúc phân lớp. Phụ thuộc chỉ đi một chiều từ trên xuống: `report` và giao diện phụ thuộc `codes`, `rc`, `mechanics`, `core`. `core` không phụ thuộc gì.

```
sectionlab/                      (tên repo và package chờ chốt)
├── pyproject.toml
├── src/sectionlab/
│   ├── core/          # hình học, đa giác, đặc trưng tiết diện, quy ước đơn vị, kiểu kết quả
│   ├── mechanics/     # SBVL: ứng suất, Mohr, dầm
│   ├── rc/            # vật liệu, tương tác P-M, moment-curvature (không phụ thuộc quy chuẩn)
│   ├── codes/         # gói quy chuẩn cắm rời: tcvn5574/, as3600/, ec2/
│   ├── report/        # từ vết tính toán -> Markdown / PDF
│   ├── cli.py
│   └── app/           # Streamlit
├── tests/
│   ├── unit/
│   └── benchmarks/    # bài chuẩn do chủ dự án tự giải tay (YAML/CSV)
├── docs/
├── .agents/           # skill và workflow của đội agent
└── .team/             # SPEC, TASK, STATUS, LOG, REVIEW
```

Bốn quyết định thiết kế chính:

1. **Vết tính toán (calculation trace).** Mọi hàm quan trọng trả về một đối tượng `Result` gồm `value`, `unit`, `formula`, `inputs`, `clause`, `steps`. Lớp báo cáo chỉ việc đọc vết này, nên không phải viết lại công thức.
2. **Quy chuẩn là gói cắm rời.** Bộ giải tương thích biến dạng của `rc/` dùng chung. Mỗi gói trong `codes/` chỉ cung cấp tham số vật liệu, hệ số an toàn hoặc hệ số giảm khả năng, giới hạn biến dạng và các quy tắc riêng của quy chuẩn đó. Thêm quy chuẩn mới không động đến lõi.
3. **Hình học tiết diện 2 cấp (Composite + Giải tích tuyệt đối):** Cấp 1 gồm các hình cơ bản chuẩn (`Rectangle`, `Circle`, `HollowCircle`, `ISection`, `TSection`, `ChannelSection`) và `CompositeSection` ghép nối bằng định lý dời trục Steiner (hỗ trợ cộng/trừ diện tích) để bảo đảm nghiệm giải tích chính xác 100% (sai số 0%) và tính ứng suất cắt Zhuravsky. Cấp 2 là `PolygonSection` dùng định lý Green chỉ tính các đặc trưng diện tích, trọng tâm và mô men quán tính cho đa giác đơn.
4. **Giải tích dầm song hành (Clebsch + Vereshchagin/Simpson):** Dùng phương pháp thông số ban đầu Clebsch để sinh hàm giải tích liên tục $y(x), \theta(x)$ trên toàn dầm, đồng thời tích hợp module nhân biểu đồ Vereshchagin/Simpson để kiểm chứng độ võng tại điểm đặc trưng và xuất vết tính toán chuẩn mực theo phong cách thi Olympic SBVL.

## 5. Tiêu chí nghiệm thu

**Chung (mọi giai đoạn):**
- `pytest` chạy xanh và `ruff check` không lỗi, chạy tự động bằng GitHub Actions trên mỗi PR.
- Độ phủ test >= 85% cho `core/` và `mechanics/`.
- Mọi hàm công khai có docstring ghi đơn vị đầu vào và đầu ra.

**Giai đoạn 0 (nền móng):**
- Thư mục `venv/` không còn được git theo dõi, có `.gitignore` và `pyproject.toml` cài được bằng `pip install -e .`.
- Các PR dọn dẹp do Jules tạo đã được duyệt và hợp nhất hoặc đóng, không còn PR mồ côi.
- Có README bản đầu bằng tiếng Anh và workflow CI chạy được.

**Giai đoạn 1 (lõi SBVL):**
- Có tệp `tests/benchmarks/sbvl.*` chứa tối thiểu 10 bài do chủ dự án giải tay. Kết quả code khớp đáp án chuẩn với sai số tương đối <= 0.1%.
- Mỗi bài benchmark có thể in lời giải từng bước bằng CLI.

**Giai đoạn 2 (BTCT):**
- Tiết diện chữ nhật một lớp cốt thép: mô men giới hạn khớp bài giải tay trong sai số <= 1%.
- Biểu đồ P-M của cột chữ nhật khớp bài giải tay tại 3 điểm kiểm soát (nén thuần túy, điểm cân bằng, uốn thuần túy) trong sai số <= 1%.
- Gói `tcvn5574` và `as3600` mỗi gói có ít nhất 3 bài benchmark do chủ dự án đối chiếu với tiêu chuẩn.
- Đổi gói quy chuẩn không yêu cầu sửa code trong `rc/`.

**Giai đoạn 3 (báo cáo và giao diện):**
- Một lệnh CLI tạo được báo cáo PDF cho ít nhất 2 ví dụ (một SBVL, một BTCT), mỗi công thức hiển thị kèm số hiệu điều khoản (nếu có).
- Ứng dụng Streamlit chạy được và có link demo công khai.

**Giai đoạn 4 (đóng gói):**
- README tiếng Anh có ảnh minh họa, hướng dẫn cài đặt, ví dụ, và mục "Validation" liệt kê các bài benchmark.
- Có phiên bản `v1.0.0` được gắn tag trên GitHub.

## 6. Lộ trình (ước lượng ở mức 5 đến 10 giờ mỗi tuần)

| Giai đoạn | Nội dung | Thời lượng ước tính |
|---|---|---|
| 0. Nền móng | Duyệt PR của Jules, bỏ venv, đặt tên dự án, CI, README đầu | 1 đến 2 tuần |
| 1. Lõi SBVL | Đặc trưng tiết diện, ứng suất, Mohr, dầm, CLI lời giải từng bước | 5 đến 6 tuần |
| 2. BTCT | Vật liệu, tương thích biến dạng, P-M, moment-curvature, gói TCVN rồi AS 3600 | 10 đến 12 tuần |
| 3. Báo cáo và giao diện | Vết tính toán thành PDF, Streamlit, link demo | 4 đến 5 tuần |
| 4. Đóng gói | Tài liệu, ảnh, benchmark công khai, tag v1.0.0 | 2 tuần |
| Mở rộng v1.1 | Gói Eurocode 2, các hướng nghiên cứu | sau v1.0 |

Tổng ước tính khoảng 6 tháng. Đây là ước lượng thô, có thể lệch tùy thời gian thi và độ khó thực tế của giai đoạn 2.

## 7. Giả định và rủi ro

- **Giả định:** chủ dự án có quyền truy cập bản TCVN 5574:2018, AS 3600 (và sau này Eurocode 2) qua trường hoặc mua. Nếu chưa có, gói quy chuẩn đó bị hoãn.
- **Rủi ro 1 (lớn nhất):** agent viết nhiều code mà chủ dự án không hiểu hết, gây khó khi phỏng vấn. Giảm thiểu: chủ dự án tự giải tay các bài benchmark và duyệt từng PR.
- **Rủi ro 2:** giai đoạn 2 khó hơn dự kiến (phi tuyến, hội tụ số). Giảm thiểu: chia nhỏ thành các bước kiểm tra độc lập, bắt đầu từ trường hợp đơn giản nhất.
- **Rủi ro 3:** tác vụ lặp hàng ngày của Jules (ví dụ tác vụ "Bolt") tạo PR không liên quan khi code còn ít. Giảm thiểu: tạm dừng cho đến khi có đủ code.
- **Rủi ro 4:** vấn đề bản quyền khi ghi điều khoản tiêu chuẩn. Giảm thiểu: chỉ ghi số hiệu điều khoản và công thức, không chép nguyên văn.
- **Lưu ý đạo đức nghề nghiệp:** README phải ghi rõ công cụ chỉ phục vụ học tập và kiểm tra chéo, không thay thế phán đoán của kỹ sư có chứng chỉ.
