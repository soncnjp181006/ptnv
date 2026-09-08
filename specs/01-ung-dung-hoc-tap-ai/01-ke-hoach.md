# 01 — Kế Hoạch Phân Tích & Khám Phá
> Ứng dụng học tập có AI Agent hỗ trợ (Math, Physics, Chemistry, Biology)

Phiên bản: v1.0.0
Ngày tạo: 2026-09-08
Trạng thái: ACTIVE

---

## 1. Goal / Outcomes

Mục tiêu cốt lõi:
- Xây dựng baseline nghiệp vụ rõ ràng, có thể kiểm chứng cho ứng dụng học tập AI.
- Xác định capability, process, use case, state, rule, actor, data, event, risk cho các cụm tính năng cốt lõi.
- Chốt business policy trước khi chuyển sang Solution Design.

Outcomes mong đợi:
- Có đầy đủ artifact trong `specs/01-ung-dung-hoc-tap-ai/` đạt các cổng G1 → G7.
- Không còn P0 unresolved.
- Mọi quyết định nghiệp vụ trace được về User answer hoặc authoritative source.

---

## 2. Scope / Non-goals

### In-scope (dự kiến)
- Cộng đồng trao đổi (community: hỏi đáp, chia sẻ đề, nhóm học).
- OCR + AI hỗ trợ giải đề (chụp đề).
- Kho đề (question bank) với cơ chế sinh đề tương tự + duyệt.
- Gói pricing Free / Plus / Pro.
- Hỗ trợ 4 môn: Toán, Lý, Hóa, Sinh.

### Non-goals
- Thiết kế UI/UX chi tiết.
- Implementation code.
- Triển khai infrastructure production.
- Nội dung curriculum chi tiết từng chương.

---

## 3. Inputs / Sources

| Source | Loại | Nội dung |
|--------|------|----------|
| User prompt | FACT | Yêu cầu ban đầu về app học tập AI, 4 môn, cộng đồng, pricing, OCR. |
| Constitution | GOVERNANCE | Hiến pháp dự án tại `.agent-system/memory/constitution/constitution.md` |
| Authority | GOVERNANCE | Ma trận quyền hạn tại `.agent-system/memory/authority/authority.md` |
| Context | GOVERNANCE | Quy chuẩn ngữ cảnh tại `.agent-system/memory/context/context.md` |

---

## 4. Known Facts

| ID | Fact | Source | Status |
|----|------|--------|--------|
| F-01 | Ứng dụng dạy Toán, Lý, Hóa, Sinh. | User prompt | FACT |
| F-02 | Có agent AI hỗ trợ. | User prompt | FACT |
| F-03 | Có cộng đồng trao đổi. | User prompt | FACT |
| F-04 | Có gói Free, Plus, Pro. | User prompt | FACT |
| F-05 | User chụp đề → AI lưu vào bộ nhớ → sinh đề tương tự → user duyệt → lưu vào kho đề. | User prompt | FACT |
| F-06 | User chụp đề trên lớp → AI hỗ trợ giải. | User prompt | FACT |
| F-07 | Kho đề phong phú, linh hoạt. | User prompt | FACT |
| F-08 | Ứng dụng hỗ trợ mọi cấp học (KHÔNG giới hạn lớp). | User answer Q-001 | CONFIRMED |
| F-09 | Đề tương tự do AI sinh sẽ được auto-approve nếu quality score vượt threshold. | User answer Q-002 | CONFIRMED |
| F-10 | Cộng đồng bao gồm: hỏi đáp, chia sẻ đề, bình luận, nhóm học. | User answer Q-003 | CONFIRMED |
| F-11 | Phân biệt gói Free/Plus/Pro dựa trên kết hợp: lượng đề, số lần AI giải/ngày, tính năng nâng cao, thời gian lưu trữ, dung lượng. | User answer Q-004 | CONFIRMED |
| F-12 | Khi học sinh chụp đề trên lớp: lưu vào lịch sử cá nhân VÀ tự động gửi đề + câu trả lời vào kho đề để admin duyệt. | User answer Q-005 | CONFIRMED |
| F-13 | Ứng dụng Multi-platform + multi-language để hỗ trợ toàn cầu. | User answer Q-006 | CONFIRMED |
| F-14 | Kho đề KHÔNG công khai. Chỉ dành riêng cho user trả phí (Plus/Pro). | User answer Q-007 | CONFIRMED |
| F-15 | OCR nâng cao + hệ thống gợi ý metadata đề (trường, năm, môn, chương). | User answer Q-008 | CONFIRMED |
| F-16 | User sở hữu hoàn toàn dữ liệu cá nhân, có quyền export/delete. Hệ thống giữ bản sao để cải thiện AI (cần điều khoản rõ trong terms). | User answer Q-009 | CONFIRMED |
| F-17 | Community cơ bản mở hoàn toàn, chỉ giới hạn một số tính năng nâng cao theo gói Free/Plus/Pro. | User answer Q-010 | CONFIRMED |

---

## 5. Unknowns

| ID | Unknown | Ảnh hưởng | Trạng thái |
|----|---------|-----------|-----------|
| U-01 | Phân khúc lớp học / cấp học. | P0 | CONFIRMED — DEC-001: Mọi cấp học |
| U-02 | Cơ chế duyệt đề tương tự. | P0 | CONFIRMED — DEC-002: Auto-approve nếu quality score > threshold |
| U-03 | Phạm vi chi tiết community. | P1 | CONFIRMED — DEC-003: Hỏi đáp + chia sẻ đề + bình luận + nhóm học |
| U-04 | Phân biệt gói Plus/Pro cụ thể. | P0 | CONFIRMED — DEC-004: Kết hợp A+B+C |
| U-05 | Nền tảng / ngôn ngữ. | P0 | CONFIRMED — DEC-006: Multi-platform + multi-language |
| U-06 | Quyền publish đề + moderation. | P1 | CONFIRMED — DEC-007: Không có kho công khai, chỉ kho cho paid users |
| U-07 | OCR metadata & data ownership. | P1 | CONFIRMED — DEC-009: User sở hữu hoàn toàn + hệ thống giữ bản sao để improve AI |
| U-08 | Community theo gói pricing. | P1 | CONFIRMED — DEC-010: Community cơ bản mở, tính năng nâng cao gated |
| U-09 | Lưu trữ đề trên lớp (real-time capture). | P1 | CONFIRMED — DEC-005: Lưu lịch sử cá nhân + tự động gửi vào kho đề để admin duyệt |

---

## 6. Analysis Stages

| Stage | Nội dung | Phụ thuộc |
|-------|----------|-----------|
| A1 Discovery | Thu thập Problem, Need, Goal, Actors, Constraints, Scope. | — |
| A2 Deep Business Analysis | Capability, Process, Use Case, Scenario, State, Rule, Actor, Data, Event, Failure, Cross-impact. | A1 |
| A3 Feasibility | Technical, Data, Operational, Security, Dependency, Cost/Effort. | A2 (P0 resolved) |
| A4 Scope / Goals | In-scope, Out-of-scope, MVP, Success criteria, Non-goals. | A2 |
| A5 Risk / Roadmap | Risk, Dependency, Critical path, Milestone, Mitigation. | A4 |
| A6 Backlog / QA / Release | Epic, Feature, User Story, AC, Test, Priority. | A5 |

---

## 7. Domain Probes

Agent sẽ tự kiểm tra các generic pattern:
- Resource: đề thi, tài khoản user, subscription slot, storage quota.
- Workflow: chụp đề → OCR → AI xử lý → duyệt → lưu.
- Transaction: mua gói, upgrade/downgrade, refund.
- Identity / Authority: học sinh, giáo viên, admin, mod.
- Time: subscription expiry, retention policy, late submission.
- Communication: notification, community alert, reminder.
- Content / Moderation: đề thi, bình luận, nhóm học.
- External Dependency: OCR API, LLM API, payment gateway.

---

## 8. Decision Checkpoints

| Checkpoint | Nội dung | Priority |
|------------|----------|----------|
| D-01 | Phân khúc lớp + môn học ban đầu. | P0 |
| D-02 | Cơ chế duyệt đề tương tự. | P0 |
| D-03 | Phân biệt gói Plus/Pro. | P0 |
| D-04 | Nền tảng + ngôn ngữ. | P0 |
| D-05 | Phạm vi community. | P1 |
| D-06 | Quyền publish + moderation. | P1 |
| D-07 | Retention / data ownership. | P1 |
| D-08 | Community theo gói pricing. | P1 |
| D-09 | Lưu trữ đề trên lớp (real-time). | P1 |

---

## 9. Expected Artifacts

```text
specs/01-ung-dung-hoc-tap-ai/
├── histories.md
├── 00-nhat-ky-quyet-dinh.md
├── versions/
├── 01-ke-hoach.md
├── 02-bao-cao-kham-pha-yeu-cau.md
├── 03-phan-tich-sau/
│   ├── 01-nghiep_vu_tong_quat.md
│   ├── 02-cac_nghiep_vu_con/
│   │   ├── 01-ocr-va-ai-giai-de/
│   │   ├── 02-sinh-de-tuong-tu/
│   │   ├── 03-kho-de/
│   │   ├── 04-community/
│   │   ├── 05-pricing-va-subscription/
│   │   └── 06-luu-tru-de-tren-lop/
│   └── 03-tong-hop.md
└── 04-tong-hop/
    └── tong-hop-toan-bo-yeu-cau.md
```

---

## 10. Quality Gates

| Gate | Điều kiện PASS |
|------|----------------|
| G1 Discovery | Có evidence cho Problem, Need, Goal, Actors, Constraints, Scope boundary. |
| G2 Business Model | Capability, Process, UC, Scenario, State, Rule đã mô hình hóa. |
| G3 Deep Analysis | Dimensions liên quan đã phân tích, DECISION_REQUIRED đã xử lý. |
| G4 Decisions | Không còn P0/P1 BLOCKING UNRESOLVED. |
| G5 Consistency | Không có contradiction chưa giải quyết. |
| G6 Traceability | Core requirement/rule/scenario/decision không orphan. |
| G7 Delivery Ready | A3–A6 đủ evidence để sang Solution Design. |

---

## 11. Next Action

Đang ở A1 Discovery. Đã xác nhận đầy đủ 10 quyết định: DEC-001 đến DEC-010.
**Hành động tiếp theo: Chuyển sang A2 Deep Business Analysis.**

---

## 12. Plan Revision Ledger

| Thời điểm | Vị trí sửa | Nội dung cũ | Nội dung mới sau thảo luận | Lý do |
|-----------|-----------|-------------|---------------------------|-------|
| 2026-09-08 | Mục 5 Known Facts | Ban đầu chỉ có 5 facts | Thêm F-06, F-07 sau phân tích prompt User | Bổ sung chi tiết từ yêu cầu ban đầu |
| 2026-09-08 | Mục 8 Decision Checkpoints | Chưa có danh sách | Thêm D-01 đến D-09 | Liệt kê đầy đủ decision points từ A1/A2 |
| 2026-09-08 | Mục 4 Known Facts | Chưa có F-08 | Thêm F-08: App hỗ trợ mọi cấp học | DEC-001 CONFIRMED: User chọn D — Mọi cấp học |
| 2026-09-08 | Mục 5 Unknowns | U-01: Phân khúc lớp chưa rõ | Cập nhật U-01: CONFIRMED mọi cấp | DEC-001 giải quyết P0 |
| 2026-09-08 | Mục 9 Expected Artifacts | Chưa có cấu trúc chi tiết | Thêm cấu trúc 03-phan-tich-sau/ theo domain probes | Xác định rõ các nghiệp vụ con |
| 2026-09-08 | 02-bao-cao-kham-pha-yeu-cau.md | Chưa cập nhật DEC-001 | Cập nhật Goal/Actors theo DEC-001 | DEC-001 mở rộng scope sang mọi cấp học |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-09 | Thêm F-09: Auto-approve nếu quality score > threshold | DEC-002 CONFIRMED: User chọn D — Auto-approve với threshold |
| 2026-09-08 | Mục 5 Unknowns | U-02: Cơ chế duyệt chưa rõ | Cập nhật U-02: CONFIRMED auto-approve với threshold | DEC-002 giải quyết P0 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-10 | Thêm F-10: Community đầy đủ 4 tính năng | DEC-003 CONFIRMED: User chọn D — Tất cả |
| 2026-09-08 | Mục 5 Unknowns | U-03: Phạm vi community chưa rõ | Cập nhật U-03: CONFIRMED đầy đủ 4 tính năng | DEC-003 giải quyết P1 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-11 | Thêm F-11: Pricing kết hợp nhiều tiêu chí | DEC-004 CONFIRMED: User chọn D — Kết hợp A+B+C |
| 2026-09-08 | Mục 5 Unknowns | U-04: Phân biệt gói chưa rõ | Cập nhật U-04: CONFIRMED kết hợp A+B+C | DEC-004 giải quyết P0 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-12 | Thêm F-12: In-class capture lưu lịch sử + gửi vào kho đề | DEC-005 CONFIRMED: User chọn B + điều kiện bổ sung |
| 2026-09-08 | Mục 5 Unknowns | U-09: Lưu trữ đề trên lớp chưa rõ | Cập nhật U-09: CONFIRMED lịch sử + admin review | DEC-005 giải quyết P1 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-15 | Thêm F-15: OCR nâng cao + gợi ý metadata | DEC-008 CONFIRMED: User chọn B |
| 2026-09-08 | Mục 5 Unknowns | U-07: OCR metadata chưa rõ | Cập nhật U-07: CONFIRMED OCR nâng cao + gợi ý metadata | DEC-008 giải quyết P1 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-16 | Thêm F-16: User sở hữu hoàn toàn + hệ thống giữ bản sao | DEC-009 CONFIRMED: User chọn A+B |
| 2026-09-08 | Mục 5 Unknowns | U-07: Data ownership chưa rõ | Cập nhật U-07: CONFIRMED A+B | DEC-009 giải quyết P1 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-17 | Thêm F-17: Community cơ bản mở, advanced gated | DEC-010 CONFIRMED: User chọn A+C |
| 2026-09-08 | Mục 5 Unknowns | U-08: Community theo gói chưa rõ | Cập nhật U-08: CONFIRMED cơ bản mở, advanced gated | DEC-010 giải quyết P1 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-13 | Thêm F-13: Multi-platform + multi-language | DEC-006 CONFIRMED: User chọn D — Toàn cầu |
| 2026-09-08 | Mục 5 Unknowns | U-05: Nền tảng/ngôn ngữ chưa rõ | Cập nhật U-05: CONFIRMED multi-platform + multi-language | DEC-006 giải quyết P0 |
| 2026-09-08 | Mục 5 Known Facts | Chưa có F-14 | Thêm F-14: Kho đề chỉ cho paid users, không công khai | DEC-007 CONFIRMED: User bác bỏ kho công khai |
| 2026-09-08 | Mục 5 Unknowns | U-06: Quyền publish + moderation chưa rõ | Cập nhật U-06: CONFIRMED không có kho công khai | DEC-007 giải quyết P1 |

---


