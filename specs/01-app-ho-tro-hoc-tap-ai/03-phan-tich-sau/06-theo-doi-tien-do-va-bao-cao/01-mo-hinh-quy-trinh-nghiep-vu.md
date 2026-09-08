# 01 — Mô Hình Quy Trình Nghiệp Vụ: Theo Dõi Tiến Độ Và Báo Cáo

> Module: `06-theo-doi-tien-do-va-bao-cao` | Ưu tiên P1, phân tích ở mức MVP cơ bản | Múi giờ `Asia/Ho_Chi_Minh`

## 1. Mục đích
- Ghi nhận sự kiện học (hỏi AI, Quiz, streak, thời gian, điểm) ➔ tổng hợp báo cáo tuần + biểu đồ tiến bộ.
- Tạo động lực duy trì thói quen, làm đầu vào upsell Paid (module 05).

## 2. L0 — Bối cảnh

```mermaid
flowchart TB
    U["fa:fa-user Hoc vien"] -->|"1. Hoc: hoi AI, lam Quiz"| S["fa:fa-chart-line He thong Tien do & Bao cao"]
    Q["fa:fa-robot Module AI / Quiz"] -->|"2. Phat su kien hoc"| S
    S -->|"3. Gui bao cao tuan, streak, huy hieu"| U
    S -->|"4. Doc quota, goi de goi y nang cap"| P["fa:fa-credit-card Module Goi & Quota"]
```

## 3. L1 — Quy trình chính

```mermaid
flowchart TB
    Start((Bat dau)) -->|"1. Phat sinh su kien hoc"| C1["Ingest event hoi AI, Quiz, login"]
    C1 -->|"2. Cap nhat tien do ngay"| C2["Cong thoi gian, dem streak, diem TB"]
    C2 -->|"3. Tong hop tuan"| C3["Chot tuan luc 00:00 thu Hai"]
    C3 -->|"3A — Du du lieu"| C4["Sinh bao cao tuan + bieu do"]
    C3 -->|"3B — Tuan trang, it du lieu"| C5["Gui nhac dong vien, goi y muc tieu"]
    C4 -->|"4. Trao huy hieu, thong bao"| End((Ket thuc))
    C5 -->|"4. Trao huy hieu, thong bao"| End
```

## 4. L2 — Chi tiết ingest + streak + chốt tuần

```mermaid
flowchart TB
    B1((Event den)) -->|"1. Nhan event chuan"| B2["Validate userId, type, occurredAt"]
    B2 -->|"2A — Event hop le"| B3["Ghi LearningEvent idempotent theo eventId"]
    B2 -->|"2B — Trung eventId"| B4["Bo qua, tra 200"]
    B2 -->|"2C — Sai dinh dang"| B5["Dead-letter + alert"]
    B3 -->|"3. Cap nhat DailyProgress"| B6["Cong timeSpent, quizScore, aiCount"]
    B6 -->|"4. Tinh streak"| B7["activeDay ? streak+1 : reset ve 1, bao ve mui gio"]
    B7 -->|"5. Den 00:00 thu Hai"| B8["Chot WeeklyReport tuan truoc"]
    B8 -->|"6A — Chot xong"| B9["Phat hanh bao cao + bieu do"]
    B8 -->|"6B — Thieu du lieu"| B10["Danh dau PARTIAL, gui ban rut gon"]
```

## 5. Quy ước chung
- Ngày học tính từ `00:00–23:59:59 Asia/Ho_Chi_Minh`; `activeDay = timeSpent >= 5 phút OR >= 1 Quiz OR >= 3 câu AI`.
- Tuần MVP: Thứ Hai–Chủ Nhật; chốt báo cáo `00:00 thứ Hai`, gửi trước 08:00.
- Mọi event có `eventId` duy nhất để ingest idempotent; sự kiện đến muộn ≤ 7 ngày vẫn được cộng bù.
