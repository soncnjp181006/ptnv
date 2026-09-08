# Mô Hình Quy Trình Gọi Món Tại Bàn (01-mo-hinh-quy-trinh-nghiep-vu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/01-mo-hinh-quy-trinh-nghiep-vu.md`
Tiền đề: bàn đã `SEATED` từ đặt bàn. Fine dining, Tablet / POS và Mobile khách cùng ghi món.

## 1. L0 — Context Flow

```mermaid
flowchart TB
    A["Phục vụ / Khách"] -->|"1. Ghi món tại bàn"| B["Hệ thống gọi món"]
    B -->|"2. Chuyển yêu cầu chế biến"| C["Bếp / Bar"]
    C -->|"3. Báo hết / nhận / xong món"| B
    B -->|"4. Cập nhật đơn và trạng thái món"| D["Đơn của bàn"]
    E["Thu ngân"] -->|"5. Chốt đơn sang thanh toán"| B
```

## 2. L1 — Core Process Flow

```mermaid
flowchart TB
    A["Bắt đầu: bàn SEATED"] -->|"1. Chọn món từ thực đơn chuỗi"| B{"Món còn phục vụ?"}
    B -->|"2A — Hết"| C["Báo hết, gợi ý món thay thế"]
    B -->|"2B — Còn"| D["Thêm vào đơn nháp"]
    D -->|"3. Xác nhận gửi bếp"| E["Chuyển KITCHEN_SENT"]
    E -->|"4. Bếp xác nhận"| F["Bếp chế biến"]
    F -->|"5. Báo món xong"| G["Phục vụ mang món"]
    C -->|"6"| H(["Kết thúc lượt ghi"])
    G -->|"7"| H
```

## 3. L2 — Exception & Recovery Flow

```mermaid
flowchart TB
    A["Đơn nháp / đã gửi bếp"] -->|"1. Yêu cầu sửa / hủy món"| B{"Món ở bước nào?"}
    B -->|"2A — Chưa gửi bếp"| C["Cho sửa / hủy trực tiếp"]
    B -->|"2B — Đã gửi, chưa làm"| D["Xin bếp xác nhận hủy"]
    B -->|"2C — Đang làm / xong"| E["Cần quản lý duyệt, có thể tính phí"]
    D -->|"3A — Bếp đồng ý"| C
    D -->|"3B — Bếp từ chối"| F["Giữ nguyên món, giải thích cho khách"]
    E -->|"4A — Duyệt"| G["Hủy có phí / ghi audit"]
    E -->|"4B — Từ chối"| F
    A -->|"5. Gửi trùng 2 lần"| H["Idempotency: chỉ ghi một lần"]
    C -->|"6"| I(["Kết thúc"])
    F -->|"7"| I
    G -->|"8"| I
    H -->|"9"| I
```

Ngưỡng sửa / hủy ở L2 nhánh 2 đang là `PROPOSED`, chờ DEC-O01.
