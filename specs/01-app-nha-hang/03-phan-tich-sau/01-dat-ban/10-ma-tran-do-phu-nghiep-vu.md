# Ma Trận Độ Phủ Nghiệp Vụ Đặt Bàn (10-ma-tran-do-phu-nghiep-vu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/10-ma-tran-do-phu-nghiep-vu.md`

| Dimension | Phủ | Ghi chú |
| :--- | :--- | :--- |
| Happy path | Có | UC-01 tới UC-05 |
| Alternative | Có | Ghép bàn, dời giờ, đến sớm |
| Business exception | Có | Hết bàn, hủy sát giờ, bom bàn |
| Technical exception | Có | Cổng cọc timeout, job trùng, mất mạng |
| Recovery | Có | Retry cọc, đề xuất lại, override quản lý |
| State | Có | `HOLD`, `RESERVED`, `SEATED`, `CANCELLED`, `RELEASED` |
| Rule | Có | BR-V01, S01, T01, P01, C01, F01, N01, AU01 |
| Authority | Có | Lễ tân, quản lý điểm, hệ thống |
| Time | Có | 60 phút fine dining, cutoff hủy |
| Data / Audit | Có | Đặt, bàn, cọc, audit |

Tự đánh giá: đủ phủ lõi để sang backlog. Chưa cần 100% tuyệt đối.
