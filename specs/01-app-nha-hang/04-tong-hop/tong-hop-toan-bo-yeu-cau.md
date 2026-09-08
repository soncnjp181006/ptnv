# Tổng Hợp Toàn Bộ Yêu Cầu — A3/A4/A5/A6 (tong-hop-toan-bo-yeu-cau.md)

Vị trí: `specs/01-app-nha-hang/04-tong-hop/tong-hop-toan-bo-yeu-cau.md`
Căn cứ: DEC-001 tới DEC-006, DEC-R01, DEC-R02, DEC-O01, DEC-P01 `CONFIRMED`. DEC-K01, DEC-B01, DEC-A01 `PROPOSED`.

## A3 — Khả thi
- Kỹ thuật: khả thi với thí điểm hẹp. Khó nhất là concurrent bàn, auto-release 60 phút, đối soát ví lơ lửng.
- Vận hành: fine dining Tier Lớn cần đào tạo lễ tân, bếp, thu ngân theo quy trình mới.
- Dữ liệu: phân tách chuỗi và điểm ngay từ đầu.
- Tích hợp: cổng ví / thẻ và sao kê QR là phụ thuộc ngoài lớn nhất.

## A4 — Phạm vi và MVP
- MVP thí điểm 1 tới 2 điểm: đặt bàn 60 phút + cọc, gọi món hủy chặt, bếp cơ bản, thu tiền mặt + QR, đối soát ca, báo cáo điểm.
- Pha 2: thêm ví / thẻ đầy đủ, Waitlist, báo cáo hợp nhất chuỗi, phân quyền chi tiết.
- Non-goal MVP: giao hàng, mang về, kho tự động, thân thiết phức tạp.
- Success: không trùng bàn, không double-charge, không đóng ca lệch không giải trình.

## A5 — Rủi ro và lộ trình
- Rủi ro: giữ 60 phút nghẽn cao điểm, ví lơ lửng kẹt ca, hủy chặt gây kẹt duyệt.
- Lộ trình: thí điểm → ổn định đối soát → nhân rộng điểm → mở ví / thẻ → báo cáo chuỗi.
- Rollback: tắt đặt online, ghi tay, thu tay khi sự cố.

## A6 — Backlog tổng
- Epic đặt bàn: giữ 60 phút, cọc, auto-release, audit.
- Epic gọi món: DRAFT tự do, sau DRAFT cần duyệt.
- Epic bếp: hàng chờ, báo hết, làm lại ưu tiên.
- Epic thanh toán: 3 kênh, khóa lơ lửng, hoàn có duyệt, đóng ca.
- Epic báo cáo và phân quyền: theo ngày kinh doanh (chờ chốt), hạn mức hoàn (chờ chốt).
- Mỗi story phải có acceptance quan sát được, trace về rule và state đã chốt.
