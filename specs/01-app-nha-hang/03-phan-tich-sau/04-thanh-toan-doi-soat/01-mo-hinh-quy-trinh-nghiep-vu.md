# Mô Hình Thanh Toán Và Đối Soát (01-mo-hinh-quy-trinh-nghiep-vu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/04-thanh-toan-doi-soat/01-mo-hinh-quy-trinh-nghiep-vu.md`
Phạm vi: 3 kênh tiền mặt + QR chuyển khoản + ví / thẻ, cọc đã thu trừ vào hóa đơn, đối soát ca điểm bán.

## 1. L0 — Context Flow

```mermaid
flowchart TB
    A["Thu ngân"] -->|"1. Lập hóa đơn từ đơn"| B["Hệ thống thanh toán"]
    B -->|"2. Thu đa kênh"| C["Tiền mặt / Ngân hàng / Cổng ví"]
    C -->|"3. Kết quả thu / sao kê"| B
    B -->|"4. Đóng ca, đối soát chênh lệch"| D["Quản lý điểm"]
    B -->|"5. Báo cáo hợp nhất"| E["Quản trị chuỗi"]
```

## 2. L1 — Core Process Flow

```mermaid
flowchart TB
    A["Bắt đầu: chốt đơn"] -->|"1. Trừ cọc đã thu"| B["Tạo hóa đơn phải thu"]
    B -->|"2. Chọn kênh thu"| C{"Kênh nào?"}
    C -->|"3A — Tiền mặt"| D["Thu tiền mặt, in hóa đơn"]
    C -->|"3B — QR chuyển khoản"| E["Tạo QR, chờ báo có"]
    C -->|"3C — Ví / thẻ"| F["Gọi cổng ngoài"]
    D -->|"4"| G["Hóa đơn PAID"]
    E -->|"5A — Có tiền"| G
    E -->|"5B — Hết hạn"| H["Hủy lượt thu, cho thử lại"]
    F -->|"6A — Thành công"| G
    F -->|"6B — Thất bại"| H
    F -->|"6C — Timeout lơ lửng"| I["Khóa chờ đối soát"]
    G -->|"7"| J(["Kết thúc"])
    H -->|"8"| J
    I -->|"9"| J
```

## 3. L2 — Exception & Recovery Flow

```mermaid
flowchart TB
    A["Giao dịch lơ lửng"] -->|"1. Tra soát cổng / sao kê"| B{"Tiền đã trừ?"}
    B -->|"2A — Rồi"| C["Ghi PAID, cấm thu lại"]
    B -->|"2B — Chưa"| D["Mở lại cho thu mới"]
    B -->|"2C — Không rõ"| E["Giữ khóa, hẹn đối soát, không tạo hóa đơn mới"]
    A -->|"3. Hoàn tiền sau PAID"| F{"Ai duyệt hoàn?"}
    F -->|"4A — Trong hạn mức"| G["Thu ngân hoàn, ghi audit"]
    F -->|"4B — Vượt hạn mức"| H["Quản lý duyệt hoàn"]
    C -->|"5"| I(["Kết thúc"])
    D -->|"6"| I
    E -->|"7"| I
    G -->|"8"| I
    H -->|"9"| I
```

Chính sách khóa chờ ở L1 nhánh 6C và L2 đang là `PROPOSED`, chờ DEC-P01.
