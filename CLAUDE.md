# CLAUDE.md — Quy Chuẩn Hệ Thống & Quản Trị Cho Claude Code (.agent-system/memory/)

Tài liệu này là **Chỉ Thị Tối Cao (System Governance Directive)** dành riêng cho **Claude Code** khi hoạt động trong workspace này. Claude Code tự động nạp tệp này khi khởi động phiên làm việc.

---

## ⚠️ BẮT BUỘC NẠP BỘ NHỚ QUẢN TRỊ (MANDATORY MEMORY INGESTION)

Mỗi khi nhận lệnh từ người dùng, thực thi slash command hoặc thực hiện bất kỳ tác vụ lập trình nào, Claude Code **BẮT BUỘC PHẢI ĐỌC VÀ TUÂN THỦ** các tệp quy chuẩn tại thư mục `.agent-system/memory/`:

| Trụ Cột | Tệp Quy Chuẩn Cần Đọc | Yêu Cầu Tuân Thủ Tuyệt Đối |
| :--- | :--- | :--- |
| **1. Hiến Pháp** | `.agent-system/memory/constitution/constitution.md` | **Nguồn sự thật tối cao (Ground Truth)**: Bắt buộc tuân thủ các nghĩa vụ MUST, ranh giới công nghệ và tiêu chuẩn chất lượng DoD. |
| **2. Quyền Hạn** | `.agent-system/memory/authority/authority.md` | **Ma trận thẩm quyền & 3 cấp độ tự chủ**: L0 (Read-Only: tự động), L1 (Bounded Work: tự động trong task), L2 (Human-Gated: **BẮT BUỘC** xin phép User trước khi commit, push, xóa tệp hoặc chạy lệnh phá hủy). |
| **3. Ngữ Cảnh** | `.agent-system/memory/context/context.md` | **Ngăn ngừa Context Drift**: Nạp ngữ cảnh phiên làm việc và bảo toàn trạng thái dự án. |
| **4. Bộ Nhớ Task** | `.agent-system/memory/memory/memory.md` | **Sổ cái thực thi**: Đối chiếu các yêu cầu, tiền lệ xử lý và ghi nhận nhật ký task. |
| **5. Quy Trình** | `.agent-system/memory/workflows/workflows.md` | **Quy trình 10 bước chuẩn hóa**: Deep-Thinking ➔ Clarify tuần tự 200+ ➔ Flowchart 3 tầng (Ngày hợp lý trước Giờ hợp lệ) ➔ Plan ➔ Tasks ➔ Implement ➔ Đối chiếu ➔ Mock Test ➔ Test thực tế có timeout ➔ Verify. |
| **6. Quan Sát** | `.agent-system/memory/observability/observability.md` | **Giám sát runtime**: Tuân thủ 4 cấp độ log và 8 trường bắt buộc cho shell logs (`timestamp`, `command`, `cwd`, `exit_code`, `duration_ms`, `stdout_preview`, `stderr_preview`, `status`). |
| **7. Khôi Phục** | `.agent-system/memory/recovery/recovery.md` | **Khôi phục sự cố không phá hủy**: 6 bước Detect ➔ Inspect ➔ Classify ➔ Recover ➔ Validate ➔ Continue; tối đa 3 lần retry trước khi Escalation. |
| **8. Tiến Hóa** | `.agent-system/memory/evolution/evolution.md` | **Tự hoàn thiện an toàn**: Rào chắn Zero-Regression và quy trình phê duyệt Human Gate L2. |

---

## 🛠️ CÁC LỆNH SLASH KHẢ DỤNG TRONG CLAUDE CODE (.claude/commands/)

Claude Code nhận diện và hỗ trợ các lệnh slash sau từ thư mục `.claude/commands/`:
- `/agent-constitution`: Đọc và kiểm tra tuân thủ Hiến pháp hệ thống (§1 - §15).
- `/agent-authority`: Kiểm tra thẩm quyền L0, L1, L2 trước khi thực thi.
- `/agent-context`: Nạp và đồng bộ ngữ cảnh làm việc.
- `/agent-memory`: Ghi nhận và tra cứu bộ nhớ công việc, tiền lệ.
- `/agent-workflows`: Thực hiện quy trình chuẩn 10 bước phát triển tính năng.
- `/agent-observability`: Kiểm tra logs và giám sát runtime.
- `/agent-recovery`: Kích hoạt quy trình khôi phục sự cố 6 bước.
- `/agent-evolution`: Đề xuất cải tiến và tiến hóa hệ thống an toàn.
- `/agent-update`: Đồng bộ và cập nhật an toàn hệ thống (Safe Update SHA-256).

---

## 🚫 CÁC NGUYÊN TẮC BẤT BIẾN (CLAUDE CODE INVARIANTS)

1. **Host-Centric AI**: Claude Code là AI Engine chính. Framework không yêu cầu Model hay API key riêng.
2. **Zero-Python Rule (§9)**: Thư mục `.agent-system/` chỉ chứa tài liệu Markdown, JSON, YAML. Tuyệt đối cấm tạo hoặc sửa tệp `.py` trong `.agent-system/`.
3. **Ngôn Ngữ Chuẩn Hóa**: Toàn bộ tài liệu, giải thích, comment dùng **Tiếng Việt**; toàn bộ mã nguồn, tên biến, tên hàm, commit message dùng **Tiếng Anh**.
4. **Không Tự Ý Commit/Push**: Tuyệt đối không tự ý chạy lệnh `git commit` hoặc `git push` trừ khi người dùng ra lệnh bằng văn bản rõ ràng.
