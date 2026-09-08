# 01 - Kế hoạch phân tích - App nhà hàng

> Artifact: `01-ke-hoach.md`
> Stage: A1 Discovery (Plan-First Protocol)
> Status: DRAFT
> Version: v1 - 2026-09-08

## 1. Mục tiêu

Biến yêu cầu mơ hồ "làm app nhà hàng" thành mô hình nghiệp vụ có thể kiểm chứng, đủ rõ để chuyển sang A2 (phân tích sâu) mà không phải đoán policy cốt lõi.

Function: `extractFacts()`, `discoverCapabilities()`, `elicitDecisions()`
Variable: `businessContext`, `capabilityCandidates`, `decisionLog`

Không thiết kế solution sớm. Không chốt business rule khi chưa có bằng chứng.

## 2. Phạm vi

### Trong phạm vi A1

- Làm rõ Problem / Need / Desired outcome / Business goal / Success metric / Constraint / Non-goal.
- Xác định Actor sơ bộ và Capability Candidates.
- Xác định Scope boundary sơ bộ (In scope / Out of scope / Non-goal).
- Thu thập FACT từ User, gắn trạng thái Evidence cho mọi phát hiện.

### Ngoài phạm vi A1

- Thiết kế database, API, kiến trúc kỹ thuật.
- Viết code, chọn framework cụ thể.
- Chốt Use Case chi tiết, State machine, Business rule cuối cùng (thuộc A2).

## 3. Dữ liệu đầu vào

| ID | Nội dung | Nguồn | Trạng thái |
|---|---|---|---|
| FACT-001 | User muốn làm app nhà hàng | User statement 2026-09-08 | `FACT` |
| OPEN-001 | Chưa rõ app phục vụ ai, giải quyết vấn đề gì | Thiếu thông tin | `UNRESOLVED` |
| OPEN-002 | Chưa rõ mô hình vận hành (1 quán / chuỗi / mang về / đặt bàn) | Thiếu thông tin | `UNRESOLVED` |
| OPEN-003 | Chưa rõ nghiệp vụ cốt lõi (bán hàng / order / quản lý / khách hàng) | Thiếu thông tin | `UNRESOLVED` |
| OPEN-004 | Chưa rõ platform, constraint, mục tiêu thành công | Thiếu thông tin | `UNRESOLVED` |

## 4. Điều đã biết (Known facts)

- Chỉ có 1 FACT duy nhất: ý định làm app cho lĩnh vực nhà hàng.
- Workspace hiện tại trống, chưa có artifact nghiệp vụ hay kỹ thuật nào.
- Chưa có domain playbook nào được kích hoạt. Mọi khái niệm nhà hàng do Agent suy luận phải gắn `PROPOSED / Source: AGENT_INFERENCE`, không được coi là policy.

## 5. Điều chưa rõ (Unknowns - P0 trước)

1. WHO: Ai dùng app? Chủ quán, thu ngân, phục vụ, bếp, khách hàng, quản lý chuỗi?
2. WHY: Vấn đề đau nhất cần giải quyết? Order chậm, thất thoát, quản lý kho, đặt bàn, thanh toán?
3. WHAT: Capability nào là cốt lõi? Menu, Order, Bàn, Thanh toán, Kho, Nhân sự, Khách hàng thân thiết, Báo cáo?
4. WHERE: Ranh giới vận hành? 1 cửa hàng hay chuỗi? Ăn tại chỗ / mang về / giao hàng?
5. WHEN/TIME: Yếu tố thời gian có quan trọng không? Giờ cao điểm, đặt trước, giữ bàn?
6. MONEY: Có xử lý tiền không? Thanh toán tại quầy, chuyển khoản, ví, tách hóa đơn, giảm giá, thuế?
7. CONSTRAINT: Platform (web/mobile), ngân sách, thời gian, hệ thống sẵn có (KDS, POS, hóa đơn điện tử)?

## 6. Các bước phân tích

```text
A1 Discovery
→ Thu thập FACT / OPEN (hiện tại)
→ Hỏi P0 theo batch (Intent → Capability → Scope)
→ User xác nhận
→ Sinh 02-bao-cao-kham-pha-yeu-cau.md
→ Sinh capability sơ bộ
→ Gate G1 review → User sign-off
→ Mới sang A2

A2 ... A6 chỉ khởi động khi G1 PASS và có User sign-off.
```

## 7. Các domain probe (chỉ là câu hỏi, chưa phải policy)

Theo Core Engine §9, các probe sau CẦN được kiểm chứng, KHÔNG được mặc định là tồn tại:

- Resource pattern: bàn/ghế có phải tài nguyên hữu hạn cần giữ/chống trùng không? Status: `PROPOSED / AGENT_INFERENCE`.
- Workflow pattern: order có nhiều bước duyệt (phục vụ → bếp → thu ngân) không?
- Transaction pattern: thanh toán có cần idempotency, đảo (reversal), đối soát không?
- Money pattern: giá, phí, giảm giá, thuế, tách/gộp hóa đơn?
- Inventory pattern: nguyên liệu / món có hết món (sold-out), trừ kho không?
- Identity pattern: phân quyền thu ngân / bếp / quản lý, chống gian lận?
- Time pattern: giữ bàn bao lâu, hết hạn, giờ cao điểm?
- Communication pattern: thông báo bếp, gọi món, hóa đơn cho khách?
- External pattern: tích hợp giao hàng, thanh toán ngoài, hóa đơn điện tử, kế toán?

Mỗi probe → Decision Request → User Confirm/Reject → mới thành rule.

## 8. Các checkpoint cần User quyết định

- Checkpoint C1 (P0): Mô hình app + người dùng chính + vấn đề cốt lõi.
- Checkpoint C2 (P0): 3-5 capability cốt lõi cho MVP.
- Checkpoint C3 (P0): Ranh giới MVP (In scope / Out of scope / Non-goal).
- Checkpoint C4 (P1): Ràng buộc platform, hệ thống sẵn có, mục tiêu đo lường.

## 9. Artifact dự kiến

Chỉ sinh khi C1-C3 được chốt:

```text
01-ke-hoach.md (hiện tại)
02-bao-cao-kham-pha-yeu-cau.md
04-mo-hinh-kha-nang-nghiep-vu.md (sơ bộ)
10-danh-sach-quyet-dinh.md (sơ bộ)
16-pham-vi-va-muc-tieu.md (sơ bộ)
```

Không sinh 05-09 (process/use-case/scenario/state/rule chi tiết) trong A1.

## 10. Quality gate áp dụng

- G1 Discovery Complete: có Problem, Need, Goal, Actors, Constraints, Scope boundary.
- G4 Decision Complete: không còn P0 UNRESOLVED thuộc core.
- G5 Consistency: không mâu thuẫn thuật ngữ/quyền hạn/phạm vi.
- Hiện tại: G1 = FAIL (thiếu 5/6 mục), G4 = FAIL (4 nhóm P0 UNRESOLVED). Chặn sang A2.

## 11. Bước tiếp theo

Hỏi batch P0 đầu tiên (Intent & Capability). Sau khi User trả lời, cập nhật model thay vì chỉ ghi log, rồi hỏi batch P0 tiếp theo về Scope/Money/Time.
