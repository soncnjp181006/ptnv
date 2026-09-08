# 06 — Danh Sách Quyết Định: Quản Lý Tài Liệu Học Tập

> `Status: PROPOSED`, `Source: AGENT_INFERENCE` — chờ user chốt.

| ID | Quyết định | Đề xuất | Thay thế | Ảnh hưởng | Status / Source |
|----|------------|---------|----------|-----------|-----------------|
| DEC-DOC-01 | Định dạng | PDF/DOCX/TXT/MD | Thêm PPTX/ảnh OCR | Phủ slide bài giảng vs OCR tốn kém | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-02 | Trần Free | 10 TL + 50MB + 20MB/file | 15 TL + 100MB | Chi phí S3/AI vs trải nghiệm | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-03 | OCR scan | Không OCR Sprint 1, báo FAILED + hướng dẫn | Tích OCR ngay | Tiến độ vs PDF scan đề thi | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-04 | Chunking | 800 tok/overlap 100 | 512 tok cho môn công thức | Trích dẫn vs chi phí embed | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-05 | Tag môn | AI gợi ý + learner xác nhận | Auto 100% | Đúng môn vs sai tag Lý/Hóa | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-06 | Tìm kiếm | Full-text + vector | Chỉ full-text Sprint 1 | Chất lượng tìm vs tiến độ | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-07 | Chia sẻ | PRIVATE + link lớp (Sprint 2) | Public thư viện chung | Lan tỏa vs bản quyền | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-08 | Công thức Toán | Giữ LaTeX/text, ảnh thành [figure] | Render ảnh công thức | Hỏi AI giải Toán vs render | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-09 | Purge | 30 ngày | 60 ngày cho mùa thi lại | An toàn xóa nhầm vs lưu trữ | PROPOSED / AGENT_INFERENCE |
| DEC-DOC-10 | Quota Paid | 500 TL / 5GB | Không giới hạn | Doanh thu 99k/990k vs lạm dụng | PROPOSED / AGENT_INFERENCE |

## Cần chốt sớm
- DEC-DOC-01/02/03 chặn thiết kế worker parse; đề nghị chốt trước Sprint 1.
