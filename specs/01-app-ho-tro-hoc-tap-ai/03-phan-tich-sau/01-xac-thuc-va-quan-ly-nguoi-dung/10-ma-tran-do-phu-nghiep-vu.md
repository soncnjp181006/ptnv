# 10 — Ma Trận Độ Phủ Nghiệp Vụ: Xác Thực & Người Dùng

> Đo % kịch bản học tập + AI được bao phủ.

| Nhóm nghiệp vụ | Kịch bản cần | Đã bao phủ | Tỉ lệ | Ghi chú |
|----------------|--------------|------------|-------|---------|
| Đăng ký/đăng nhập | 8 | 8 | 100% | Email + Google OAuth |
| Hồ sơ học tập (`grade_level`, môn) | 4 | 3 | 75% | Thiếu import từ trường học |
| Phân vai Free/Paid/Admin | 5 | 5 | 100% | Gắn quota 20 câu AI/ngày |
| Khóa/mở/kháng nghị | 5 | 4 | 80% | Kháng nghị online bản rút gọn Sprint 1 |
| Audit & tuân thủ | 4 | 3 | 75% | Export CSV dời Sprint 2 |
| Bảo mật phiên | 6 | 6 | 100% | Rotation + reuse detection |
| **Tổng** | **32** | **29** | **91%** | Đủ sang thiết kế |

## Lỗ hổng & bù đắp
- G1: Chưa import lớp từ trường → bù bằng tự khai `grade_level/subjects`, giáo viên duyệt sau.
- G2: Kháng nghị chỉ qua email → Sprint 1 dùng form đơn giản, Sprint 2 thêm tracking ticket.
- G3: Chưa SSO trường học (SAML) → để backlog Paid-trường học.

## Ngưỡng gate
- ≥ 90% mới cho qua thiết kế. Hiện 91% → PASS có điều kiện (ghi nợ G1–G3 vào backlog).
