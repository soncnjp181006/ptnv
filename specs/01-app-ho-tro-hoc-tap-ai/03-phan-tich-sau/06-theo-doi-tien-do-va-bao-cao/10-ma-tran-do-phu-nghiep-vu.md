# 10 — Ma Trận Độ Phủ Nghiệp Vụ: Theo Dõi Tiến Độ Và Báo Cáo

> P1 nhưng MVP cơ bản vẫn đủ tạo động lực và đo lường.

| Use case | MVP cơ bản | Lý do | Rủi ro nếu cắt |
| :--- | :--- | :--- | :--- |
| UC-601 ghi hỏi AI | Có | Nguồn event chính | Mất 50% hoạt động |
| UC-602 ghi Quiz | Có | Có điểm số đo lường | Không tính được avgScore |
| UC-603 streak | Có | Giữ chân rẻ nhất | Mất động lực daily |
| UC-604 dashboard | Có (7 ngày) | User thấy tiến bộ | Không cảm nhận giá trị |
| UC-605 báo cáo tuần | Có (email/in-app) | Chạm lại user churn | Tụt retention |
| UC-608 bù trễ | Có | Công bằng số liệu | Khiếu nại mất streak |
| UC-606 mục tiêu | Không (P2) | Cần UX đặt mục tiêu | Dời được |
| UC-607 huy hiệu | Cơ bản 3 badge | Động lực bổ sung | Dời mở rộng được |
| UC-609 xuất file | Không (P2) | Ít dùng ở MVP | Dời được |

## Kết luận
- MVP cơ bản: 6 use case (601–605 + 608) + 3 badge cứng; đủ demo giá trị P1.
- Phase 2: mục tiêu linh hoạt, badge mở rộng, xuất PDF, so sánh cohort.

## Lý do giữ/cắt từng nhóm (chi tiết)
- Giữ ingest async vì mọi số liệu downstream phụ thuộc nó; cắt là mù dữ liệu.
- Giữ streak vì là cơ chế giữ chân rẻ nhất (1 số nguyên + 1 job), hiệu quả đã chứng minh ở app học ngôn ngữ.
- Giữ dashboard 7 ngày vì user cần thấy tiến bộ ngay; chart 30 ngày/cohort để sau.
- Giữ báo cáo tuần vì là điểm chạm lại (re-engagement) duy nhất của P1; tần suất tuần đủ, ngày sẽ spam.
- Giữ bù trễ vì streak mất oan là khiếu nại top 1 của app streak — rẻ hơn xử lý khiếu nại.
- Cắt mục tiêu tùy biến vì cần thêm UX đặt/sửa/xóa mục tiêu (~1 sprint) mà giá trị chưa kiểm chứng.

## Backlog phase 2 (đã nhìn thấy, chưa làm)
1. Mục tiêu tuần tùy biến + thanh tiến trình + gợi ý điều chỉnh.
2. Hệ badge mở rộng (theo môn, theo khung giờ, theo chuỗi Quiz đúng).
3. Xuất báo cáo PDF/ảnh chia sẻ + leaderboard bạn bè (có đồng ý).
4. Phân tích theo môn/chủ đề yếu nhất + gợi ý ôn tập (nối module 04).

## Tiêu chí đưa use case vào MVP cơ bản (scoring)
| Tiêu chí (trọng số) | Giải thích |
| :--- | :--- |
| Đo được cốt lõi (×3) | Ingest AI/Quiz là nguồn mọi số liệu |
| Giữ chân rẻ (×3) | Streak + báo cáo tuần chi phí thấp, tác động cao |
| Thấy ngay giá trị (×2) | Dashboard 7 ngày cho user cảm nhận tiến bộ |
| Công bằng số liệu (×2) | Bù trễ chống khiếu nại mất streak |
| Mở rộng sau (×1) | Mục tiêu, badge+, xuất file để phase 2 |

## Cách đọc bảng độ phủ
- "Có" = đầy đủ luồng + test + vận hành trong 2 sprint.
- "Cơ bản 3 badge" = logic trao badge cứng, chưa có trang sưu tập badge đẹp.
- Mọi mục phase 2 đều có vé riêng, không lẫn vào MVP.
