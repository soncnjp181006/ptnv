# Tổng Hợp Toàn Bộ Yêu Cầu — App Hỗ Trợ Học Tập + AI (Freemium)

## 1. Tầm Nhìn & Mục Tiêu

Xây dựng ứng dụng học tập cá nhân hỗ trợ bởi AI theo mô hình freemium: gói Free đủ dùng để thu hút, gói Paid (Tháng 99.000 VND / Năm 990.000 VND, `Status: PROPOSED`) mở hạn mức cao để monetization bền vững và bù chi phí `LLM`.

Mục tiêu đo được (đề xuất, chốt song song khi thiết kế):
- Kích hoạt: >= 60% tài khoản mới có >= 1 tài liệu trong 7 ngày.
- Gắn bó: DAU/MAU >= 25%, streak trung bình >= 4 ngày.
- Chuyển đổi Free ➔ Paid >= 3-5% sau 60 ngày.
- Chất lượng AI: >= 80% hữu ích, báo cáo sai < 5%.

## 2. Phạm Vi MVP (A4)

**In-Scope MVP (P0+P1 cơ bản)**:
- Xác thực email/password + Google OAuth, phân vai Guest/Free/Paid/Admin.
- Tài liệu: PDF/DOCX/TXT/MD, Free 10 tài liệu / 50MB / 20MB mỗi tệp.
- Gia sư AI: hỏi đáp RAG Top-K=5, trích dẫn nguồn, quota 20 câu/ngày Free.
- Quiz/Flashcard: sinh bằng AI 3 bộ/ngày Free, làm bài và chấm miễn phí, giải thích sau khi nộp.
- Gói và thanh toán: Free/Tháng/Năm, quota 4 chiều reset 00:00 `Asia/Ho_Chi_Minh`, grace 3 ngày, webhook idempotent, đối soát.
- Tiến độ: streak, dashboard 7 ngày, báo cáo tuần T2–CN.

**Out-of-Scope MVP**: Mobile native, offline sâu, live-class, marketplace khóa học, chứng chỉ pháp lý, PPTX/OCR scan, semantic rerank nâng cao (sang Sprint 2+).

## 3. Tính Khả Thi (A3)

- **Kỹ thuật**: Khả thi với Web responsive + `LLM` ngoài + RAG + lưu trữ tệp. Rủi ro chính là chi phí `LLM` và hallucination — đã có quota + citation + kiểm duyệt.
- **Vận hành**: Cần quy trình takedown bản quyền tay, duyệt hoàn tiền tay, theo dõi quota và đối soát thanh toán hằng ngày trong MVP.
- **Dữ liệu**: Riêng tư theo mặc định, audit 12 tháng cho hành vi nhạy cảm, purge 30 ngày sau xóa mềm.
- **Bảo mật/tuân thủ**: JWT ngắn + refresh xoay, khóa sau 5 lần sai, HMAC webhook, không dùng tài liệu riêng để train chung khi chưa đồng ý (`DEC-005 PROPOSED`).
- **Phụ thuộc**: Nhà cung cấp `LLM`, cổng thanh toán, lưu trữ tệp — đều có timeout/retry/fallback ở mức policy.
- **Chi phí**: Biến phí `LLM` là rủi ro lớn nhất. Quota Free mặc định + rate limit + cảnh báo là hàng rào bắt buộc.

## 4. Lộ Trình (A5)

| Giai đoạn | Nội dung | Cửa ra |
| :--- | :--- | :--- |
| Sprint 0 | Chốt DEC-001..005, khởi tạo Solution Design, hạ tầng, auth | Thiết kế được duyệt |
| Sprint 1 | Auth + Tài liệu + Gia sư AI (quota + RAG + citation) + Gói/Thanh toán lõi | Bản alpha nội bộ, test quota và webhook |
| Sprint 2 | Quiz/Flashcard + Tiến độ cơ bản + thông báo quota/hết hạn + canary 10% | Beta giới hạn |
| Sprint 3 | Củng cố: numeric-check, xáo trộn options, bù tiến độ trễ, mở 100% | GA MVP |

Đường găng: Tài liệu READY ➔ RAG ➔ Quota ➔ Thanh toán kích hoạt. Rollback: feature flag tắt sinh AI, giữ học liệu và luyện tập cũ.

## 5. Backlog Tổng Hợp (A6)

**Epic E1 — Truy cập và tài khoản**:
- US-E1-01 Đăng ký/đăng nhập Free, US-E1-02 Google OAuth, US-E1-03 Quản lý hồ sơ và vai.
- Acceptance: phân biệt Guest/Free/Paid, khóa 5 lần/15 phút, audit đầy đủ.

**Epic E2 — Học liệu**:
- US-E2-01 Tải PDF/DOCX/TXT/MD, US-E2-02 Xử lý thành READY có trích dẫn trang, US-E2-03 Tìm kiếm/gắn thẻ/lưu trữ.
- Acceptance: chỉ READY vào RAG, PRIVATE mặc định, purge 30 ngày.

**Epic E3 — Gia sư AI**:
- US-E3-01 Hỏi đáp có hold quota nguyên tử, US-E3-02 Trích dẫn nguồn + vote/báo sai, US-E3-03 Retry 1 lần + fallback + hoàn quota partial.
- Acceptance: hết quota chặn trước gọi `LLM`, `Idempotency-Key` 24 giờ, timeout 30 giây.

**Epic E4 — Quiz/Flashcard**:
- US-E4-01 Sinh đề tốn 1 lượt quota, US-E4-02 Làm bài/chấm miễn phí < 200ms, US-E4-03 Giải thích sau SUBMITTED, SM-2 cho Flashcard.
- Acceptance: `DRAFT➔IN_PROGRESS➔SUBMITTED➔SCORED`, chống double-submit.

**Epic E5 — Monetization**:
- US-E5-01 Xem/mua/gia hạn/hủy gói, US-E5-02 Webhook idempotent + đối soát UNKNOWN, US-E5-03 Quota 4 chiều + grace 3 ngày + nhắc T-7/T-3/T-1.
- Acceptance: 1 PENDING/user, đơn treo 30 phút ➔ TIMEOUT, hoàn full/partial/void duyệt tay.

**Epic E6 — Tiến độ**:
- US-E6-01 Ingest `AI_ASKED/QUIZ_COMPLETED/STUDY_SESSION` idempotent, US-E6-02 Streak + dashboard 7 ngày < 1 giây, US-E6-03 Báo cáo tuần trước 08:00 thứ Hai.
- Acceptance: `activeDay` = 5 phút/1 Quiz/3 câu AI, bù trễ <= 7 ngày, nhãn PARTIAL khi thiếu.

Chi tiết test scenario xem `16-ke-hoach-kiem-thu.md` từng module. Kế hoạch phát hành xem `18-ke-hoach-phat-hanh.md` từng module.

## 6. Ma Trận Truy Vết Lõi

```text
FACT-01 (app học tập + AI) ➔ CAP-02/03/04 ➔ Module 02/03/04 ➔ BR-R/C/N ➔ Acceptance hỏi đáp có nguồn
FACT-02 (Free + Paid) ➔ CAP-01/05 ➔ Module 01/05 ➔ BR-F/T/X/RV ➔ Acceptance mua gói + quota + đối soát
```

## 7. Tình Trạng Quyết Định

- `CONFIRMED`: FACT-01, FACT-02 (User xác nhận ý tưởng freemium).
- `PROPOSED` (không chặn thiết kế, chốt song song ở Solution Design): DEC-001 đối tượng phổ thông, DEC-002 Web responsive, DEC-003 quota Free, DEC-004 giá 99k/990k, DEC-005 riêng tư train, DEC-006 kiểm duyệt, DEC-501 cổng thanh toán, DEC-502 phí, DEC-506 ngưỡng hoàn, DEC-601/602 ngưỡng `activeDay`/tuần.
- `UNRESOLVED` chặn lõi: 0.

## 8. Artifacts

- `01-ke-hoach.md`, `02-bao-cao-kham-pha-yeu-cau.md`, `03-phan-tich-sau/` (116 tệp), `histories.md`, chính là tệp này.
