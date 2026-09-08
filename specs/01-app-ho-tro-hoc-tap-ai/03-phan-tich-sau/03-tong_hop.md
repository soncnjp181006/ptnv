# Báo Cáo Tổng Hợp Chặng Phân Tích Sâu (03-tong_hop.md)

## 1. Độ Bao Phủ

- 6 nghiệp vụ con, mỗi module đủ 19 tệp, tổng 114 tệp + 2 tệp tổng quan = 116 tệp.
- Mọi module có Flowchart L0/L1/L2 đánh số edge, nhánh có điều kiện rõ ràng.
- Không có file nghiệp vụ trong `docs/`, toàn bộ trong `specs/01-app-ho-tro-hoc-tap-ai/`.

## 2. Chuỗi Truy Vết Lõi

```text
REQ Free+Paid ➔ CAP-01..06 ➔ 6 module ➔ Use Case ➔ Scenario (Happy/Alternative/Exception/Recovery)
➔ Rule BR-V/S/P/A/T/C/R/F/X/D/RV/N/AU ➔ State ➔ Data/Event ➔ Acceptance ➔ Backlog Epic/Story
```

Không có orphan rule lõi. Mọi mutation AI tốn chi phí đều kiểm tra quota. Mọi giao dịch tiền đều idempotent + đối soát.

## 3. Quyết Định & Rủi Ro Nổi Bật

- Toàn bộ DEC ở trạng thái `PROPOSED` (`Source: AGENT_INFERENCE`), không có P0 `UNRESOLVED` chặn thiết kế.
- Rủi ro lớn nhất: chi phí `LLM` vượt doanh thu Free, AI hallucination, webhook thanh toán treo `UNKNOWN`, vi phạm bản quyền tài liệu.
- Mitigation đã mô hình hóa: quota nguyên tử + rollback, trích dẫn nguồn + báo cáo sai, ingest webhook idempotent + đối soát 15 phút tới 24 giờ, tài liệu PRIVATE mặc định + takedown tay.

## 4. Sẵn Sàng Sang A3 ➔ A6 Tổng Hợp

Đủ đầu vào cho tổng hợp khả thi, phạm vi MVP, roadmap và backlog tại `04-tong-hop/tong-hop-toan-bo-yeu-cau.md`.
