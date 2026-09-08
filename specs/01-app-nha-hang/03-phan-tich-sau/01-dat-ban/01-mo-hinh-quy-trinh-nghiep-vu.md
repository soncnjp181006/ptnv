# Mô Hình Quy Trình Nghiệp Vụ Đặt Bàn (01-mo-hinh-quy-trinh-nghiep-vu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/01-mo-hinh-quy-trinh-nghiep-vu.md`
Phạm vi: Chuỗi Tier Lớn, kênh Ăn tại chỗ + Đặt bàn trước, 4 nền tảng.
Mọi chính sách số liệu (phút giữ bàn, giờ hủy) là `PROPOSED`, chờ User chốt ở `06-danh-sach-quyet-dinh.md`.

## 1. L0 — Context Flow

```mermaid
flowchart TB
    A["Khách hàng"] -->|"1. Gửi yêu cầu đặt bàn"| B["Hệ thống đặt bàn"]
    B -->|"2. Xác nhận / từ chối / đề xuất giờ khác"| A
    C["Lễ tân điểm bán"] -->|"3. Duyệt, xếp bàn, xác nhận khách đến"| B
    B -->|"4. Cập nhật trạng thái bàn"| D["Sơ đồ bàn điểm bán"]
    B -->|"5. Nhắc giờ, báo đến muộn / hủy"| E["Thông báo tới khách"]
    F["Quản trị chuỗi"] -->|"6. Cấu hình khung giờ, sức chứa, chính sách giữ chỗ"| B
```

## 2. L1 — Core Process Flow

```mermaid
flowchart TB
    A["Bắt đầu: tiếp nhận yêu cầu"] -->|"1. Kiểm tra điểm bán mở cửa"| B{"Điểm bán mở cửa?"}
    B -->|"2A — Không"| C["Từ chối, gợi ý điểm / ngày khác"]
    B -->|"2B — Có"| D["Kiểm tra ngày giờ hợp lệ"]
    D -->|"3. Ngày giờ hợp lệ"| E{"Còn bàn phù hợp?"}
    E -->|"4A — Không"| F["Đề xuất giờ lân cận / vào Waitlist"]
    E -->|"4B — Có"| G["Giữ tạm bàn"]
    G -->|"5. Khách xác nhận thông tin"| H{"Cần đặt cọc?"}
    H -->|"6A — Không"| I["Tạo đặt bàn RESERVED"]
    H -->|"6B — Có"| J["Thu cọc, đối soát cọc"]
    J -->|"7. Cọc thành công"| I
    J -->|"8. Cọc thất bại / hết hạn"| K["Nhả giữ tạm"]
    I -->|"9. Lễ tân xác nhận khách đến"| L["Chuyển SEATED"]
    C -->|"10"| M(["Kết thúc"])
    F -->|"11"| M
    K -->|"12"| M
    L -->|"13"| M
```

## 3. L2 — Exception & Recovery Flow

```mermaid
flowchart TB
    A["Đặt bàn RESERVED"] -->|"1. Tới giờ hẹn"| B{"Khách đến đúng giờ?"}
    B -->|"2A — Có"| C["SEATED, phục vụ tiếp"]
    B -->|"2B — Không"| D{"Quá grace period?"}
    D -->|"3A — Chưa quá"| E["Tiếp tục giữ, nhắc khách"]
    D -->|"3B — Quá"| F["Auto-release bàn"]
    F -->|"4. Xử lý cọc theo chính sách"| G{"Hoàn / phạt cọc?"}
    G -->|"5A — Hoàn"| H["Hoàn cọc, ghi audit"]
    G -->|"5B — Phạt / giữ"| I["Ghi nhận doanh thu cọc, ghi audit"]
    A -->|"6. Khách hủy trước giờ"| J{"Trước cutoff?"}
    J -->|"7A — Có"| H
    J -->|"7B — Không"| I
    A -->|"8. Trùng bàn do concurrent"| K["Arbitration: ưu tiên xác nhận trước, đề xuất bàn khác"]
    C -->|"9"| L(["Kết thúc"])
    H -->|"10"| L
    I -->|"11"| L
    K -->|"12"| L
```

## 4. Ghi chú đọc sơ đồ

- Edge đánh số thứ tự thực thi. Nhánh A / B ghi rõ điều kiện.
- `grace period`, `cutoff`, chính sách cọc chưa chốt số, đang là `PROPOSED` ở tệp quy tắc.
- Concurrent hai lễ tân cùng xếp một bàn được xử lý ở nhánh 8 của L2, chi tiết arbitration ở A2 sâu hơn.
