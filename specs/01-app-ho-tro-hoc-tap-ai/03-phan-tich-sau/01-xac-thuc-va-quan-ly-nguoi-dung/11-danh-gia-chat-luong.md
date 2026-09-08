# 11 — Đánh Giá Chất Lượng: Xác Thực & Người Dùng

> Tiêu chí chấm trên ngữ cảnh app học tập + AI freemium.

| Tiêu chí | Điểm/10 | Nhận xét |
|----------|---------|----------|
| Đầy đủ (đăng ký→khóa→xóa→audit) | 9 | Đủ vòng đời GUEST→DELETED |
| Nhất quán quota Free/Paid | 9 | 20 câu AI/ngày, 50MB, 3 Quiz/ngày dùng xuyên suốt |
| Khả kiểm thử | 8 | Mỗi BR có mã lỗi `*_REQUIRED/LOCKED/SUSPENDED` rõ |
| Bảo mật học sinh (vị thành niên) | 7 | Cần thêm consent PHHS cho <13 tuổi |
| Trải nghiệm onboarding | 8 | Google 1-chạm tốt; còn thiếu gợi ý môn học sau đăng ký |
| Audit & giải trình | 8 | Đủ suspend/audit, thiếu export |
| **Tổng** | **49/60 ≈ 82%** | Đạt gate ≥ 75% |

## Slop-flags cần tránh
- Không hard-code giá 99k/990k ở client — đọc từ `subscriptions.plans`.
- Không trả chi tiết `user tồn tại/không` ở forgot-password (chống enumerate).
- Không lưu `id_token` Google, chỉ lưu `sub`.

## Hành động cải tiến
- A1: Thêm checkbox consent PHHS + gợi ý `grade_level` sau register (Sprint 1).
- A2: Thêm export audit CSV cho Admin (Sprint 2).
- A3: Thêm captcha sau lần sai thứ 3 để giảm lock oan phòng máy tính chung.
