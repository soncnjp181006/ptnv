# 06 — Danh Sách Quyết Định: Xác Thực & Quản Lý Người Dùng

> Mọi DEC dưới đây `Status: PROPOSED`, `Source: AGENT_INFERENCE` vì user chưa chốt chi tiết.

| ID | Quyết định | Lựa chọn đề xuất | Phương án thay thế | Ảnh hưởng | Status / Source |
|----|------------|------------------|--------------------|-----------|-----------------|
| DEC-AUTH-01 | Kênh đăng nhập | Email/password + Google OAuth | Thêm Facebook/Zalo OA | Tăng phủ học sinh, tốn tích hợp | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-02 | Bắt buộc verify email? | Mềm: chưa verify vẫn dùng 5 câu AI/ngày | Cứng: chặn hoàn toàn | Cân bằng spam vs onboarding | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-03 | Thời lượng token | Access 15p + refresh 30d xoay vòng | Access 60p cho đỡ login lại | Bảo mật vs tiện lợi | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-04 | Khóa sai pass | 5 lần → 15p | 10 lần → 30p + captcha | Chống brute-force | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-05 | Gói Paid | Tháng 99k / Năm 990k (đã chốt giá) | Thêm gói Học kỳ 499k | Doanh thu vs phức tạp billing | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-06 | Lưu audit bao lâu | 12 tháng rồi ẩn danh | 24 tháng | Pháp lý vs lưu trữ | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-07 | Xóa tài khoản | Soft-delete 30 ngày rồi purge | Xóa cứng ngay | Khôi phục nhầm vs quyền riêng tư | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-08 | Liên kết đa OAuth | 1 email ↔ 1 Google sub | Cho nhiều Google/account | Tiện vs trùng quota Free | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-09 | Hồ sơ học tập | `grade_level, subjects[]` tự khai | Đồng bộ từ trường học | Cá nhân hóa AI vs xác thực | PROPOSED / AGENT_INFERENCE |
| DEC-AUTH-10 | Admin phân quyền | Admin tập trung khóa/mở | Thêm Moderator trường học | Kiểm duyệt vs phân tán quyền | PROPOSED / AGENT_INFERENCE |

## Ghi chú trình duyệt
- Cần user chốt DEC-AUTH-02 và DEC-AUTH-05 trước Sprint 1 để khóa luồng onboarding và billing.
- Các DEC còn lại có thể chốt trong thiết kế chi tiết mà không chặn code auth lõi.
