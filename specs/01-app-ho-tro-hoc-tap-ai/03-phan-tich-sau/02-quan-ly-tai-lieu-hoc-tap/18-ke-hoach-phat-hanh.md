# 18 — Kế Hoạch Phát Hành: Tài Liệu Học Tập

## 1. Chiến lược
- Cờ: `docs_upload` → `doc_rag` → `doc_search` → `doc_lifecycle`. Canary 5% lớp 9 ôn Vi-ét trước.
- Worker deploy riêng, scale HPA theo queue; tắt `doc_rag` là hỏi AI fallback không nguồn.

## 2. Go/No-Go

| Hạng mục | Tiêu chí | Chủ trì |
|----------|----------|---------|
| Upload/quota | Chặn 50MB + upsell Paid đúng | Backend |
| Parse RAG | READY ≥95%, chunks có page_no | AI/QA |
| Search | p95 <600ms, đúng owner | QA |
| Privacy | Cross-owner 403 100% | Security |
| Purge | Cron 30 ngày xóa S3+vector | DevOps |
| Rollback | Requeue DLQ + tắt cờ search | DevOps |

## 3. Sau phát hành
- Theo dõi FAILED (scan mờ), queue lag mùa thi, tỉ lệ hỏi AI có trích dẫn; trực tăng worker cuối kỳ.
