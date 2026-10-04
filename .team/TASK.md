# KẾ HOẠCH CÔNG VIỆC: SectionLab

Tài liệu căn cứ: [.team/SPEC.md](file:///c:/Workspace/01_Projects/.team/SPEC.md) (Đã duyệt)
Quy ước đơn vị: N, mm, MPa, radian.

---

## GIAI ĐOẠN 0: NỀN MÓNG

- [x] T0.1: Thiết lập `.gitignore` chuẩn cho Python và workspace
  - File liên quan: `.gitignore`
  - Phụ thuộc: không có
  - Xong khi: File `.gitignore` được tạo ở thư mục gốc, loại trừ `venv/`, `__pycache__/`, `.pytest_cache/`, `*.egg-info/`, `.ruff_cache/`. Lệnh `git status --ignored` hiển thị `venv/` bị ignore và không có file rác bị theo dõi.

- [x] T0.2: Khởi tạo cấu trúc package `src/sectionlab` và `pyproject.toml`
  - File liên quan: `pyproject.toml`, `src/sectionlab/__init__.py`
  - Phụ thuộc: T0.1
  - Xong khi: Chạy `pip install -e .` thành công trong venv; `python -c "import sectionlab; print(sectionlab.__version__)"` in ra version không lỗi.

- [x] T0.3: Thiết lập GitHub Actions CI workflow
  - File liên quan: `.github/workflows/ci.yml`
  - Phụ thuộc: T0.2
  - Xong khi: File `.github/workflows/ci.yml` tồn tại, cấu hình ma trận test Python >= 3.11, cài đặt package và chạy `ruff check` cùng `pytest`.

- [x] T0.4: Soạn thảo `README.md` bản đầu bằng tiếng Anh
  - File liên quan: `README.md`
  - Phụ thuộc: T0.2
  - Xong khi: File `README.md` hoàn chỉnh bằng tiếng Anh, mô tả mục tiêu thư viện SectionLab, kiến trúc 3 lớp, hướng dẫn cài đặt và chạy thử.

---

## GIAI ĐOẠN 1: LÕI SBVL (HÌNH HỌC, ỨNG SUẤT, DẦM)

### Nhóm 1: Nền tảng kết quả & Hình học tiết diện (core)

- [x] T1.1: Xây dựng module `sectionlab.core.result` định nghĩa đối tượng `Result` cho calculation trace
  - File liên quan: `src/sectionlab/core/result.py`, `tests/unit/test_result.py`
  - Phụ thuộc: T0.2
  - Xong khi: `pytest tests/unit/test_result.py` pass 100%; đối tượng `Result` lưu trữ đầy đủ `value`, `unit`, `formula`, `inputs`, `clause`, `steps`, có method biểu diễn string/dict rõ ràng.

- [x] T1.2: Xây dựng lớp cơ sở `Section` và các hình học cơ bản giải tích (`Rectangle`, `Circle`, `HollowCircle`)
  - File liên quan: `src/sectionlab/core/geometry.py`, `tests/unit/test_basic_geometry.py`
  - Phụ thuộc: T1.1
  - Xong khi: `pytest tests/unit/test_basic_geometry.py` pass 100%; tính đúng $A, x_c, y_c, I_x, I_y, W_x, W_y, i_x, i_y$ giải tích với sai số 0% cho hình chữ nhật, hình tròn đặc và hình tròn rỗng.

- [x] T1.3: Xây dựng các hình học cơ bản giải tích tiếp theo (`ISection`, `TSection`, `ChannelSection`)
  - File liên quan: `src/sectionlab/core/geometry.py`, `tests/unit/test_standard_sections.py`
  - Phụ thuộc: T1.2
  - Xong khi: `pytest tests/unit/test_standard_sections.py` pass 100%; tính chính xác các đặc trưng hình học cho thép hình I, T, [ (Channel) theo kích thước hình học chuẩn.

- [x] T1.4: Xây dựng lớp `CompositeSection` tổ hợp hình học bằng Định lý dời trục Steiner
  - File liên quan: `src/sectionlab/core/geometry.py`, `tests/unit/test_composite_section.py`
  - Phụ thuộc: T1.3
  - Xong khi: `pytest tests/unit/test_composite_section.py` pass 100%; hỗ trợ ghép nhiều hình (cộng diện tích) và khoét lỗ (trừ diện tích); tính chính xác $A, x_c, y_c, I_x, I_y, I_{xy}$, góc xoay trục chính quán tính $\alpha_0$ và mô men quán tính chính $I_u, I_v$.

- [x] T1.5: Xây dựng lớp `PolygonSection` dùng Định lý Green cho đa giác đơn
  - File liên quan: `src/sectionlab/core/polygon.py`, `tests/unit/test_polygon.py`
  - Phụ thuộc: T1.2
  - Xong khi: `pytest tests/unit/test_polygon.py` pass 100%; tính đúng diện tích, trọng tâm và mô men quán tính bậc hai cho đa giác lồi và lõm không tự cắt; kiểm chứng khớp với hình chữ nhật giải tích.

### Nhóm 2: Cơ học & Ứng suất (mechanics)

- [x] T1.6: Xây dựng module ứng suất `sectionlab.mechanics.stress` (Uốn $\sigma_z$, Xoắn thuần túy, và Cắt Zhuravsky)
  - File liên quan: `src/sectionlab/mechanics/stress.py`, `tests/unit/test_stress.py`
  - Phụ thuộc: T1.4
  - Xong khi: `pytest tests/unit/test_stress.py` pass 100%; tính đúng phân bố ứng suất pháp $\sigma_z(y)$ do mô men uốn $M_x$, ứng suất xoắn $\tau$, và ứng suất tiếp cắt theo công thức Zhuravsky $\tau(y) = \frac{Q \cdot S_x^*(y)}{b(y) \cdot I_x}$ trên các hình tiêu chuẩn và `CompositeSection`.

- [x] T1.7: Xây dựng module phân tích trạng thái ứng suất phẳng và vòng tròn Mohr
  - File liên quan: `src/sectionlab/mechanics/mohr.py`, `tests/unit/test_mohr.py`
  - Phụ thuộc: T1.6
  - Xong khi: `pytest tests/unit/test_mohr.py` pass 100%; từ $(\sigma_x, \sigma_y, \tau_{xy})$ tính đúng ứng suất chính $\sigma_1, \sigma_2$, phương chính $\theta_p$, ứng suất tiếp cực đại $\tau_{\max}$ và vẽ được vòng tròn Mohr bằng matplotlib.

### Nhóm 3: Dầm tĩnh định & Độ võng (mechanics/beam)

- [x] T1.8: Xây dựng mô hình dầm tĩnh định và biểu đồ nội lực $Q(x), M(x)$
  - File liên quan: `src/sectionlab/mechanics/beam.py`, `tests/unit/test_beam.py`
  - Phụ thuộc: T1.1
  - Xong khi: `pytest tests/unit/test_beam.py` pass 100%; giải đúng phản lực liên kết và thiết lập hàm nội lực cắt $Q(x)$, uốn $M(x)$ cho dầm 2 gối tựa (có/không console), dầm ngàm cantilever dưới các tải trọng: lực tập trung $P$, ngẫu lực $M$, tải phân bố đều $q$, tải tam giác.

- [x] T1.9: Cài đặt Phương pháp thông số ban đầu Clebsch xác định đường đàn hồi liên tục $y(x), \theta(x)$
  - File liên quan: `src/sectionlab/mechanics/beam.py`, `tests/unit/test_clebsch.py`
  - Phụ thuộc: T1.8
  - Xong khi: `pytest tests/unit/test_clebsch.py` pass 100%; tìm chính xác các thông số ban đầu $y_0, \theta_0$ từ điều kiện biên và trả về hàm giải tích liên tục cho góc xoay $\theta(x)$ và độ võng $y(x)$.

- [x] T1.10: Xây dựng module kiểm chứng độ võng bằng Phương pháp nhân biểu đồ Vereshchagin / Simpson
  - File liên quan: `src/sectionlab/mechanics/vereshchagin.py`, `tests/unit/test_vereshchagin.py`
  - Phụ thuộc: T1.8, T1.9
  - Xong khi: `pytest tests/unit/test_vereshchagin.py` pass 100%; tính độ võng và góc xoay tại các điểm đặc trưng bằng nhân biểu đồ $(\bar{M} \times M_P)$ khớp với kết quả phương pháp Clebsch với sai số $< 0.01\%$, xuất vết tính toán từng bước.

### Nhóm 4: Benchmark Olympic & CLI Lời giải từng bước

- [x] T1.11: Xây dựng bộ test benchmark 10 bài toán SBVL giải tay chuẩn
  - File liên quan: `tests/benchmarks/test_sbvl_benchmarks.py`, `src/sectionlab/benchmarks.py`
  - Phụ thuộc: T1.4, T1.6, T1.7, T1.9, T1.10
  - Xong khi: `pytest tests/benchmarks/test_sbvl_benchmarks.py` pass 100% với tối thiểu 10 bài toán tổng hợp (đặc trưng tiết diện phức, ứng suất Zhuravsky, dầm tĩnh định, độ võng Clebsch/Vereshchagin); kết quả khớp lời giải tay với sai số tương đối $\le 0.1\%$.

- [x] T1.12: Xây dựng giao diện dòng lệnh CLI in lời giải từng bước
  - File liên quan: `src/sectionlab/cli.py`, `tests/unit/test_cli.py`
  - Phụ thuộc: T1.11
  - Xong khi: Chạy lệnh `sectionlab solve --benchmark <id>` in ra đầy đủ vết tính toán (đầu vào, công thức, các bước trung gian, kết quả kèm đơn vị); kiểm tra test CLI pass.

---

## GIAI ĐOẠN 2: LÕI BÊ TÔNG CỐT THÉP PHI TUYẾN (RC & CODES)

### Nhóm 1: Mô hình vật liệu & Chia thớ sợi tiết diện (rc/materials, rc/section)

- [x] T2.1: Xây dựng mô hình vật liệu phi tuyến cho bê tông và cốt thép (`sectionlab.rc.materials`)
  - File liên quan: `src/sectionlab/rc/materials.py`, `tests/unit/test_rc_materials.py`
  - Phụ thuộc: T1.1
  - Xong khi: `pytest tests/unit/test_rc_materials.py` pass 100%; cài đặt đầy đủ `SteelMaterial` (đàn hồi - dẻo lý tưởng và tái bền tuyến tính, kéo/nén đối xứng) và `ConcreteMaterial` (quan hệ $\sigma_c(\varepsilon_c)$ parabol-chữ nhật/Hognestad phi tuyến cùng tham số khối ứng suất chữ nhật tương đương Whitney $\alpha_1, \beta_1$, bỏ qua bê tông chịu kéo hoặc xét $f_{ct}$ tùy chọn).

- [x] T2.2: Xây dựng mô hình tiết diện BTCT rời rạc hóa cốt thép và chia thớ sợi Fiber Section (`sectionlab.rc.section`)
  - File liên quan: `src/sectionlab/rc/section.py`, `tests/unit/test_rc_section.py`
  - Phụ thuộc: T1.2, T2.1
  - Xong khi: `pytest tests/unit/test_rc_section.py` pass 100%; định nghĩa `RebarLayer` (diện tích $A_s$ hoặc số thanh + đường kính, tọa độ $y$ / chiều sâu $d$), `ConcreteFiber`, và `RCSection` (kết hợp tiết diện bê tông chữ nhật/chữ T, các lớp cốt thép, tự động chia $N$ thớ sợi bê tông theo chiều cao tiết diện và trừ diện tích bê tông bị cốt thép chiếm chỗ).

### Nhóm 2: Giải thuật cân bằng phi tuyến, Biểu đồ tương tác P-M & Moment-Curvature (rc/solver, rc/interaction, rc/moment_curvature)

- [x] T2.3: Xây dựng bộ giải tương thích biến dạng & cân bằng phi tuyến Newton-Raphson (`sectionlab.rc.solver`)
  - File liên quan: `src/sectionlab/rc/solver.py`, `tests/unit/test_rc_solver.py`
  - Phụ thuộc: T2.2
  - Xong khi: `pytest tests/unit/test_rc_solver.py` pass 100%; giải chính xác chiều cao vùng nén $c$ (trục trung hòa) từ phương trình cân bằng lực dọc $\sum N(c) = P_{\text{target}}$ bằng thuật toán lặp tiếp tuyến Newton-Raphson kết hợp chia đôi/Brent đảm bảo hội tụ tuyệt đối; tính khả năng chịu uốn giới hạn $M_u$ cho tiết diện chữ nhật cốt đơn và cốt kép khớp bài giải tay với sai số $\le 1\%$, trả về đầy đủ vết `Result`.

- [x] T2.4: Xây dựng bộ sinh biểu đồ tương tác $P-M$ ($N-M_x-M_y$) (`sectionlab.rc.interaction`)
  - File liên quan: `src/sectionlab/rc/interaction.py`, `tests/unit/test_rc_interaction.py`
  - Phụ thuộc: T2.3
  - Xong khi: `pytest tests/unit/test_rc_interaction.py` pass 100%; quét biến dạng từ nén thuần túy ($P_0$), qua điểm phá hoại cân bằng ($P_b, M_b$), uốn thuần túy ($M_0$) đến kéo thuần túy ($P_t$); kiểm chứng tại 3 điểm kiểm soát (nén thuần túy, điểm cân bằng, uốn thuần túy) khớp bài giải tay với sai số $\le 1\%$; hỗ trợ kiểm tra điểm tải trọng $(P_u, M_u)$ nằm trong miền an toàn và tương tác uốn xiên 2 phương $N-M_x-M_y$ (công thức Bresler / quét mặt phẳng).

- [x] T2.5: Xây dựng module phân tích quan hệ Mô men - Độ cong phi tuyến $M-\kappa$ (`sectionlab.rc.moment_curvature`)
  - File liên quan: `src/sectionlab/rc/moment_curvature.py`, `tests/unit/test_moment_curvature.py`
  - Phụ thuộc: T2.3
  - Xong khi: `pytest tests/unit/test_moment_curvature.py` pass 100%; giải đường cong $M-\kappa$ bằng tích phân thớ sợi (fiber integration) và lặp Newton-Raphson tìm $\varepsilon_0$ tại mỗi bước độ cong $\kappa$ dưới lực dọc $N$ không đổi; xác định rõ các điểm đặc trưng: nứt bê tông ($M_{cr}, \kappa_{cr}$), chảy dẻo cốt thép ($M_y, \kappa_y$), và cực hạn ($M_u, \kappa_u$, độ dẻo $\mu_\kappa = \kappa_u / \kappa_y$).

### Nhóm 3: Gói quy chuẩn cắm rời TCVN 5574:2018 & AS 3600:2018 (codes)

- [x] T2.6: Xây dựng giao diện quy chuẩn cắm rời và gói `sectionlab.codes.tcvn5574` (TCVN 5574:2018)
  - File liên quan: `src/sectionlab/codes/base.py`, `src/sectionlab/codes/tcvn5574/__init__.py`, `tests/benchmarks/test_tcvn5574_benchmarks.py`
  - Phụ thuộc: T2.3, T2.4
  - Xong khi: `pytest tests/benchmarks/test_tcvn5574_benchmarks.py` pass 100%; định nghĩa giao diện trừu tượng `DesignCode` tại `codes/base.py`; cài đặt `TCVN5574_2018` (cấp độ bền bê tông B15–B60, cốt thép CB240-T, CB300-V, CB400-V, CB500-V, hệ số $\gamma_b, \gamma_s$, giới hạn vùng nén tương đối $\xi_R$, $\varepsilon_{b2} = 0.0035$, ghi rõ số hiệu điều khoản TCVN 5574:2018 mà không chép nguyên văn); có ít nhất 3 bài benchmark giải tay đối chiếu tiêu chuẩn với sai số $\le 1\%$.

- [x] T2.7: Xây dựng gói quy chuẩn `sectionlab.codes.as3600` (AS 3600:2018) và kiểm chứng tính độc lập của `rc/`
  - File liên quan: `src/sectionlab/codes/as3600/__init__.py`, `tests/benchmarks/test_as3600_benchmarks.py`
  - Phụ thuộc: T2.6
  - Xong khi: `pytest tests/benchmarks/test_as3600_benchmarks.py` pass 100%; cài đặt `AS3600_2018` ($f'_c$ 25–100 MPa, hệ số khối ứng suất $\alpha_2 = \max(0.67, 0.85 - 0.0015 f'_c)$, $\gamma = \max(0.67, 0.97 - 0.0025 f'_c)$, $\varepsilon_{cu} = 0.003$, hệ số giảm khả năng chịu lực $\phi$ theo trạng thái kéo/nén, tham chiếu điều khoản AS 3600:2018); có ít nhất 3 bài benchmark giải tay với sai số $\le 1\%$ và test chứng minh chuyển đổi giữa `TCVN5574_2018` và `AS3600_2018` không cần sửa bất kỳ dòng code nào trong `src/sectionlab/rc/`.

- [x] T2.8: Tích hợp các bài toán benchmark BTCT (TCVN 5574 & AS 3600) vào CLI và hoàn thiện xuất pakage `rc`, `codes`
  - File liên quan: `src/sectionlab/rc/__init__.py`, `src/sectionlab/codes/__init__.py`, `src/sectionlab/benchmarks.py`, `src/sectionlab/cli.py`, `tests/unit/test_cli.py`
  - Phụ thuộc: T2.5, T2.6, T2.7
  - Xong khi: `pytest` toàn bộ dự án pass 100%, độ phủ test toàn bộ $\ge 85\%$, `ruff check .` 0 lỗi, và lệnh `sectionlab solve --benchmark <id>` xuất được vết tính toán chi tiết cho cả bài toán SBVL (Giai đoạn 1) và BTCT theo TCVN 5574 / AS 3600 (Giai đoạn 2).

