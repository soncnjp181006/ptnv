# Quy Tắc Vận Hành Đồ Thị DAG Toán Học (DAG Rules & State Engine)

Tài liệu này là **Quy chuẩn toán học và thuật toán (Mathematical & Algorithmic Specification)** định nghĩa các quy tắc chuyển trạng thái, điều kiện kích hoạt, rẽ nhánh song song, hợp lưu và quay lui khắc phục sự cố cho Đồ thị 65 chặng kỹ thuật của Extension Bundle `fullstack`.

---

## 🧭 1. Mô Hình Toán Học Của Đồ Thị SDLC

Quy trình phát triển phần mềm toàn diện được mô hình hóa thành một **Đồ thị Hướng có Chu trình Phản hồi (Directed Cyclic Graph - DCG)**:

$$\mathcal{G} = (\mathcal{V}, \mathcal{E})$$

Trong đó:
- **Tập hợp 65 đỉnh (Vertices)**:
  $$\mathcal{V} = \{A_1, \dots, A_6, B_1, \dots, B_8, C_1, \dots, C_7, D_1, \dots, D_9, E_1, \dots, E_9, F_1, \dots, F_{10}, G_1, \dots, G_8, H_1, \dots, H_8\}$$
- **Tập hợp các cạnh có hướng (Directed Edges)**:
  $$\mathcal{E} \subset \mathcal{V} \times \mathcal{V}$$
  Cạnh $(u, v) \in \mathcal{E}$ thể hiện rằng chặng kỹ thuật $u$ là **tiền đề bắt buộc (Dependency)** của chặng kỹ thuật $v$. Chặng $v$ không được phép bắt đầu trước khi $u$ đã hoàn thành và nghiệm thu đạt chuẩn.
- **Vòng lặp tiến hóa khép kín (The Closed-Loop Feedback Edge)**:
  $$(H_8, A_2) \in \mathcal{E}$$
  Cạnh ngược nối từ chặng $H_8$ (Yêu cầu cải tiến mới) quay trực tiếp về $A_2$ (Phân tích Nghiệp vụ) giúp toàn bộ vòng đời phát triển trở thành một chu trình tự tiến hóa không có điểm dừng chết.

---

## 📊 2. Không Gian Trạng Thái Của Chặng (Node State Space)

Mỗi chặng kỹ thuật $v \in \mathcal{V}$ tại thời điểm $t$ luôn tồn tại ở duy nhất một trong 6 trạng thái:

$$\mathcal{S}(v, t) \in \{\text{BLOCKED}, \text{PENDING}, \text{READY}, \text{IN\_PROGRESS}, \text{COMPLETED}, \text{FAILED}\}$$

```
                ┌──────────────┐
                │   BLOCKED    │ ◄── Chưa thỏa mãn điều kiện tiên quyết
                └──────┬───────┘
                       │ 100% Inbound Dependencies == COMPLETED
                       ▼
                ┌──────────────┐
                │    READY     │ ◄── Sẵn sàng để Master Skill tiếp nhận
                └──────┬───────┘
                       │ Master Skill nạp stage module tương ứng
                       ▼
                ┌──────────────┐
     ┌─────────►│ IN_PROGRESS  │ ◄── Agent đang viết spec, code hoặc test
     │          └──────┬───────┘
     │                 │
     │        ┌────────┴────────┐
     │        │                 │
     │ DoD Gate PASS       DoD Gate FAIL
     │        │                 │
     │        ▼                 ▼
     │  ┌───────────┐     ┌───────────┐
     │  │ COMPLETED │     │  FAILED   │
     │  └───────────┘     └─────┬─────┘
     │                          │
     └────── Quay lui sửa ──────┘
```

### Ý Nghĩa Các Trạng Thái:
1. **`BLOCKED`**: Chặng chưa thể kích hoạt do còn ít nhất một chặng tiền đề chưa hoàn thành.
2. **`READY`**: Toàn bộ các chặng tiền đề đã hoàn tất nghiệm thu, tài nguyên đầu vào đã sẵn sàng.
3. **`IN_PROGRESS`**: Master Skill phụ trách đã nạp module `stages/<ID>-<name>.md` vào ngữ cảnh và Agent đang tích cực xử lý.
4. **`COMPLETED`**: Toàn bộ sản phẩm bàn giao (Artifacts) đã được tạo và 100% tiêu chí nghiệm thu DoD Gate đã được tích chọn pass (`[x]`).
5. **`FAILED`**: Chặng không vượt qua bài kiểm tra chất lượng (ví dụ: Unit Test fail, Security Scan phát hiện Critical CVE, Smoke Test timeout).

---

## 🔒 3. Điều Kiện Kích Hoạt & Nghiệm Thu (Activation & Completion Gates)

### 3.1. Thuật Toán Kiểm Tra Kích Hoạt `CanActivate(v)`

Một chặng $v$ chỉ được phép chuyển từ `BLOCKED` sang `READY` hoặc `IN_PROGRESS` khi và chỉ khi thỏa mãn biểu thức logic:

$$\text{CanActivate}(v) \iff \forall u \in \text{Inbound}(v): \mathcal{S}(u) = \text{COMPLETED}$$

Trong đó $\text{Inbound}(v) = \{u \in \mathcal{V} \mid (u, v) \in \mathcal{E}\}$ là tập hợp các chặng phụ thuộc trực tiếp của $v$.

#### Các Ngoại Lệ Được Định Nghĩa:
- **Khởi đầu Chu kỳ 1 ($A_1$)**:
  $$\text{Inbound}(A_1) = \emptyset \implies \text{CanActivate}(A_1) = \text{True}$$
- **Điểm Hợp lưu Khởi động Chu kỳ 2+ ($A_2$)**:
  Chặng $A_2$ có thể kích hoạt theo 2 trường hợp:
  - Chu kỳ 1: $\mathcal{S}(A_1) = \text{COMPLETED}$.
  - Chu kỳ 2 trở đi: $\mathcal{S}(H_8) = \text{COMPLETED}$.

---

### 3.2. Thuật Toán Kiểm Tra Nghiệm Thu `CanComplete(v)`

Một chặng $v$ chỉ được phép chuyển từ `IN_PROGRESS` sang `COMPLETED` khi thỏa mãn đồng thời các điều kiện:

$$\text{CanComplete}(v) \iff \text{ArtifactExists}(v) \land \text{DoDChecked}(v) \land \text{ZeroCriticalFlag}(v) \land \text{ZeroSuperficialFlag}(v) \land \text{MinimumDecompositionMet}(v) \land \text{HumanSignOffIfPhaseA}(v)$$

Trong đó:
1. **`ArtifactExists(v)`**: Tệp sản phẩm bàn giao bắt buộc (ví dụ `specs/data/B5-erd-db.md` hoặc `migrations/001_init.sql`) phải tồn tại thực tế trên ổ đĩa và không được rỗng.
2. **`DoDChecked(v)`**: Toàn bộ các tiêu chí trong bảng checklist nghiệm thu của file `stages/<ID>-<name>.md` đều đã được kiểm tra và đánh dấu pass (`[x]`).
3. **`ZeroCriticalFlag(v)`**: Không còn bất kỳ cờ cảnh báo rủi ro mức độ Critical hoặc Blocker nào chưa được xử lý.
4. **`ZeroSuperficialFlag(v)`** *(Tiêu Chuẩn Chống Làm Qua Loa & Chống Slop)*:
   - Nghiêm cấm tài liệu sơ sài chỉ có vài gạch đầu dòng hời hợt hoặc chỉ có logic CRUD ngây thơ ("Nhập ➔ Check ➔ Lưu DB").
   - Mọi phân tích nghiệp vụ tại $A_1, A_2$ bắt buộc phải bao quát đủ **7 Trục Chiều Sâu Thực Chiến**:
     1. Multi-Actor & Handoffs (Phân vai tác nhân & luồng chuyển giao)
     2. State Machine & Transition Rules (Vòng đời trạng thái đối tượng)
     3. Data Consistency, Locking & Idempotency (Khóa dữ liệu, chống trùng lặp, Reserve/Commit)
     4. Offline-First & Degraded Modes (Mất mạng, timeout, sự cố thiết bị ngoại vi)
     5. Dual-Approval & Security Controls (Mã PIN quản lý, cách ly chi nhánh RLS, Audit Trail)
     6. Edge Cases, Race Conditions & Wastage Tracking (Tranh chấp tài nguyên, huỷ món hỏng)
     7. Financial/Audit Reconciliation & Variance Analysis (Đối soát tiền/kho, phân tích chênh lệch).
5. **`MinimumDecompositionMet(v)`** *(Bắt Buộc Phân Rã Chi Tiết [FORM D])*:
   - Đối với các chặng $A_1, A_2$: Mỗi cụm nghiệp vụ lớn trong `phan-tich/<XX>-<cum-nghiep-vu>/` **BẮT BUỘC PHẢI CHỨA TỐI THIỂU TỪ 2 ĐẾN 5 TỆP CHI TIẾT `<ZZ>-*.md` theo [FORM D]**.
   - CẤM folder chỉ có duy nhất 1 file `tong-hop.md`. Bất kỳ cụm nghiệp vụ nào thiếu file chi tiết [FORM D] thì $\text{MinimumDecompositionMet}(v) = \text{False}$ và bị chặn nghiệm thu.
6. **`HumanSignOffIfPhaseA(v)`** *(Quy tắc Bất Biến Phân Hệ A - Cấm Tự Duyệt)*:
   Đối với các chặng thuộc Phân hệ Discovery & Planning ($v \in \{A_1, A_2, A_3, A_4, A_5, A_6\}$):
   $$\text{HumanSignOffIfPhaseA}(v) \iff \text{FlowchartRendered}(v) \land \text{InteractiveInterviewCompleted}(v) \land \text{UserApproved}(v)$$
   - **`FlowchartRendered(v)`**: Bắt buộc có sơ đồ luồng Mermaid Flowchart trực quan hóa quy trình để cả User và AI cùng nhìn thấy logic nghiệp vụ.
   - **`InteractiveInterviewCompleted(v)`**: Bắt buộc Agent phải dừng lại ở Interactive Interview Gate để gửi câu hỏi làm rõ / ma trận đánh đổi và chờ User phản hồi trong chat.
   - **`UserApproved(v)`**: Bắt buộc có sự xác nhận / ký duyệt bằng văn bản thực tế từ Người dùng (User/PO) trong đoạn chat. **TUYỆT ĐỐI CẤM AI tự ý tick `[x]` hoặc tự chuyển trạng thái hoàn thành các chặng A nếu thiếu sự đồng thuận thực tế của User**, nhằm ngăn chặn triệt để nguy cơ mất phương hướng nghiệp vụ.

---

## 🔀 4. Cơ Chế Rẽ Nhánh Song Song (Fork) & Hợp Lưu Đồng Bộ (Join)

Để tối ưu hóa thời gian thực thi, đồ thị hỗ trợ xử lý song song có kiểm soát:

```
                  ┌──────────────┐
                  │ A6: Backlog  │
                  └──────┬───────┘
                         │ Fork (Rẽ nhánh song song)
            ┌────────────┴────────────┐
            ▼                         ▼
     ┌──────────────┐          ┌──────────────┐
     │ B1: UX Flow  │          │ B3: Arch C4  │
     └──────┬───────┘          └──────┬───────┘
            ▼                         ▼
     ┌──────────────┐          ┌──────────────┐
     │ B2: UI Wire  │          │ B4: Domain   │
     └──────┬───────┘          └──────┬───────┘
            │                  ┌──────┴──────┐
            │                  ▼             ▼
            │            ┌───────────┐ ┌───────────┐
            │            │  B5: ERD  │ │  B6: API  │
            │            └─────┬─────┘ └─────┬─────┘
            │                  │             │
            └────────────┬─────┴─────────────┘
                         │ Join (Hợp lưu đồng bộ 3 luồng)
                         ▼
                  ┌──────────────┐
                  │   C1: Repo   │
                  └──────────────┘
```

### 4.1. Quy Tắc Rẽ Nhánh Song Song (Fork Rules):
1. **Fork 1 (Phân hệ B)**: Từ $A_6$ (Backlog) đồng thời kích hoạt 2 luồng độc lập:
   - Luồng Sản phẩm & Trải nghiệm: $B_1 \to B_2$.
   - Luồng Kỹ thuật & Kiến trúc: $B_3 \to B_4 \to (B_5, B_6)$ và $B_3 \to B_7 \to B_8$.
2. **Fork 2 (Phân hệ D)**: Từ $C_7$ (CI Pipeline) đồng thời kích hoạt 4 luồng lập trình:
   - Lập trình Backend ($D_1$).
   - Lập trình Frontend ($D_2$).
   - Script Database Migration ($D_3$).
   - Adapter Dịch vụ bên thứ ba ($D_4$).
3. **Fork 3 (Phân hệ G)**: Từ $G_2$ (Infrastructure) đồng thời kích hoạt 3 luồng giám sát:
   - Centralized Logs ($G_5$).
   - Prometheus Metrics ($G_6$).
   - OpenTelemetry Tracing ($G_7$).

### 4.2. Quy Tắc Hợp Lưu Đồng Bộ (Join Rules):
1. **Join 1 ($C_1: Repository Init$)**:
   $$\text{Inbound}(C_1) = \{B_2, B_5, B_6\}$$
   *Ý nghĩa*: Không được khởi tạo repository khi chưa có cả Wireframe UI ($B_2$), Schema Cơ sở dữ liệu ($B_5$) và API Contract ($B_6$).
2. **Join 2 ($D_5: System Integration$)**:
   $$\text{Inbound}(D_5) = \{D_1, D_2, D_3, D_4\}$$
   *Ý nghĩa*: Không được thực hiện tích hợp hệ thống khi Backend, Frontend, Migration hoặc External Adapters chưa hoàn tất mã nguồn độc lập.
3. **Join 3 ($G_8: Alerting Rules$)**:
   $$\text{Inbound}(G_8) = \{G_5, G_6, G_7\}$$
   *Ý nghĩa*: Chỉ cấu hình ngưỡng cảnh báo khi cả 3 nguồn telemetry (Logs, Metrics, Traces) đã thông suốt.

---

## 🔄 5. Quy Tắc Quay Lui Khắc Phục Sự Cố (Rollback Loops)

Khi một chặng kỹ thuật gặp lỗi và chuyển sang trạng thái `FAILED`, hệ thống **tuyệt đối không cho phép bỏ qua (Bypass)**, mà kích hoạt một trong các **Vòng Lặp Quay Lui (Rollback Loops)** được định nghĩa trước:

| Vị Trí Phát Hiện Lỗi | Chặng Bị Fail | Điểm Quay Lui Bắt Buộc | Hành Động Khắc Phục Cần Thực Hiện |
| :--- | :---: | :---: | :--- |
| **Lint / Typecheck** | $E_1$ | $D_1$ hoặc $D_2$ | Sửa lỗi cú pháp, định dạng hoặc xung đột kiểu dữ liệu, commit lại tại $D_6$. |
| **Unit Test** | $E_2$ | $D_1$ hoặc $D_2$ | Bổ sung xử lý edge cases, sửa logic hàm, chạy lại $E_2$ cho đến khi 100% Pass. |
| **Integration Test** | $E_3$ | $D_5$ | Sửa kết nối giữa API và Database, đồng bộ tham số truyền giữa Controller và Service. |
| **Contract Test** | $E_4$ | $B_6$ hoặc $D_1$ | Xử lý vi phạm tương thích ngược (Breaking Change) trong OpenAPI Spec hoặc DTO. |
| **E2E Test** | $E_5$ | $D_2$ hoặc $D_5$ | Sửa lỗi luồng trải nghiệm trên UI, đồng bộ trạng thái giữa Frontend và Backend. |
| **Security Scan** | $E_6$ | $C_5$ hoặc $B_7$ | Nâng cấp thư viện dính CVE, mã hóa lại biến môi trường hoặc cấu hình lại phân quyền. |
| **Performance Test** | $E_7$ | $B_5$ hoặc $D_1$ | Đánh thêm Index cơ sở dữ liệu, tối ưu câu truy vấn N+1, bổ sung bộ nhớ đệm Cache. |
| **UAT Sign-off** | $E_8$ | $A_6$ hoặc $D_1$ | Làm rõ lại User Story chưa đạt kỳ vọng của PO, điều chỉnh lại tính năng. |
| **Staging Smoke Test**| $F_8$ | $F_6$ hoặc $C_4$ | Sửa sai lệch biến môi trường giữa Dev và Staging (Environment Parity). |
| **Production Incident**| $H_1$ | $H_3 \to F_9$ | Kích hoạt Rollback khẩn cấp về version trước hoặc triển khai Hotfix an toàn. |

---

## 🗄️ 6. Tính Nhất Quán Nguyên Tử Của Sổ Cái (Atomic State Mutation)

Mọi biến động trạng thái trên đồ thị DAG đều được ghi nhận nguyên tử vào tệp:
`.agent-system/memory/fullstack/lifecycle_state.json`

### Quy Tắc Ghi Nhận:
1. **Tính Bất Biến Lịch Sử (Append-only Audit Log)**:
   Mọi chuyển đổi trạng thái đều phải lưu lại lịch sử `transitions` gồm `from_state`, `to_state`, `timestamp`, `triggered_by` và `evidence_path`.
2. **Khóa Đơn Tiến Trình (Single Mutex Gate)**:
   Không cho phép hai tiến trình ghi đè đồng thời vào `lifecycle_state.json`.

---

## 📚 Tài Liệu Liên Quan (Fullstack SDLC Suite)

- [Kiến Trúc Tổng Quan (Architecture)](../../../../docs/fullstack/architecture.md)
- [Cấu Trúc Cài Đặt & Thư Mục (Installation & Storage)](../../../../docs/fullstack/installation-and-storage.md)
- [Đặc Tả Đồ Thị DAG 65 Chặng (DAG Specification)](../../../../docs/fullstack/dag-specification.md)
- [Quy Tắc Đồ Thị DAG Toán Học (DAG Rules)](../../../../docs/fullstack/dag-rules.md)
- [Đặc Tả Vòng Đời 65 Chặng (Lifecycle Specification)](../../../../docs/fullstack/lifecycle.md)
- [Danh Mục 9 Master Skills & 65 Modules (Skills Catalog)](../../../../docs/fullstack/skills-catalog.md)
- [Hướng Dẫn Điều Phối Đa Nền Tảng (Host Routing Guide)](../../../../docs/fullstack/host-routing-guide.md)
- [Quản Trị Phiên Bản & Nâng/Hạ Version (Version Management)](../../../../docs/fullstack/version-management.md)
- [Kiến Trúc Framework Chung (Framework Architecture)](../../../../docs/architecture.md)
