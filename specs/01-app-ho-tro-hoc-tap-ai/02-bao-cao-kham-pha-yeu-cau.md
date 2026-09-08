# Báo Cáo Khám Phá Yêu Cầu (02-bao-cao-kham-pha-yeu-cau.md)

## 1. Bối Cảnh & Vấn Đề

Người dùng muốn xây dựng ứng dụng hỗ trợ học tập có tích hợp AI, vận hành theo mô hình freemium (gói Free + gói trả phí). Yêu cầu ban đầu rất khái quát, chưa chỉ rõ đối tượng, môn học, nền tảng hay giới hạn gói.

Hệ thống phân tích theo Core Engine: `Goal` ➔ `Actor` ➔ `Capability` ➔ `Process` ➔ `Resource` ➔ `Rule` ➔ `Decision`, không hard-code domain ngoài những gì User xác nhận. Mọi suy luận Best Practice gắn `Status: PROPOSED`, `Source: AGENT_INFERENCE`.

## 2. Phân Tích Ý Định (Intent & Goal Analysis)

- **Problem**: Người học thiếu gia sư cá nhân 24/7, tài liệu phân tán, khó tự luyện tập và đo lường tiến bộ.
- **Need**: Nơi lưu tài liệu tập trung, hỏi đáp AI theo ngữ cảnh tài liệu, tự sinh Quiz/Flashcard, theo dõi tiến độ.
- **Desired outcome**: Học nhanh hơn, hiểu sâu hơn, duy trì thói quen học hằng ngày.
- **Business goal (nhà cung cấp)**: Thu hút người dùng Free, chuyển đổi một phần sang Paid để bù chi phí `LLM` + hạ tầng.
- **Success metric (đề xuất)**:
  - Kích hoạt: >= 60% tài khoản mới tải >= 1 tài liệu trong 7 ngày.
  - Gắn bó: DAU/MAU >= 25%, streak trung bình >= 4 ngày.
  - Chuyển đổi: Free ➔ Paid >= 3-5% sau 60 ngày.
  - Chất lượng AI: >= 80% câu trả lời được đánh giá hữu ích, tỷ lệ báo cáo sai < 5%.
- **Constraint**:
  - Chi phí `LLM` biến đổi theo usage, bắt buộc có quota + kiểm soát lạm dụng.
  - Tuân thủ quyền riêng tư tài liệu học tập, bản quyền tài liệu tải lên.
  - Ngôn ngữ chính tiếng Việt trong MVP.
- **Non-goal**: Không làm sàn khóa học đa giảng viên, không live-class, không chứng chỉ pháp lý trong MVP.

## 3. Actor & Stakeholder

| Actor | Mục tiêu | Ghi chú |
| :--- | :--- | :--- |
| Học viên Free (`LearnerFree`) | Học miễn phí với giới hạn hợp lý | Actor chính MVP |
| Học viên Paid (`LearnerPaid`) | Hạn mức cao, tính năng nâng cao | Nguồn doanh thu |
| Khách vãng lai (`Guest`) | Dùng thử giới hạn trước khi đăng ký | Chuyển đổi đăng ký |
| Quản trị viên (`Admin`) | Quản lý gói, giá, nội dung vi phạm, vận hành | Phạm vi nội bộ |
| Hệ thống ngoài | Cổng thanh toán, nhà cung cấp `LLM`, lưu trữ tệp | `External dependency` |

Chi tiết quyền hạn xem `03-phan-tich-sau/01-xac-thuc-va-quan-ly-nguoi-dung/`.

## 4. Capability Candidates

| Mã | Capability | Outcome | Actor chính |
| :--- | :--- | :--- | :--- |
| CAP-01 | Xác thực và quản lý người dùng | Truy cập an toàn, phân biệt Free/Paid | Mọi actor |
| CAP-02 | Quản lý tài liệu học tập | Kho tri thức cá nhân có cấu trúc | Learner |
| CAP-03 | Gia sư AI hỏi đáp | Giải đáp theo ngữ cảnh 24/7 | Learner |
| CAP-04 | Sinh và luyện Quiz/Flashcard | Tự kiểm tra, ghi nhớ dài hạn | Learner |
| CAP-05 | Gói dịch vụ và thanh toán | Monetization bền vững, kiểm soát quota | Learner, Admin |
| CAP-06 | Theo dõi tiến độ | Động lực duy trì, nhìn thấy tiến bộ | Learner |

Mỗi Capability phân rã thành Process ➔ Use Case ➔ Scenario trong `03-phan-tich-sau/`.

## 5. Phạm Vi Sơ Bộ & Ranh Giới

- **In-Scope MVP**: 6 capability trên, nền tảng Web responsive, thanh toán 1 cổng trong nước + 1 cổng thẻ quốc tế ở mức policy.
- **Out-of-Scope MVP**: Mobile native, offline-first sâu, live-class, marketplace khóa học, chấm thi pháp lý.
- **Ranh giới**: Không tự quyết hãng `LLM` hay kiến trúc trong Phase A.

## 6. Rủi Ro & Điểm Mù Đã Phát Hiện

- Quota Free quá rộng ➔ lỗ chi phí `LLM`. Mitigation: quota mặc định + rate limit + cảnh báo.
- AI trả lời sai (hallucination) ➔ mất niềm tin. Mitigation: trích dẫn nguồn, nút báo cáo sai, disclaimer.
- Vi phạm bản quyền tài liệu tải lên ➔ rủi ro pháp lý. Mitigation: điều khoản sử dụng, giới hạn chia sẻ công khai trong MVP.
- Gian lận thanh toán / gia hạn lỗi ➔ thất thu. Mitigation: idempotency, đối soát, webhook retry.

## 7. Quyết Định Đề Xuất (Chờ User Chốt Song Song, Không Chặn Thiết Kế)

- `DEC-001` Đối tượng MVP: học sinh/sinh viên phổ thông đa môn. `Status: PROPOSED`.
- `DEC-002` Nền tảng MVP: Web responsive. `Status: PROPOSED`.
- `DEC-003` Quota Free: 20 câu AI/ngày, 10 tài liệu, 50MB, 3 bộ Quiz/ngày. `Status: PROPOSED`.
- `DEC-004` Gói Paid: Tháng 99.000 VND, Năm 990.000 VND. `Status: PROPOSED`.
- `DEC-005` Riêng tư dữ liệu: không dùng tài liệu riêng để train chung khi chưa đồng ý. `Status: PROPOSED`.
- `DEC-006` Kiểm duyệt: lọc độc hại + báo cáo sai. `Status: PROPOSED`.

## 8. Kết Luận Chặng A1

Đạt điều kiện vào A2. Toàn bộ Unknowns đã chuyển thành `PROPOSED` có trace, không còn P0 `UNRESOLVED` chặn lõi. Tiếp tục phân tích sâu 6 nghiệp vụ con theo chuẩn 19 tệp.
