# Báo Cáo Chuỗi (05-bao-cao-chuoi gộp lõi)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/05-bao-cao-chuoi/`

## Flowchart
```mermaid
flowchart TB
    A["Đóng ca điểm bán"] -->|"1. Chốt doanh thu theo kênh"| B["Báo cáo điểm"]
    B -->|"2. Gửi hợp nhất"| C["Báo cáo chuỗi"]
    C -->|"3. Đối chiếu cọc, hoàn, phạt"| D["Kết quả hợp nhất"]
    B -->|"4A — Lệch"| E["Ghi chênh lệch, yêu cầu giải trình"]
    B -->|"4B — Khớp"| D
```

## Use case / Quy tắc / Quyết định
- UC: báo cáo điểm, báo cáo hợp nhất, giải trình chênh lệch.
- BR-D01 PROPOSED: không hợp nhất khi còn ca chưa đóng hoặc còn `PENDING_REVIEW` chưa bàn giao.
- DEC-B01 UNRESOLVED (P1): chốt báo cáo theo ngày kinh doanh (tới 2h sáng) hay ngày dương lịch? Đề xuất: ngày kinh doanh.
