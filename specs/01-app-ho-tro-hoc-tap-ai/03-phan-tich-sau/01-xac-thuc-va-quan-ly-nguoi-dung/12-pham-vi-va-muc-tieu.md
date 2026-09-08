# 12 — Phạm Vi & Mục Tiêu: Xác Thực & Người Dùng

## 1. Mục tiêu (SMART)
- O1: 95% learner đăng ký xong trong < 2 phút (email hoặc Google 1-chạm) trong Sprint 1.
- O2: 0 vụ rò password plaintext; 100% suspend có `reason_code` + audit.
- O3: Tỉ lệ verify email ≥ 70% nhờ giới hạn 5 câu AI/ngày khi chưa verify.
- O4: Hỗ trợ 10k concurrent login mùa khai giảng, p95 < 800ms.

## 2. Trong phạm vi (IN)
- Đăng ký/đăng nhập email + Google OAuth, verify, reset pass, refresh/logout.
- Hồ sơ học tập: `display_name, avatar, grade_level (1-12/ĐH), subjects[]`.
- Phân vai LearnerFree/LearnerPaid/Admin/Guest; quota Free 20 AI/ngày, 10 TL, 50MB, 3 Quiz/ngày.
- Khóa/mở/xóa mềm, audit 12 tháng, nâng/hạ Paid 99k/tháng–990k/năm.

## 3. Ngoài phạm vi (OUT)
- SSO trường học (SAML/LDAP), định danh eKYC, ví điện tử riêng.
- Phân quyền giáo viên/phụ huynh chi tiết (để module lớp học Sprint 3).
- Chat hỗ trợ trực tiếp trong auth.

## 4. Giả định & ràng buộc
- Học sinh dùng chung máy phòng tin → cần logout xa + cảnh báo phiên lạ.
- Trẻ <13 tuổi cần consent PHHS (tuân thủ địa phương).
- Múi giờ tính quota/reset là `Asia/Ho_Chi_Minh`.

## 5. Tiêu chí thành công
- Login success ≥ 98%, lock oan < 2%, audit đầy đủ 100% thao tác Admin.
