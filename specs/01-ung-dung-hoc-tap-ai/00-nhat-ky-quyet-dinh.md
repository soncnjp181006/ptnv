# Nhật Ký Quyết Định — Ứng Dụng Học Tập AI

> Trạng thái: ACTIVE
> Quy tắc: APPEND-ONLY. Mỗi quyết định phải có Q-ID, A-ID, DEC-ID.
> Nguồn gốc: Mọi Q&A từ A1→A6 được ghi nguyên văn tại đây trước khi sửa bất kỳ artifact nào khác.

---

## Q-001: Phân khúc lớp học và môn học ban đầu

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-001 |
| **Decision ID** | DEC-001 |
| **Priority** | P0 |
| **Stage** | A1 Discovery |
| **Question** | Ứng dụng hướng đến phân khúc lớp học nào và phạm vi môn học ban đầu là gì? |
| **Context** | Yêu cầu hiện tại liệt kê 4 môn: Toán, Lý, Hóa, Sinh. Chưa rõ là cấp học nào (THCS, THPT, Đại học) và có mở rộng môn sau không. |
| **Why it matters** | Phân khúc quyết định nội dung câu hỏi, độ khó AI, cơ chế đề xuất, curriculum mapping và pricing. |
| **Options** | **A**: Chỉ THPT (lớp 10-12). **B**: THCS + THPT (lớp 6-12). **C**: Từ THCS đến Đại học (lớp 6+). **D**: Mọi cấp học. |
| **Agent recommendation** | B — THCS + THPT. Đủ rộng cho product-market fit ban đầu, vẫn tập trung đủ để xây đề.JSON quality. |
| **Expected answer format** | Chọn 1 hoặc nhiều phương án; có thể bổ sung phạm vi khác. |
| **Status** | CONFIRMED |
| **User selected option(s)** | D |
| **User free-text answer (nguyên văn)** | D |
| **User clarification / rationale (nếu có)** | *Không có — User chọn trực tiếp D* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-002: Cơ chế tạo đề tương tự (similar question generation)

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-002 |
| **Decision ID** | DEC-002 |
| **Priority** | P0 |
| **Stage** | A2 Deep Analysis |
| **Question** | Khi AI sinh đề tương tự từ đề gốc, user duyệt trước khi lưu vào kho đề. Cơ chế duyệt là gì? |
| **Context** | Có nhiều cách: approve/reject từng câu, chỉnh sửa rồi approve, duyệt cả đề, auto-approve với threshold. |
| **Why it matters** | Quyết định UX, latency, storage cost và quality control của kho đề. |
| **Options** | **A**: Duyệt từng câu hỏi. **B**: Duyệt cả đề (batch). **C**: User chỉnh sửa rồi approve đề. **D**: Auto-approvè đề có điểm chất lượng > threshold. |
| **Agent recommendation** | C — cho phép chỉnh sửa + approve đề, vì user là học sinh/giáo viên, cần kiểm soát chất lượng nhanh. |
| **Expected answer format** | Chọn 1 phương án; có thể kết hợp. |
| **Status** | CONFIRMED |
| **User selected option(s)** | D |
| **User free-text answer (nguyên văn)** | D |
| **User clarification / rationale (nếu có)** | *Không có — User chọn trực tiếp D* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-003: Phạm vi cộng đồng (community)

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-003 |
| **Decision ID** | DEC-003 |
| **Priority** | P1 |
| **Stage** | A1 Discovery |
| **Question** | Tính năng cộng đồng trao đổi bao gồm những khả năng nào? |
| **Context** | "Cộng đồng trao đổi" có thể hiểu là: hỏi đáp, chia sẻ đề, nhóm học, hoặc tất cả. |
| **Why it matters** | Quyết định social graph, content moderation, notification, và complexity của MVP. |
| **Options** | **A**: Chỉ hỏi đáp (Q&A). **B**: Chia sẻ đề + bình luận. **C**: Nhóm học (study group). **D**: Tất cả các tính năng trên. |
| **Agent recommendation** | D — tất cả, vì synergy giữa các use case rõ ràng và rất phù hợp mục tiêu "trao đổi". |
| **Expected answer format** | Đơn chọn hoặc đa chọn. |
| **Status** | CONFIRMED |
| **User selected option(s)** | D |
| **User free-text answer (nguyên văn)** | D |
| **User clarification / rationale (nếu có)** | *Không có — User chọn trực tiếp D* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-004: Gói Plus/Pro — phân biệt giá trị

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-004 |
| **Decision ID** | DEC-004 |
| **Priority** | P0 |
| **Stage** | A2 Deep Analysis |
| **Question** | Điểm khác biệt cốt lõi giữa gói Free, Plus và Pro là gì? |
| **Context** | Freemium cần có feature gap rõ ràng để convert. Có thể phân biệt theo: lượng đề, quality AI, storage, community... |
| **Why it matters** | Pricing, conversion funnel, infrastructure cost, và cả feature gating trong code. |
| **Options** | **A**: Lượng đề + số lần AI giải/ngày. **B**: Tính năng nâng cao (explanations, video, analytics). **C**: Thời gian lưu trữ đề + dung lượng. **D**: Kết hợp nhiều tiêu chí. |
| **Agent recommendation** | D — kết hợp A + B + C, rõ ràng và dễ upsell. |
| **Expected answer format** | Đa chọn hoặc mô tả tự do. |
| **Status** | CONFIRMED |
| **User selected option(s)** | D |
| **User free-text answer (nguyên văn)** | D |
| **User clarification / rationale (nếu có)** | *Không có — User chọn trực tiếp D* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-005: AI hỗ trợ giải đề trên lớp (real-time)

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-005 |
| **Decision ID** | DEC-005 |
| **Priority** | P1 |
| **Stage** | A2 Deep Analysis |
| **Question** | Khi học sinh chụp đề trên lớp để AI hỗ trợ giải, hệ thống cần lưu lại hoạt động này không? |
| **Context** | Có thể chỉ hỗ trợ giải tức thì (ephemeral), hoặc lưu vào lịch sử cá nhân, hoặc tích hợp với kho đề sau khi duyệt. |
| **Why it matters** | Ảnh hưởng đến privacy policy, storage cost, và downstream feature "sinh đề tương tự từ lịch sử". |
| **Options** | **A**: Không lưu — chỉ giải tức thì, ephemeral. **B**: Lưu vào lịch sử cá nhân của user. **C**: Lưu và prompt đề xuất lưu vào kho đề sau duyệt. |
| **Agent recommendation** | B — lưu lịch sử cá nhân, cho phép user export/import sau. Không mặc định đưa vào kho đề chung. |
| **Expected answer format** | Chọn 1. |
| **Status** | CONFIRMED |
| **User selected option(s)** | B (với điều kiện bổ sung) |
| **User free-text answer (nguyên văn)** | B, nhưng hệ thống sẽ lưu câu trả lời + đề vào kho để admin duyệt |
| **User clarification / rationale (nếu có)** | *User chọn B (lưu lịch sử cá nhân) nhưng yêu cầu thêm: đề + câu trả lời tự động được gửi vào kho đề công khai để admin duyệt. Đây là hành vi mặc định, không phải prompt tùy chọn.* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-006: Nền tảng và ngôn ngữ

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-006 |
| **Decision ID** | DEC-006 |
| **Priority** | P0 |
| **Stage** | A1 Discovery |
| **Question** | Ứng dụng chạy trên nền tảng nào và ngôn ngữ chính là gì? |
| **Context** | Ảnh hưởng tech stack, OCR quality, notification mechanism, và market fit. |
| **Why it matters** | Mobile-first hay web-first? Tiếng Việt hay đa ngôn ngữ? Quyết định kiến trúc kỹ thuật. |
| **Options** | **A**: Mobile app (iOS + Android) + tiếng Việt. **B**: Web app + tiếng Việt. **C**: Mobile + Web + tiếng Việt. **D**: Multi-platform + multi-language. |
| **Agent recommendation** | C — Mobile + Web, tiếng Việt. Phù hợp thói quen học sinh Việt Nam và cho phép capture nhanh trên mobile. |
| **Expected answer format** | Chọn 1 hoặc mô tả khác. |
| **Status** | CONFIRMED |
| **User selected option(s)** | D |
| **User free-text answer (nguyên văn)** | D, ứng dụng sẽ hỗ trợ dịch -> toàn thế giới -> hay hơn đúng ko |
| **User clarification / rationale (nếu có)** | *User chọn D (Multi-platform + multi-language). Lý do: hỗ trợ dịch để mở rộng toàn cầu.* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-007: Kiểm soát chất lượng kho đề

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-007 |
| **Decision ID** | DEC-007 |
| **Priority** | P1 |
| **Stage** | A2 Deep Analysis |
| **Question** | Ai có quyền publish đề vào kho đề công khai? Có moderation không? |
| **Context** | Nếu mọi user đều có thể đưa đề lên, cần cơ chế chống spam, lỗi, hoặc đề có bản quyền. |
| **Why it matters** | Ảnh hưởng đến trust của product, chi phí moderation, và growth loop. |
| **Options** | **A**: Chỉ giáo viên/admin được publish. **B**: User gửi → admin duyệt. **C**: User publish tự do + community report. **D**: Kết hợp B + C. |
| **Agent recommendation** | D — user gửi, admin duyệt lần đầu, sau đó community report để scale. |
| **Expected answer format** | Chọn 1 hoặc mô tả. |
| **Status** | CONFIRMED |
| **User selected option(s)** | Không có kho công khai |
| **User free-text answer (nguyên văn)** | không có kho công khai nhé, kho chỉ dùng cho gói mất tiền thôi |
| **User clarification / rationale (nếu có)** | *User bác bỏ hoàn toàn khái niệm "kho đề công khai". Kho đề chỉ tồn tại dành riêng cho user trả phí (Plus/Pro). Điều này thay đổi business model và loại bỏ moderation ở tầm công khai.* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-008: Dữ liệu đề gốc và quyền tác giả

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-008 |
| **Decision ID** | DEC-008 |
| **Priority** | P1 |
| **Stage** | A1 Discovery |
| **Question** | Khi user chụp đề, hệ thống có cần OCR nâng cao và lưu metadata đề (trường, năm, môn, chương) không? |
| **Context** | Metadata giúp search, filter, recommendation và analytics. OCR quality ảnh hưởng UX. |
| **Why it matters** | Quyết định data model, AI prompt quality, và cost của OCR service. |
| **Options** | **A**: OCR cơ bản + user nhập metadata thủ công. **B**: OCR nâng cao + hệ thống gợi ý metadata. **C**: OCR nâng cao + auto-fill metadata bằng AI. |
| **Agent recommendation** | B — OCR nâng cao + gợi ý metadata, user confirm. Cân bằng automation và control. |
| **Expected answer format** | Chọn 1. |
| **Status** | CONFIRMED |
| **User selected option(s)** | B |
| **User free-text answer (nguyên văn)** | B |
| **User clarification / rationale (nếu có)** | *Không có — User chọn trực tiếp B* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-009: Retention và quyền sở hữu dữ liệu user

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-009 |
| **Decision ID** | DEC-009 |
| **Priority** | P1 |
| **Stage** | A2 Deep Analysis |
| **Question** | User sở hữu dữ liệu đề cá nhân như thế nào? Có quyền xóa, export, hoặc chuyển nhượng không? |
| **Context** | Liên quan đến privacy, GDPR-like compliance, và trust. |
| **Why it matters** | Ảnh hưởng data architecture, legal compliance, và feature scope. |
| **Options** | **A**: User sở hữu hoàn toàn, có quyền export/delete mọi lúc. **B**: Hệ thống giữ bản sao để cải thiện AI. **C**: Để xác định sau, ghi nhận là requirement. |
| **Agent recommendation** | A — user sở hữu hoàn toàn, có quyền export/delete. Ghi rõ trong terms. |
| **Expected answer format** | Chọn 1. |
| **Status** | CONFIRMED |
| **User selected option(s)** | A + B |
| **User free-text answer (nguyên văn)** | A + B |
| **User clarification / rationale (nếu có)** | *Multi-select: User chọn cả A (user sở hữu hoàn toàn, có quyền export/delete) và B (hệ thống giữ bản sao để cải thiện AI). Cần có điều khoản rõ trong terms về quyền sở hữu vs license để improve AI.* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---

## Q-010: Mô hình community — quyền truy cập theo gói

| Trường | Nội dung |
|--------|----------|
| **Q-ID** | Q-010 |
| **Decision ID** | DEC-010 |
| **Priority** | P1 |
| **Stage** | A2 Deep Analysis |
| **Question** | Các tính năng community có bị giới hạn theo gói Free/Plus/Pro không? |
| **Context** | Ví dụ: Free chỉ xem, Plus được hỏi, Pro được tạo nhóm. |
| **Why it matters** | Ảnh hưởng monetization, engagement, và permission system. |
| **Options** | **A**: Không giới hạn — community mở hoàn toàn. **B**: Giới hạn theo gói (read/write/create). **C**: Chỉ giới hạn một số tính năng nâng cao. |
| **Agent recommendation** | B — giới hạn rõ, dễ upsell và quản lý moderation. |
| **Expected answer format** | Chọn 1. |
| **Status** | CONFIRMED |
| **User selected option(s)** | A + C |
| **User free-text answer (nguyên văn)** | A + C |
| **User clarification / rationale (nếu có)** | *Multi-select: User chọn A (cộng đồng cơ bản mở hoàn toàn) kết hợp C (chỉ giới hạn tính năng nâng cao theo gói). Tức là: community cơ bản không giới hạn, nhưng một số tính năng nâng cao bị gating theo plan.* |
| **Answered at** | 2026-09-08 |
| **Recorded by** | USER |

---


