# 02 — Báo Cáo Khám Phá Yêu Cầu (A1 Discovery)

> Ứng dụng học tập có AI Agent hỗ trợ (Toán, Lý, Hóa, Sinh)

Phiên bản: v1.0.0
Ngày tạo: 2026-09-08
Trạng thái: DRAFT — chờ xác nhận từ User

---

## 1. Problem

Học sinh cần một công cụ học tập tập trung cho 4 môn khoa học tự nhiên (Toán, Lý, Hóa, Sinh) với sự hỗ trợ của AI, đồng thời có môi trường cộng đồng để trao đổi, chia sẻ tài liệu và đề thi.

Vấn đề cốt lõi:
- Học sinh không có nơi tập trung lưu trữ, quản lý và sinh đề tương tự từ đề thi thực tế.
- Không có công cụ AI chụp đề → giải → tạo đề tương tự một cách có cấu trúc.
- Cộng đồng học tập phân mảnh, thiếu không gian trao đổi có tổ chức.
- Thiếu hệ thống pricing linh hoạt để mở rộng từ free sang paid.

---

## 2. Need

| Need | Mô tả |
|------|-------|
| Hỗ trợ học tập thông minh | AI giúp giải bài, giải thích, sinh đề tương tự. |
| Lưu trữ đề có cấu trúc | Học sinh chụp đề, hệ thống OCR + AI lưu trữ có metadata. |
| Tạo kho đề cá nhân & công khai | User duyệt đề AI sinh ra, lưu vào kho riêng hoặc chia sẻ. |
| Cộng đồng trao đổi | Không gian hỏi đáp, chia sẻ đề, nhóm học. |
| Monetization linh hoạt | Gói Free/Plus/Pro để khuyến khích upgrade. |

---

## 3. Goal

Xây dựng một nền tảng học tập AI-first cho Toán, Lý, Hóa, Sinh với:
- OCR + AI hỗ trợ giải và sinh đề.
- Kho đề phong phú, có thể tìm kiếm và lọc theo môn, chương, độ khó.
- Cộng đồng tích hợp để trao đổi, hỏi đáp, chia sẻ.
- Freemium pricing model rõ ràng.

Business goal: Trở thành công cụ học tập ưu tiên cho học sinh từ cấp Tiểu học đến Đại học, trong đó tập trung vào Toán, Lý, Hóa, Sinh, với sự hỗ trợ của AI và cộng đồng trao đổi.

Success metric (dự kiến):
- Số đề trong kho đề.
- Tỷ lệ conversion Free → Plus/Pro.
- engagement: số lần chụp đề/ngày, số post/comment trong community.
- Retention: DAU/MAU.

---

## 4. Desired Outcome

- Học sinh chụp đề bằng điện thoại, nhận giải chi tiết trong vài giây.
- Học sinh duyệt đề tương tự do AI sinh, chỉnh sửa nếu cần, lưu vào kho đề cá nhân.
- Giáo viên có thể đóng góp đề vào kho công khai, kiểm soát chất lượng.
- Học sinh tham gia cộng đồng, hỏi đáp, chia sẻ kinh nghiệm.
- User trả phí để mở khóa dung lượng lớn hơn, tính năng AI nâng cao, và quyền community cao cấp.

---

## 5. Actors

| Actor | Vai trò | Mục tiêu |
|--------|---------|----------|
| Học sinh (Student) | Người dùng chính | Học tập, luyện đề, hỏi đáp từ cấp Tiểu học đến Đại học. |
| Giáo viên (Teacher) | Contributor / Moderator | Đóng góp đề, giải đáp, kiểm soát chất lượng. |
| Admin | Quản trị hệ thống | Quản lý user, content, pricing, moderation. |
| AI Agent | Hỗ trợ tự động | OCR, giải bài, sinh đề, gợi ý, moderation assistant. |
| System / External API | Hạ tầng | LLM API, OCR API, Payment gateway, Notification. |

> **DEC-001 CONFIRMED:** Ứng dụng hỗ trợ **mọi cấp học** (không giới hạn lớp). Điều này mở rộng scope nội dung, độ khó AI và curriculum mapping sang toàn bộ hệ thống giáo dục.

---

## 6. Constraints

| Constraint | Loại | Mô tả |
|------------|------|-------|
| Curriculum alignment | BUSINESS | Nội dung phải phù hợp với chương trình THCS/THPT Việt Nam. |
| Privacy & Data | COMPLIANCE | Dữ liệu học sinh phải tuân thủ quy định bảo vệ dữ liệu cá nhân. |
| AI Quality | TECHNICAL | Độ chính xác OCR + giải bài phải đủ cao để học sinh tin dùng. |
| Cost | OPERATIONAL | Chi phí LLM/OCR phải kiểm soát được theo pricing tier. |
| Moderation | OPERATIONAL | Community cần cơ chế moderation để tránh spam, sai lệch kiến thức. |
| Mobile-first UX | TECHNICAL | Trải nghiệm mobile phải mượt, capture nhanh. |

---

## 7. Non-goals

- Không phải LMS (Learning Management System) đầy đủ.
- Không phải nền tảng dạy học trực tuyến có video course.
- Không xử lý thanh toán phức tạp (multi-currency, installment...).
- Không hỗ trợ chương trình Đại học ban đầu.
- Không có livestream / real-time tutoring.

---

## 8. Initial Scope Boundary

### Cốt lõi (Core) — Phải có trong MVP
1. OCR + AI giải đề (chụp đề → giải chi tiết).
2. Sinh đề tương tự + duyệt + lưu vào kho đề.
3. Kho đề với search, filter theo môn, lớp, chương.
4. Community cơ bản: hỏi đáp, bình luận, chia sẻ đề.
5. Pricing Free/Plus/Pro với feature gates rõ ràng.

### Nâng cao (Stretch) — Sau MVP
1. Nhóm học (study group) có lịch, assignment.
2. Analytics cá nhân: điểm mạnh/yếu, đề xuất lộ trình.
3. Gamification: streak, badge, leaderboard.
4. AI tutor theo cá nhân hóa.
5. Multi-language.

---

## 9. Decision Checkpoints Cần User Xác Nhận

Agent đã tạo 10 câu hỏi trong `00-nhat-ky-quyet-dinh.md`:
- Q-001: Phân khúc lớp học (P0)
- Q-002: Cơ chế duyệt đề tương tự (P0)
- Q-003: Phạm vi community (P1)
- Q-004: Phân biệt gói Plus/Pro (P0)
- Q-005: Lưu trữ đề trên lớp (P1)
- Q-006: Nền tảng + ngôn ngữ (P0)
- Q-007: Quyền publish + moderation (P1)
- Q-008: OCR metadata (P1)
- Q-009: Data ownership (P1)
- Q-010: Community theo gói pricing (P1)

**Trạng thái hiện tại:** Đang ở Decision Checkpoint. Yêu cầu User trả lời Q-001 trước.

---


