# KẾ HOẠCH CÔNG VIỆC: SectionLab

Tài liệu căn cứ: [.team/SPEC.md](file:///c:/Workspace/sectionlab/.team/SPEC.md) (Đã duyệt)
Quy ước đơn vị: N, mm, MPa, radian.

---

## GIAI ĐOẠN 0: NỀN MÓNG

- [x] T0.1: Thiết lập `.gitignore` chuẩn cho Python và workspace
  - File liên quan: `.gitignore`
  - Phụ thuộc: không có
  - Xong khi: File `.gitignore` ở thư mục gốc chứa các mục loại trừ bắt buộc `venv/`, `__pycache__/`, `.pytest_cache/`, `*.egg-info/`, `.ruff_cache/`. Lệnh `git check-ignore venv/` trả về exit code 0 và `git status --ignored` hiển thị `venv/` được bỏ qua.

- [x] T0.2: Khởi tạo cấu trúc package `src/sectionlab/` và `pyproject.toml`
  - File liên quan: `pyproject.toml`, `src/sectionlab/__init__.py`, `src/sectionlab/cli.py`, `src/sectionlab/core/__init__.py`, `src/sectionlab/mechanics/__init__.py`, `src/sectionlab/rc/__init__.py`, `src/sectionlab/codes/__init__.py`, `src/sectionlab/report/__init__.py`
  - Phụ thuộc: T0.1
  - Xong khi: Chạy `pip install -e .` thành công; thực thi `python -c "import sectionlab; print(sectionlab.__version__)"` và kiểm tra import được các subpackage `core`, `mechanics`, `rc`, `codes`, `report` cùng module `cli` mà không có lỗi.

- [x] T0.3: Thiết lập GitHub Actions CI workflow
  - File liên quan: `.github/workflows/ci.yml`
  - Phụ thuộc: T0.2
  - Xong khi: File `.github/workflows/ci.yml` tồn tại, kích hoạt trên push và pull_request; cấu hình kiểm tra Python >= 3.11, cài đặt package cùng dependencies kiểm thử và thực thi `ruff check .` cùng `pytest`.

- [x] T0.4: Soạn thảo `README.md` bản đầu bằng tiếng Anh
  - File liên quan: `README.md`
  - Phụ thuộc: T0.2
  - Xong khi: File `README.md` bằng tiếng Anh có tên dự án (SectionLab), mô tả 1 dòng, hướng dẫn cài đặt (`pip install -e .`), các mục giữ chỗ theo SPEC (kiến trúc 3 lớp, quy ước đơn vị N-mm-MPa, disclaimer kỹ thuật).
