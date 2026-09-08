# 05 — Bộ Quy Tắc Nghiệp Vụ: Quản Lý Tài Liệu Học Tập

## BR-DOC-01 — Định dạng cho phép
- **WHEN:** Learner tải tài liệu học tập.
- **MUST:** Chỉ nhận `PDF/DOCX/TXT/MD`; từ chối còn lại 400 `UNSUPPORTED_TYPE`.
- **OTHERWISE:** Kẻ tấn công cài mã độc qua file thực thi.
- **AFFECTS:** UC-DOC-01. **SOURCE:** Quy ước an toàn file. **STATUS:** APPROVED.

## BR-DOC-02 — Quota Free 10 TL / 50MB
- **WHEN:** Upload ở plan FREE.
- **MUST:** Chặn khi `docs_count ≥ 10` hoặc `storage_used + file > 50MB`, trả 403 + upsell Paid 99k.
- **OTHERWISE:** Vỡ chi phí lưu trữ + AI.
- **AFFECTS:** `quotas`, UC-DOC-12. **SOURCE:** Quy ước freemium đã chốt. **STATUS:** APPROVED.

## BR-DOC-03 — Trần file Free 20MB
- **WHEN:** File Free vượt 20MB.
- **MUST:** Từ chối 413, hướng dẫn nén/tách chương.
- **OTHERWISE:** Worker parse treo mùa cao điểm.
- **AFFECTS:** UC-DOC-01. **SOURCE:** Giới hạn worker. **STATUS:** APPROVED.

## BR-DOC-04 — Chỉ READY mới hỏi AI
- **WHEN:** Hỏi AI / sinh Quiz theo tài liệu.
- **MUST:** Retriever chỉ lấy chunks của docs `READY` + PRIVATE của chính learner.
- **OTHERWISE:** Trả lời từ tài liệu lỗi/thiếu gây sai kiến thức.
- **AFFECTS:** RAG, Quiz. **SOURCE:** Chất lượng học tập. **STATUS:** APPROVED.

## BR-DOC-05 — Chia chunks chuẩn RAG
- **WHEN:** Parse thành công.
- **MUST:** Cắt 800 tokens, overlap 100, giữ `page_no`, embed và index vector.
- **OTHERWISE:** Trích dẫn trang sai, AI bịa nguồn.
- **AFFECTS:** `doc_chunks`, UC-DOC-02. **SOURCE:** Cấu hình RAG. **STATUS:** APPROVED.

## BR-DOC-06 — Riêng tư mặc định
- **WHEN:** Tạo document.
- **MUST:** `visibility=PRIVATE`; chia sẻ chỉ qua link lớp có kiểm soát.
- **OTHERWISE:** Lộ đề cương/ bài làm của học sinh.
- **AFFECTS:** UC-DOC-15. **SOURCE:** Privacy học sinh. **STATUS:** APPROVED.

## BR-DOC-07 — Gỡ bản quyền + giữ vết
- **WHEN:** Admin gỡ đề thi lậu/vi phạm.
- **MUST:** Chuyển DELETED + `reason=COPYRIGHT`, ghi audit, thông báo learner.
- **OTHERWISE:** Liên đới bản quyền.
- **AFFECTS:** UC-DOC-14. **SOURCE:** Chính sách nội dung. **STATUS:** APPROVED.

## BR-DOC-08 — Xóa mềm 30 ngày
- **WHEN:** Learner xóa tài liệu.
- **MUST:** Giữ S3 + vector 30 ngày rồi purge cứng.
- **OTHERWISE:** Mất tài liệu ôn thi do xóa nhầm.
- **AFFECTS:** UC-DOC-10/11. **SOURCE:** Quy ước dữ liệu. **STATUS:** APPROVED.
