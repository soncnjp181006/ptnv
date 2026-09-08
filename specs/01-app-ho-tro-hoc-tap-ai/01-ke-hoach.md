# Kế Hoạch Phân Tích & Khám Phá Nghiệp Vụ (01-ke-hoach.md)

> [!NOTE]
> Tài liệu này là **Kế hoạch sống (Living Plan)**. Khi thảo luận với người dùng dẫn đến thay đổi mục tiêu, phạm vi hoặc quyết định, Agent **BẮT BUỘC SỬA TRỰC TIẾP TẠI CHỖ** các mục bị ảnh hưởng và ghi nhận vào bảng Lịch sử Điều chỉnh ở cuối tài liệu.

---

## 1. Mục Tiêu (Goal & Outcomes)

- **Mục tiêu cốt lõi**: Biến ý tưởng mơ hồ "app hỗ trợ học tập + AI, có tính phí + gói Free" thành mô hình nghiệp vụ có thể kiểm chứng, đủ rõ để sang Solution Design mà không phải đoán policy cốt lõi.
- **Kết quả mong đợi**:
  - Xác lập Problem, Need, Goal, Actor, Scope cho ứng dụng học tập hỗ trợ bởi AI.
  - Mô hình hóa 6 nghiệp vụ con với Flowchart L0/L1/L2 đánh số, Use Case, Scenario, State, Rule.
  - Chốt phạm vi MVP, tính khả thi, rủi ro, lộ trình và backlog có tiêu chí nghiệm thu.
- **Tiêu chí thành công**:
  - Không còn P0 `UNRESOLVED` chặn thiết kế cốt lõi (các `PROPOSED` Best Practice được ghi rõ nguồn).
  - Mọi requirement lõi trace được tới Acceptance Criteria.
  - Đạt cổng G1, G2, G3.

## 2. Phạm Vi & Ranh Giới (Scope & Boundaries)

- **Trong phạm vi (In-Scope / MVP)**:
  - Xác thực và quản lý người dùng (học viên, khách vãng lai, quản trị).
  - Quản lý tài liệu học tập do người dùng tải lên.
  - Gia sư AI hỏi đáp theo ngữ cảnh tài liệu (RAG).
  - Tạo và luyện tập Quiz/Flashcard bằng AI.
  - Gói dịch vụ, hạn mức Free/Paid và thanh toán.
  - Theo dõi tiến độ học tập và báo cáo cơ bản.
- **Ngoài phạm vi (Out-of-Scope / Non-Goals)**:
  - Sàn khóa học thương mại điện tử đa giảng viên, live-class video thời gian thực.
  - Chấm thi chính thức có giá trị pháp lý, chứng chỉ được công nhận.
  - Tích hợp phần cứng IoT, ứng dụng native offline-first phức tạp trong MVP.
  - Hệ thống kế toán thuế chuyên sâu (chỉ ghi nhận giao dịch, đối soát cơ bản).
- **Ranh giới kỹ thuật / nghiệp vụ**:
  - Phase A chỉ chốt **policy nghiệp vụ**, không chốt cơ chế cài đặt (`Redis lock`, `SELECT FOR UPDATE`, hãng `LLM` cụ thể).
  - Thanh toán qua cổng ngoài trong MVP, không tự xây cổng thanh toán.

## 3. Dữ Liệu Đầu Vào (Inputs)

- **Yêu cầu từ người dùng**: "Mình định làm app hỗ trợ học tập + AI, có tính phí + gói free".
- **Tài liệu tham chiếu / Domain Playbook**: Không có playbook kích hoạt tường minh. Áp dụng Core Engine + suy luận Best Practice chuẩn ngành Education SaaS + AI Application, mọi đề xuất gắn `Source: AGENT_INFERENCE`, `Status: PROPOSED`.

## 4. Điều Đã Biết (Known Facts)

- [FACT-01]: User muốn xây dựng ứng dụng hỗ trợ học tập có tích hợp AI. `Status: FACT`.
- [FACT-02]: Mô hình kinh doanh gồm gói trả phí và gói Free. `Status: FACT`.
- [FACT-03]: Chưa có ràng buộc nền tảng, môn học, đối tượng cụ thể từ User. `Status: FACT` (về sự thiếu thông tin).
- [FACT-04]: Workspace hiện tại là dự án mới, chưa có mã nguồn nghiệp vụ. `Status: FACT`.

## 5. Điều Chưa Rõ & Giả Định (Unknowns & Assumptions)

- [UNKNOWN-01]: Đối tượng học chính là ai (học sinh K12, sinh viên, người đi làm, luyện ngoại ngữ)? `Trạng thái: PROPOSED mặc định phổ thông + ghi DEC-001`.
- [UNKNOWN-02]: Nền tảng ưu tiên (Web responsive trước hay Mobile App)? `Trạng thái: PROPOSED Web responsive MVP`.
- [UNKNOWN-03]: Giới hạn Free cụ thể (số câu hỏi AI/ngày, số tài liệu, dung lượng)? `Trạng thái: PROPOSED quota mặc định`.
- [UNKNOWN-04]: Cổng thanh toán và chu kỳ gói (tháng/năm)? `Trạng thái: PROPOSED MoMo/VNPay/Stripe + chu kỳ tháng`.
- [ASSUMPTION-01]: Ngôn ngữ chính là tiếng Việt, hỗ trợ tài liệu tiếng Việt + tiếng Anh trong MVP. `Status: ASSUMPTION` ➔ `PROPOSED`.
- [ASSUMPTION-02]: AI trả lời dựa trên tài liệu của người dùng + kiến thức chung, có trích dẫn nguồn. `Status: ASSUMPTION` ➔ `PROPOSED`.
- [ASSUMPTION-03]: Dữ liệu học tập của người dùng là riêng tư, không dùng chéo để train công khai khi chưa同意. `Status: ASSUMPTION` ➔ `PROPOSED`.

## 6. Các Bước Phân Tích Kỹ Thuật (Analysis Stages)

- Chặng A1: Khám phá yêu cầu & phạm vi (tệp `02-bao-cao-kham-pha-yeu-cau.md`).
- Chặng A2: Phân tích nghiệp vụ sâu & Use Cases (thư mục `03-phan-tich-sau/`, 6 nghiệp vụ con x 19 tệp, Flowchart L0/L1/L2 đánh số).
- Chặng A3: Đánh giá tính khả thi (tệp `13-kha-thi-va-rui-ro.md` từng module).
- Chặng A4: Xác lập mục tiêu & MVP (tệp `12-pham-vi-va-muc-tieu.md` từng module + tổng hợp).
- Chặng A5: Đánh giá rủi ro & lộ trình (tệp `14-lo-trinh-thuc-hien.md` từng module).
- Chặng A6: Phân rã Backlog & tiêu chí nghiệm thu (tệp `15`, `16`, `18`, `19` + `04-tong-hop/tong-hop-toan-bo-yeu-cau.md`).

## 7. Các Domain Probes Cần Thăm Dò (Hidden Business Probes)

- [PROBE-01]: Resource — Hạn mức AI (quota câu hỏi, token, số tài liệu) có phải tài nguyên hữu hạn cần allocation/expiry/reconciliation? ➔ Có, xử lý ở module 05.
- [PROBE-02]: Workflow — Tài liệu và Quiz có lifecycle (DRAFT ➔ READY ➔ ARCHIVED) cần approval/rework? ➔ Có, mức nhẹ.
- [PROBE-03]: Transaction — Thanh toán gói có cần idempotency, reversal/refund, đối soát? ➔ Có.
- [PROBE-04]: Money — Pricing, chu kỳ, gia hạn, hoàn tiền, tiền tệ VND? ➔ Có.
- [PROBE-05]: Identity — Phân quyền học viên/quản trị, audit hành vi AI? ➔ Có.
- [PROBE-06]: Time — Chu kỳ quota (ngày/tháng), chu kỳ gói, timezone Asia/Ho_Chi_Minh? ➔ Có.
- [PROBE-07]: Communication — Thông báo hết quota, hết hạn gói, kết quả học tập? ➔ Có.
- [PROBE-08]: Content — Nội dung do người dùng tạo + nội dung AI sinh cần kiểm duyệt, báo cáo vi phạm? ➔ Có.
- [PROBE-09]: External — Nhà cung cấp `LLM`, cổng thanh toán, lưu trữ tệp có timeout/retry/fallback? ➔ Có, mức policy.

## 8. Các Checkpoints Cần User Quyết Định (Decision Checkpoints)

- [DEC-001]: Đối tượng học ưu tiên MVP — Đề xuất: Học sinh/Sinh viên phổ thông đa môn (Toán, Lý, Hóa, Anh văn). `Ưu tiên: P0, Trạng thái: PROPOSED`.
- [DEC-002]: Nền tảng MVP — Đề xuất: Web responsive trước, Mobile sau. `Ưu tiên: P0, Trạng thái: PROPOSED`.
- [DEC-003]: Hạn mức Free — Đề xuất: 20 câu hỏi AI/ngày, 10 tài liệu, 50MB, 3 bộ Quiz AI/ngày. `Ưu tiên: P0, Trạng thái: PROPOSED`.
- [DEC-004]: Mô hình gói Paid — Đề xuất: Gói Tháng (99.000 VND) + Gói Năm (990.000 VND), quota cao gấp 10-20 lần. `Ưu tiên: P0, Trạng thái: PROPOSED`.
- [DEC-005]: Chính sách dữ liệu AI — Đề xuất: Không dùng tài liệu riêng tư để train chung khi chưa có đồng ý tường minh. `Ưu tiên: P0, Trạng thái: PROPOSED`.
- [DEC-006]: Kiểm duyệt nội dung AI — Đề xuất: Lọc nội dung độc hại + cho phép báo cáo câu trả lời sai. `Ưu tiên: P1, Trạng thái: PROPOSED`.

Không có P0 `UNRESOLVED` chặn thiết kế sau khi áp dụng Best Practice. Toàn bộ `PROPOSED` trên không chặn sang Solution Design, có thể chốt song song.

## 9. Danh Sách Artifacts Dự Kiến (Expected Artifacts trong specs/01-app-ho-tro-hoc-tap-ai/)

> *Lưu ý: Tuyệt đối cấm tạo trong `docs/`. Bản mới nhất nằm trực tiếp tại thư mục ngoài của yêu cầu; các bản cũ lưu trong `versions/`.*

- `histories.md` (Nhật ký thay đổi & điểm phục hồi rollback)
- `01-ke-hoach.md` (Kế hoạch sống - Living Plan, chính là tệp này)
- `02-bao-cao-kham-pha-yeu-cau.md` (Khám phá chặng A1)
- `03-phan-tich-sau/` (Phân tích chuyên sâu chặng A2 - 19 tệp theo từng nghiệp vụ con)
  - `01-nghiep_vu_tong_quat.md`
  - `02-cac_nghiep_vu_con.md`
  - `01-xac-thuc-va-quan-ly-nguoi-dung/`
  - `02-quan-ly-tai-lieu-hoc-tap/`
  - `03-gia-su-ai-hoi-dap/`
  - `04-luyen-tap-quiz-flashcard/`
  - `05-goi-dich-vu-thanh-toan-va-han-muc/`
  - `06-theo-doi-tien-do-va-bao-cao/`
  - `03-tong_hop.md`
- `04-tong-hop/tong-hop-toan-bo-yeu-cau.md` (Tổng hợp toàn diện A1 ➔ A6)
- `versions/` (Snapshot các phiên bản cũ đã chốt để rollback khi cần)

## 10. Tiêu Chí Nghiệm Thu Chặng (Quality Gates & DoD)

- [x] G1: Discovery Complete — Problem, Need, Goal, Actor, Scope, Constraint đã xác lập. Không còn P0 `UNRESOLVED` chặn lõi.
- [ ] G2: Business Model Complete — Capability, Process, Use Case, Resource đã mô hình hóa (hoàn tất khi xong `03-phan-tich-sau/`).
- [ ] G3: Deep Analysis Complete — Scenario, State, Rule, Authority, Time, Data, Side-effect, Failure/Recovery đầy đủ + Feasibility, Roadmap, Backlog.

## 11. Bước Tiếp Theo (Next Actions)

- Hoàn thiện `02-bao-cao-kham-pha-yeu-cau.md`.
- Phân rã và phân tích sâu 6 nghiệp vụ con theo chuẩn 19 tệp, Flowchart L0/L1/L2 đánh số edge.
- Tổng hợp A3 ➔ A6 vào `04-tong-hop/tong-hop-toan-bo-yeu-cau.md`, xuất Thẻ Nghiệm Thu và Menu 1-4.

---

## 12. Lịch Sử Điều Chỉnh Kế Hoạch (Plan Revision Ledger)

*Bảng này ghi nhận mọi thay đổi trực tiếp trên Kế hoạch sau các vòng lặp thảo luận và phản biện với người dùng:*

| STT | Thời điểm | Mục sửa đổi | Nội dung cũ | Nội dung mới sau thảo luận | Quyết định / Căn cứ |
| :---: | :--- | :--- | :--- | :--- | :--- |
| 1 | 2026-09-08 | Khởi tạo | - | Toàn bộ kế hoạch v1 | FACT-01, FACT-02 + Best Practice Education SaaS |
