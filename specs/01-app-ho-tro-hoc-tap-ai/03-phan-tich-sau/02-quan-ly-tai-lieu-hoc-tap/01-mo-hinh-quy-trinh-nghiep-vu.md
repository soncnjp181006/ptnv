# 01 — Mô Hình Quy Trình Nghiệp Vụ: Quản Lý Tài Liệu Học Tập

> Module: `learning-document` | Định dạng: `PDF/DOCX/TXT/MD` | Quota Free: 10 tài liệu, 50MB | Vòng đời: `UPLOADING→PROCESSING→READY→ARCHIVED/DELETED`

## 1. Mục đích
- Chuẩn hóa tải lên, trích xuất văn bản làm ngữ liệu RAG hỏi AI, gắn thẻ môn học, tìm kiếm, lưu trữ/xóa và quyền riêng tư.
- Đầu vào cho `DocumentService`, bảng `documents`, `doc_chunks`, object storage.

## 2. L0 — Sơ đồ ngữ cảnh (Context)

```mermaid
flowchart TB
    L[LearnerFree / LearnerPaid] -->|"1. Tải lên PDF/DOCX/TXT/MD"| DOC[DocumentService]
    DOC -->|"2. Kiểm tra quota 10 TL + 50MB"| QUO[QuotaService]
    DOC -->|"3. Lưu file gốc"| OBJ[(ObjectStorage S3)]
    DOC -->|"4. Trích xuất + chia chunks"| AIW[AI Pipeline: extract + embed]
    AIW -->|"5. Index vector"| VEC[(VectorDB pgvector)]
    L -->|"6. Tìm kiếm / hỏi AI trên tài liệu"| DOC
    AD[Admin] -->|"7. Gỡ tài liệu vi phạm bản quyền"| DOC
```

## 3. L1 — Quy trình lõi (Core Process)

```mermaid
flowchart TB
    A[Start: Learner chọn file] -->|"1. Validate đuôi + dung lượng"| B{Quá quota?}
    B -->|"2A — Có: vượt 10 TL hoặc 50MB"| R1[Chặn 403 + gợi ý nâng Paid 99k]
    B -->|"2B — Không: còn quota"| C[Tạo UPLOADING + upload S3]
    C -->|"3. Chuyển PROCESSING: trích văn bản"| D[Parse PDF/DOCX/TXT/MD]
    D -->|"4. Gắn thẻ môn học + khối lớp"| E[READY: tìm kiếm + hỏi AI]
    E -->|"5. Lưu trữ khi hết kỳ học"| F[ARCHIVED]
    F -->|"6. Khôi phục nếu cần"| E
```

## 4. L2 — Ngoại lệ & phục hồi (Exception & Recovery)

```mermaid
flowchart TB
    S[File vào PROCESSING] -->|"1. Parse thử"| P1{Trích xuất được?}
    P1 -->|"2A — Có"| P2{Dung lượng sau parse quá lớn?}
    P1 -->|"2B — Không: file lỗi/scan mờ"| F1[Đánh dấu FAILED + báo learner tải lại]
    P2 -->|"3A — Có: vượt trần"| F2[Cắt + tóm tắt, báo Paid mở rộng]
    P2 -->|"3B — Không: vừa"| OK[Chia chunks 800 tokens + embed + READY]
    F1 -->|"4. Retry tối đa 3 lần"| RT[Queue retry DLQ]
    OK -->|"5. Ghi log xử lý + trừ quota"| LG[(doc_logs)]
```

## 5. Ghi chú
- Free: tối đa 10 tài liệu, tổng 50MB, mỗi file ≤ 20MB; Paid mở 500 tài liệu / 5GB.
- TXT/MD parse trực tiếp; PDF/DOCX qua worker async, timeout 5 phút/file.
- Tài liệu mặc định PRIVATE; chia sẻ chỉ qua link lớp học (module sau).
- Mọi thời gian log theo `Asia/Ho_Chi_Minh`.
