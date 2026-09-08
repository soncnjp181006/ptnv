# Kế Hoạch Phân Tích & Khám Phá Nghiệp Vụ (01-ke-hoach.md)

> Tài liệu này là **Kế hoạch sống (Living Plan)**. Khi thảo luận với người dùng dẫn đến thay đổi mục tiêu, phạm vi hoặc quyết định, Agent **BẮT BUỘC SỬA TRỰC TIẾP TẠI CHỖ** các mục bị ảnh hưởng dưới đây và ghi nhận vào bảng Lịch sử Điều chỉnh ở cuối tài liệu.

Vị trí: `specs/01-app-nha-hang/01-ke-hoach.md`
Trạng thái: Chặng A1 đang thực thi
Nguồn sự thật: Hiến pháp tại `.agent-system/memory/constitution/constitution.md`

---

## 1. Mục Tiêu (Goal & Outcomes)

- **Vấn đề (Problem)**: `UNRESOLVED` — Chưa rõ nỗi đau cụ thể. Quán đang quản lý thủ công bằng giấy? Thất thoát đơn? Quá tải giờ cao điểm?
- **Nhu cầu (Need)**: Có nhu cầu làm một ứng dụng cho nhà hàng. Mức độ chi tiết: rất mơ hồ.
- **Kết quả mong đợi (Desired outcome)**: `UNRESOLVED` — Chưa xác định. Ví dụ: giảm thời gian phục vụ, tăng đơn online, chống thất thoát.
- **Mục tiêu cốt lõi (Goal)**: `CONFIRMED` — Xây dựng app kết hợp toàn diện: vừa quản lý vận hành nội bộ vừa làm kênh bán hàng cho khách, triển khai theo pha (pha 1 vận hành, pha 2 mở rộng cho khách). Căn cứ: DEC-001.
- **Tiêu chí thành công (Success metric)**: `UNRESOLVED` — Chưa có số đo kiểm chứng được.
- **Ràng buộc (Constraint)**: `UNRESOLVED` — Chưa rõ ngân sách, thời gian, nền tảng, nhân sự vận hành.
- **Non-goal**: `UNRESOLVED` — Chưa xác định ranh giới loại trừ.

## 2. Phạm Vi & Ranh Giới (Scope & Boundaries)

- **Trong phạm vi (In-Scope / MVP)**: `CONFIRMED` hướng kết hợp toàn diện, phân pha. Pha 1 ưu tiên vận hành. Các ứng viên năng lực (Capability Candidates, trạng thái `PROPOSED` chờ xác minh ở A2):
  - `table_management` (quản lý bàn / sơ đồ bàn)
  - `reservation` (đặt bàn trước)
  - `menu_management` (quản lý thực đơn)
  - `order_taking` (gọi món tại bàn / online)
  - `kitchen_display` (điều phối bếp / bar)
  - `payment_billing` (thanh toán, hóa đơn)
  - `inventory` (kho nguyên liệu)
  - `staff_role` (phân quyền nhân viên)
  - `customer_loyalty` (khách thân thiết, khuyến mãi)
  - `reporting` (báo cáo doanh thu)
- **Ngoài phạm vi (Out-of-Scope / Non-Goals)**: `UNRESOLVED`.
- **Ranh giới kỹ thuật / nghiệp vụ**: Chưa thiết kế kiến trúc sớm ở chặng A1 theo quy chuẩn. Chỉ phân tích nghiệp vụ. Đã rõ ràng buộc chuỗi: cần phân biệt dữ liệu cấp chuỗi (thực đơn chung, kho trung tâm, báo cáo hợp nhất) và dữ liệu cấp điểm bán (bàn, đơn, ca làm việc).

## 3. Dữ Liệu Đầu Vào (Inputs)

- **Yêu cầu từ người dùng**: "Mình định làm app nhà hàng".
  - Function: `extractFacts()`
  - Variable: `businessContext = "restaurant_app_vague"`
  - Artifact: `specs/01-app-nha-hang/01-ke-hoach.md`
- **Tài liệu tham chiếu / Domain Playbook**: Không có playbook nhà hàng được kích hoạt tường minh. Mọi suy luận domain từ Agent đều gắn `Status: PROPOSED, Source: AGENT_INFERENCE`.

## 4. Điều Đã Biết (Known Facts)

- [FACT-01]: Người dùng có ý định xây dựng một ứng dụng liên quan đến nhà hàng. Nguồn: yêu cầu trực tiếp từ User. Trạng thái: `FACT`.
- [FACT-02]: Workspace hiện tại chưa có mã nguồn nghiệp vụ, chỉ có khung quản trị `.agent-system/` và `.opencode/`. Nguồn: kiểm tra thư mục gốc. Trạng thái: `FACT`.
- [DEC-001]: Đã chốt mô hình kết hợp toàn diện (vận hành nội bộ + kênh khách hàng, làm theo pha). Trạng thái: `CONFIRMED`.
- [DEC-002 refined]: Chuỗi theo mô hình fine dining, suất ăn dài, giá trị đơn cao, số lượt quay thấp. Hệ quả: chính sách giữ bàn 60 phút ở đặt bàn là hợp lý, khác mô hình quay nhanh. Trạng thái: `CONFIRMED`.

## 5. Điều Chưa Rõ & Giả Định (Unknowns & Assumptions)

- [UNKNOWN-01]: Hệ thống phục vụ ai là chính? Đã chốt kết hợp toàn diện theo DEC-001. Trạng thái: `CONFIRMED`.
- [UNKNOWN-02]: Loại hình kinh doanh là gì? Đã chốt chuỗi nhiều chi nhánh ngay từ khai trương. Trạng thái: `CONFIRMED`. Hệ quả: cần quản lý tập trung `menu`, `inventory`, `reporting` đa điểm.
- [UNKNOWN-03]: Kênh phục vụ nào là cốt lõi? Đã chốt MVP gồm Ăn tại chỗ + Đặt bàn trước. Mang về / Giao hàng để pha sau. Trạng thái: `CONFIRMED`.
- [UNKNOWN-04]: Phương thức thanh toán và xuất hóa đơn mong muốn? Đã chốt MVP: Tiền mặt + Chuyển khoản / QR + Ví điện tử / Thẻ. Hệ quả: cần đối soát đa kênh, phí cổng, hoàn tiền. Trạng thái: `CONFIRMED`.
- [UNKNOWN-05]: Quy mô vận hành? Đã chốt Tier Lớn (trên 5 điểm hoặc trên 40 bàn/điểm hoặc trên 100 món). Rủi ro: MVP quá lớn. Giảm thiểu: thí điểm 1 tới 2 điểm trước, rồi nhân rộng. Trạng thái: `CONFIRMED`.
- [UNKNOWN-06]: Nền tảng mục tiêu? Đã chốt full 4 nền tảng: Web quản trị + Tablet / POS + Màn hình bếp / bar + Mobile cho khách. Rủi ro effort cao, cần phân pha thiết bị ở A5. Trạng thái: `CONFIRMED`.
- [UNKNOWN-07]: Ràng buộc ngân sách, thời gian, tuân thủ hóa đơn / thuế? Trạng thái: `UNRESOLVED`.
- [ASSUMPTION-01]: Tạm giả định có ít nhất hai nhóm Actor là nhân viên phục vụ và quản lý. Trạng thái: `ASSUMPTION`. Không được dùng làm policy đã chốt.

## 6. Các Bước Phân Tích Kỹ Thuật (Analysis Stages)

- Chặng A1: Khám phá yêu cầu & phạm vi — đang thực thi. Đầu ra: `01-ke-hoach.md`, `02-bao-cao-kham-pha-yeu-cau.md`.
- Chặng A2: Phân tích nghiệp vụ sâu & Use Cases — chưa bắt đầu. Yêu cầu P0 được chốt.
- Chặng A3: Đánh giá tính khả thi — chưa bắt đầu.
- Chặng A4: Xác lập mục tiêu, phạm vi MVP & KPIs — chưa bắt đầu.
- Chặng A5: Đánh giá rủi ro & lộ trình — chưa bắt đầu.
- Chặng A6: Phân rã Backlog & tiêu chí nghiệm thu — chưa bắt đầu.

## 7. Các Domain Probes Cần Thăm Dò (Hidden Business Probes)

Mọi probe dưới đây có `Status: PROPOSED, Source: AGENT_INFERENCE`. Không được chuyển thành quy tắc nghiệp vụ khi chưa hỏi User.

- [PROBE-01] Resource bàn ghế hữu hạn: `availability`, `allocation`, `hold`, `expiry`, `release`, `conflict` khi nhiều khách cùng xin một bàn?
- [PROBE-02] Vòng đời đơn gọi món: `state`, `transition`, `cancellation`, `rework` khi bếp báo hết món?
- [PROBE-03] Giao dịch thanh toán: `pricing`, `discount`, `tax`, `refund`, `partial`, `reconciliation`?
- [PROBE-04] Tồn kho nguyên liệu theo món: `on-hand`, `reserved`, `consumed`, `shortage` có trừ kho khi chốt món?
- [PROBE-05] Phân quyền Actor: `role`, `permission`, `approval`, `audit` cho thu ngân, bếp, quản lý?
- [PROBE-06] Yếu tố thời gian: `business_date`, `cutoff`, `grace_period` giữ bàn khi khách đến muộn?
- [PROBE-07] Thông báo: `trigger`, `channel`, `template` cho xác nhận đặt bàn, gọi bếp, báo món xong?

## 8. Các Checkpoints Cần User Quyết Định (Decision Checkpoints)

- [DEC-001]: Hệ thống phục vụ ai là chính và tạo giá trị cốt lõi gì? Đáp án: Kết hợp toàn diện, phân pha. Ưu tiên: P0. Trạng thái: `CONFIRMED`.
- [DEC-002]: Loại hình và quy mô nhà hàng ở thời điểm khai trương? Đáp án: Chuỗi nhiều chi nhánh. Ưu tiên: P0. Trạng thái: `CONFIRMED`. Loại: Đơn chọn.
- [DEC-003]: Kênh phục vụ cốt lõi trong MVP? Đáp án: Ăn tại chỗ + Đặt bàn trước. Ưu tiên: P0. Trạng thái: `CONFIRMED`. Loại: Đa chọn.
- [DEC-004]: Phương thức thanh toán chấp nhận trong MVP? Đáp án: Tiền mặt + Chuyển khoản / QR + Ví điện tử / Thẻ. Ưu tiên: P0. Trạng thái: `CONFIRMED`. Loại: Đa chọn.
- [DEC-005]: Số lượng vận hành dự kiến? Đáp án: Tier Lớn, thí điểm 1 tới 2 điểm trước khi nhân rộng. Ưu tiên: P1. Trạng thái: `CONFIRMED`.
- [DEC-006]: Nền tảng thiết bị mục tiêu? Đáp án: đủ 4 nền tảng (Web, Tablet / POS, Màn hình bếp, Mobile khách). Ưu tiên: P1. Trạng thái: `CONFIRMED`. Loại: Đa chọn.

## 9. Danh Sách Artifacts Dự Kiến (Expected Artifacts trong specs/01-app-nha-hang/)

> Lưu ý: Tuyệt đối cấm tạo trong `docs/`. Bản mới nhất nằm trực tiếp tại thư mục ngoài của yêu cầu; các bản cũ lưu trong `versions/`.

- `histories.md` (Nhật ký thay đổi & điểm phục hồi rollback)
- `01-ke-hoach.md` (Kế hoạch sống - Living Plan, tài liệu này)
- `02-bao-cao-kham-pha-yeu-cau.md` (Khám phá chặng A1, tạo sau khi chốt P0)
- `03-phan-tich-sau/` (Phân tích chuyên sâu chặng A2 - 19 tệp theo từng nghiệp vụ con)
- `04-tong-hop/tong-hop-toan-bo-yeu-cau.md` (Tổng hợp toàn diện)
- `versions/` (Snapshot các phiên bản cũ đã chốt để rollback khi cần)

## 10. Tiêu Chí Nghiệm Thu Chặng (Quality Gates & DoD)

- [x] G1: Discovery Complete (DEC-001 tới DEC-004 đã `CONFIRMED`, đã tạo `02-bao-cao-kham-pha-yeu-cau.md`)
- [ ] G2: Business Model Complete (Có Capability, Process, Use Case, Business Object)
- [ ] G3: Deep Analysis Complete (Có Scenario, State, Rule, Authority, Resource, Time, Data cho các concern liên quan)
- [ ] G4: Decision Complete (Không còn P0 `UNRESOLVED`)
- [ ] G5: Consistency Complete (Không có mâu thuẫn chưa xử lý)
- [ ] G6: Traceability Complete (Không có orphan core requirement / rule / scenario)
- [ ] G7: Delivery Ready (Đủ thông tin để sang thiết kế mà không phải đoán policy cốt lõi)

## 11. Bước Tiếp Theo (Next Actions)

- P0 đã chốt DEC-001 tới DEC-004, P1 đã chốt DEC-005 và DEC-006, G1 đạt. Bước kế tiếp: sang A2 phân tích sâu theo từng nghiệp vụ con.
- Khi chốt xong P1, hoàn thiện vòng A1 và bắt đầu `03-phan-tich-sau/` ở A2.

---

## 12. Lịch Sử Điều Chỉnh Kế Hoạch (Plan Revision Ledger)

*Bảng này ghi nhận mọi thay đổi trực tiếp trên Kế hoạch sau các vòng lặp thảo luận và phản biện với người dùng:*

| STT | Thời điểm | Mục sửa đổi | Nội dung cũ | Nội dung mới sau thảo luận | Quyết định / Căn cứ |
| :---: | :--- | :--- | :--- | :--- | :--- |
| 1 | 2026-09-08 | Mục 1 Goal, Mục 2 Scope, UNKNOWN-01, DEC-001 | `UNRESOLVED` chờ chốt đối tượng phục vụ | Chốt mô hình kết hợp toàn diện, phân pha (vận hành trước, khách hàng sau) | DEC-001 `CONFIRMED` theo trả lời User |
| 2 | 2026-09-08 | UNKNOWN-02, DEC-002, Mục 2 Ranh giới | `UNRESOLVED` loại hình quán | Chốt chuỗi nhiều chi nhánh, phân biệt dữ liệu cấp chuỗi và cấp điểm bán | DEC-002 `CONFIRMED` theo trả lời User |
| 3 | 2026-09-08 | UNKNOWN-03, DEC-003 | `UNRESOLVED` kênh phục vụ | Chốt MVP: Ăn tại chỗ + Đặt bàn trước, lùi Mang về / Giao hàng | DEC-003 `CONFIRMED` theo trả lời User |
| 4 | 2026-09-08 | UNKNOWN-04, DEC-004 | `UNRESOLVED` thanh toán | Chốt MVP: Tiền mặt + Chuyển khoản / QR + Ví / Thẻ, cần đối soát đa kênh | DEC-004 `CONFIRMED` theo trả lời User |
| 5 | 2026-09-08 | UNKNOWN-05, DEC-005 | `UNRESOLVED` quy mô | Chốt Tier Lớn, kèm giảm thiểu thí điểm 1 tới 2 điểm trước | DEC-005 `CONFIRMED` theo trả lời User |
| 6 | 2026-09-08 | UNKNOWN-06, DEC-006 | `UNRESOLVED` nền tảng | Chốt full 4 nền tảng, cần phân pha thiết bị ở A5 | DEC-006 `CONFIRMED` theo trả lời User |
| 7 | 2026-09-08 | DEC-002 refined, Mục 4 Known Facts | Chuỗi Tier Lớn quay nhanh | Bổ sung mô hình fine dining: suất dài, giữ bàn 60 phút hợp lý | DEC-R01 thảo luận, User xác nhận fine dining |
