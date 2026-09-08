# Bức Tranh Nghiệp Vụ Tổng Quát (01-nghiep_vu_tong_quat.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-nghiep_vu_tong_quat.md`
Căn cứ: DEC-001 tới DEC-006 `CONFIRMED`. Chuỗi Tier Lớn, thí điểm 1 tới 2 điểm.

## 1. Chuỗi giá trị

Khách đặt bàn trước hoặc tới trực tiếp → Lễ tân xếp bàn trên pool chung → Phục vụ ghi món → Bếp / bar thực hiện → Thu ngân lập hóa đơn đa kênh → Đối soát ca → Báo cáo điểm và hợp nhất chuỗi.

## 2. Pool tài nguyên dùng chung

- Bàn là tài nguyên hữu hạn, dùng chung giữa hai kênh Ăn tại chỗ và Đặt bàn trước. Mọi tranh chấp đều quy về `table_id` + `business_date` + khung giờ.
- Thực đơn là dữ liệu cấp chuỗi, giá có thể lệch theo điểm. Đơn, bàn, ca là dữ liệu cấp điểm.

## 3. Thứ tự bóc tách A2

1. `01-dat-ban` — đang thực thi, xung đột cao nhất.
2. `02-goi-mon-tai-ban` — phụ thuộc bàn đã `OCCUPIED` hoặc `RESERVED_SEATED`.
3. `03-dieu-phoi-bep` — phụ thuộc đơn đã `KITCHEN_SENT`.
4. `04-thanh-toan-doi-soat` — phụ thuộc hóa đơn đa kênh ví / thẻ.
5. `05-bao-cao-chuoi` và `06-phan-quyen` — cắt ngang.

## 4. Nguyên tắc xuyên suốt

- Mọi luồng Mermaid đánh số edge, nhánh có điều kiện rõ ràng.
- Mọi policy suy luận từ Agent gắn `PROPOSED`, chỉ thành `CONFIRMED` sau khi User chốt.
- Không thiết kế DB hay kiến trúc kỹ thuật ở chặng này.
