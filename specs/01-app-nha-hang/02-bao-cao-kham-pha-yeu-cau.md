# Báo Cáo Khám Phá Yêu Cầu (02-bao-cao-kham-pha-yeu-cau.md)

Vị trí: `specs/01-app-nha-hang/02-bao-cao-kham-pha-yeu-cau.md`
Chặng: A1 Discovery
Trạng thái P0: Đã chốt DEC-001 tới DEC-004. Đủ điều kiện đóng G1.
Nguồn sự thật: `01-ke-hoach.md` (Living Plan, bản mới nhất)

---

## 1. Vấn Đề, Nhu Cầu, Mục Tiêu

- **Vấn đề (Problem)**: `DERIVED` từ FACT + DEC — Chuỗi nhà hàng cần một nền tảng thống nhất để vừa vận hành nhiều điểm bán vừa phục vụ khách ăn tại chỗ và đặt bàn trước, tránh phân mảnh công cụ rời rạc.
- **Nhu cầu (Need)**: `FACT` — Làm app nhà hàng.
- **Mục tiêu cốt lõi (Goal)**: `CONFIRMED` — App kết hợp toàn diện (vận hành nội bộ + kênh khách hàng), triển khai theo pha.
- **Kết quả mong đợi**: Giảm thời gian phục vụ tại bàn, giảm thất thoát đơn và tiền, nhìn được doanh thu hợp nhất toàn chuỗi theo ngày.
- **Tiêu chí thành công (đề xuất, chưa chốt số)**: Thời gian từ gọi món tới báo bếp dưới 1 phút, tỉ lệ đơn sai / thất lạc giảm, đối soát tiền mặt + QR + ví khớp trong ngày.
- **Ràng buộc đã thấy**: Mô hình chuỗi từ đầu nên bắt buộc phân tách dữ liệu cấp chuỗi và cấp điểm bán. Thanh toán đa kênh nên bắt buộc có đối soát và phân quyền thu ngân.
- **Non-goal cho MVP**: Mang về, giao hàng, công nợ trả sau, chương trình khách thân thiết phức tạp.

## 2. Actor và Bên Liên Quan

- `customer`: Khách ăn tại chỗ và khách đặt bàn trước. Mục tiêu: tìm bàn, đặt chỗ, xem menu, gọi món, thanh toán nhanh.
- `receptionist`: Lễ tân điểm bán. Mục tiêu: tiếp nhận đặt bàn, xếp bàn, xác nhận khách đến.
- `waiter`: Phục vụ. Mục tiêu: ghi món tại bàn, chuyển bếp, bổ sung / hủy món có kiểm soát.
- `kitchen_staff`: Bếp / bar. Mục tiêu: nhận yêu cầu theo thứ tự, báo hết món, báo món xong.
- `cashier`: Thu ngân. Mục tiêu: lập hóa đơn, thu đa kênh, đối soát ca.
- `branch_manager`: Quản lý điểm bán. Mục tiêu: giám sát bàn, đơn, nhân sự ca, xử lý ngoại lệ.
- `chain_admin`: Quản trị chuỗi. Mục tiêu: quản lý thực đơn chung, giá theo điểm, báo cáo hợp nhất.
- Actor chi tiết về quyền hạn, phê duyệt, ủy quyền sẽ làm rõ ở A2. Hiện tại `ASSUMPTION-01` đã được thay bằng các Actor `PROPOSED` ở trên.

## 3. Phạm Vi MVP Đã Chốt

### Trong phạm vi

- Quản lý đa điểm bán ở mức cơ bản: danh sách điểm, phân biệt dữ liệu chuỗi và điểm.
- Sơ đồ bàn và trạng thái bàn cho ăn tại chỗ.
- Đặt bàn trước với giữ chỗ có thời hạn và xác nhận lễ tân.
- Thực đơn dùng chung toàn chuỗi, cho phép giá riêng theo điểm nếu cần (chờ chốt ở A2).
- Gọi món tại bàn, chuyển bếp / bar, cập nhật trạng thái món.
- Hóa đơn và thanh toán ba kênh: tiền mặt, chuyển khoản / QR, ví điện tử / thẻ.
- Đối soát ca và báo cáo doanh thu theo điểm và hợp nhất chuỗi ở mức cơ bản.
- Phân quyền tối thiểu cho 7 nhóm Actor ở trên.

### Ngoài phạm vi MVP

- Mang về và giao hàng tận nơi.
- Công nợ và trả sau.
- Kho nguyên liệu trừ tự động theo định mức (chỉ ghi nhận nhu cầu, chốt ở A2).
- Khuyến mãi và khách thân thiết phức tạp.
- Tích hợp kế toán thuế và hóa đơn điện tử đầy đủ (chỉ ghi nhận rủi ro tuân thủ).

## 4. Capability Candidates (trạng thái PROPOSED)

| Mã | Năng lực | Kết quả nghiệp vụ | Actor chính | Ghi chú pha |
| :--- | :--- | :--- | :--- | :--- |
| CAP-01 | `branch_management` | Nhìn được toàn chuỗi, cấu hình theo điểm | `chain_admin` | Pha 1 lõi |
| CAP-02 | `table_management` | Biết bàn nào trống, đang giữ, đang phục vụ | `receptionist`, `waiter` | Pha 1 lõi |
| CAP-03 | `reservation` | Giữ chỗ có thời hạn, chống tranh chấp bàn | `customer`, `receptionist` | Pha 1 lõi |
| CAP-04 | `menu_management` | Thực đơn nhất quán, giá linh hoạt theo điểm | `chain_admin` | Pha 1 lõi |
| CAP-05 | `order_taking` | Ghi món nhanh, ít sai sót | `waiter`, `customer` | Pha 1 lõi |
| CAP-06 | `kitchen_coordination` | Bếp nhận đúng thứ tự, báo hết / xong kịp thời | `kitchen_staff` | Pha 1 lõi |
| CAP-07 | `payment_billing` | Thu đúng, đủ, đa kênh | `cashier` | Pha 1 lõi, rủi ro đối soát cao |
| CAP-08 | `shift_reconciliation` | Chốt ca không lệch tiền | `cashier`, `branch_manager` | Pha 1 |
| CAP-09 | `reporting` | Doanh thu theo điểm và toàn chuỗi | `branch_manager`, `chain_admin` | Pha 1 cơ bản |
| CAP-10 | `staff_authority` | Ai được làm gì, ai duyệt hủy / giảm giá | Toàn bộ | Pha 1, làm rõ ở A2 |

## 5. Quyết Định Đã Chốt

- DEC-001 `CONFIRMED`: Kết hợp toàn diện, phân pha (vận hành trước, khách hàng sau).
- DEC-002 `CONFIRMED`: Chuỗi nhiều chi nhánh ngay từ đầu.
- DEC-003 `CONFIRMED`: MVP gồm Ăn tại chỗ + Đặt bàn trước. Đa chọn.
- DEC-004 `CONFIRMED`: MVP gồm Tiền mặt + Chuyển khoản / QR + Ví điện tử / Thẻ. Đa chọn. Hệ quả P0: cần chính sách phí cổng, đối soát, hoàn tiền, xử lý giao dịch lơ lửng ở A2.
- DEC-005 `CONFIRMED`: Tier Lớn (trên 5 điểm hoặc trên 40 bàn/điểm hoặc trên 100 món). Giảm thiểu: thí điểm 1 tới 2 điểm trước khi nhân rộng.
- DEC-006 `CONFIRMED`: Full 4 nền tảng (Web quản trị, Tablet / POS, Màn hình bếp / bar, Mobile cho khách). Đa chọn. Hệ quả: cần phân pha thiết bị ở A5.

## 6. Điểm Chưa Rõ Còn Lại (chuyển sang A2)

- UNKNOWN-05 (P1) `CONFIRMED`: Tier Lớn, thí điểm 1 tới 2 điểm trước.
- UNKNOWN-06 (P1) `CONFIRMED`: Full 4 nền tảng.
- UNKNOWN-07: Ngân sách, mốc thời gian, yêu cầu hóa đơn thuế.
- Các probe PROBE-01 tới PROBE-07 vẫn ở `PROPOSED`, chờ elicitation ở A2: giữ bàn bao lâu, cho hủy món tới bước nào, ai duyệt giảm giá, đối soát ví theo chu kỳ nào.

## 7. Rủi Ro Thấy Sớm

- Kết hợp toàn diện + chuỗi + 3 kênh thanh toán trong một MVP là phạm vi lớn. Bắt buộc giữ kỷ luật phân pha, nếu không sẽ vỡ tiến độ.
- Thanh toán ví / thẻ cần hợp đồng cổng ngoài, phí, đối soát và hoàn tiền. Nếu chưa có đối tác, pha 1 nên cho chạy tiền mặt + QR trước.
- Đặt bàn và ăn tại chỗ cùng tranh một pool bàn hữu hạn. Nếu không chốt chính sách giữ chỗ và nhả chỗ, giờ cao điểm sẽ xung đột.

## 8. Cổng Chất Lượng

- G1 Discovery Complete: Đạt. P0 từ DEC-001 tới DEC-004 đã `CONFIRMED`.
- G2 trở đi: Chưa đạt. Chờ A2 mô hình hóa Capability, Process, Use Case, Business Object.

## 9. Bước Tiếp Theo

1. Làm rõ 2 điểm P1 còn lại (quy mô số liệu và nền tảng thiết bị) bằng 1 tới 2 câu hỏi tuần tự.
2. Sang A2: bóc tách từng nghiệp vụ con (đặt bàn, gọi món, bếp, thanh toán, đối soát, báo cáo chuỗi) kèm Flowchart L0 / L1 / L2 đánh số edge, ma trận kịch bản, máy trạng thái, quy tắc nghiệp vụ và ma trận truy vết.
3. Không thiết kế kiến trúc kỹ thuật hay cấu trúc DB ở chặng này.
